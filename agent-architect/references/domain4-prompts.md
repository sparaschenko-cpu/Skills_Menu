# Domain 4 — Prompt Engineering и структурированный вывод

## Содержание
1. [Системный промпт](#системный-промпт)
2. [Few-shot примеры](#few-shot)
3. [Структурированный вывод через tool_use](#structured-output)
4. [Validation-Retry Loop](#validation-retry)
5. [Batch Processing](#batch)
6. [Многопроходное ревью](#ревью)

---

## Системный промпт

```python
SYSTEM_PROMPT = """
Ты — агент поддержки клиентов. Задача: решать обращения клиентов используя
инструменты get_customer, lookup_order, process_refund, escalate_to_human.

## Критерии успеха (явные и измеримые)
1. Верифицируй клиента через get_customer ПЕРЕД любыми операциями с заказами
2. Возврат обрабатывается только при наличии подтверждённого customer_id
3. Эскалируй к человеку при: явном запросе клиента, сумме > 500, исключении из политики

## Процесс работы
1. Получи описание проблемы от клиента
2. Вызови get_customer для верификации (email или phone)
3. Если клиент не найден — запроси дополнительные идентификаторы
4. Вызови lookup_order если проблема связана с заказом
5. Реши проблему или эскалируй согласно критериям выше

## Запрещено
- Никогда не вызывай process_refund без предварительного get_customer
- Не выбирай клиента эвристически при множественных совпадениях — запрашивай уточнение
- Не пытайся решить самостоятельно если клиент явно просит человека

## Формат ответа клиенту
Краткий, конкретный, без технических деталей о внутренних инструментах.
"""
```

**Принципы системного промпта:**
- Явные **измеримые** критерии вместо "будь хорошим" или "будь консервативным"
- Конкретные триггеры эскалации — не "когда сложно", а "сумма > 500"
- Запреты формулируй как "Никогда X без Y", а не абстрактно

---

## Few-shot

Few-shot примеры — наиболее эффективная техника когда детальные инструкции дают нестабильный формат.

```python
FEW_SHOT_EXAMPLES = [
    # Пример 1: стандартный случай ревью
    {
        "role": "user",
        "content": """
Проверь этот код:
def calculate_discount(price, discount_pct):
    return price - (price * discount_pct / 100)
"""
    },
    {
        "role": "assistant",
        "content": """
{
  "issues": [],
  "verdict": "approved",
  "notes": "Корректная логика. Рассмотри добавление проверки на отрицательные значения если нужна валидация входных данных."
}
"""
    },
    # Пример 2: реальная проблема
    {
        "role": "user",
        "content": """
Проверь этот код:
def get_user(user_id):
    query = f"SELECT * FROM users WHERE id = {user_id}"
    return db.execute(query)
"""
    },
    {
        "role": "assistant",
        "content": """
{
  "issues": [{
    "file": "users.py",
    "line": 2,
    "severity": "critical",
    "category": "security",
    "description": "SQL injection уязвимость: user_id подставляется в запрос без параметризации",
    "fix": "query = 'SELECT * FROM users WHERE id = %s'; db.execute(query, (user_id,))"
  }],
  "verdict": "changes_required"
}
"""
    }
]

# Включай examples в messages ДО реального запроса
messages = FEW_SHOT_EXAMPLES + [{"role": "user", "content": actual_request}]
```

**Когда добавлять few-shot:**
- Нестабильный формат вывода несмотря на детальные инструкции
- Неоднозначные случаи требуют демонстрации правильного решения
- Задачи извлечения данных с вариативным форматированием
- 2-4 примера достаточно — больше не всегда лучше

---

## Structured Output

```python
# JSON Schema для инструмента извлечения
EXTRACTION_SCHEMA = {
    "name": "extract_invoice_data",
    "description": "Извлеки структурированные данные из счёта",
    "input_schema": {
        "type": "object",
        "properties": {
            "invoice_number": {
                "type": "string",
                "description": "Номер счёта. Формат: INV-XXXXXX"
            },
            "total_amount": {
                "type": "number",
                "description": "Итоговая сумма в рублях"
            },
            "vendor_name": {
                "type": ["string", "null"],  # nullable — может отсутствовать
                "description": "Название поставщика или null если не указан"
            },
            "line_items": {
                "type": "array",
                "items": {
                    "type": "object",
                    "properties": {
                        "description": {"type": "string"},
                        "quantity": {"type": "number"},
                        "unit_price": {"type": "number"}
                    },
                    "required": ["description", "quantity", "unit_price"]
                }
            },
            "payment_status": {
                "type": "string",
                "enum": ["paid", "pending", "overdue", "unclear", "other"],
                # "unclear" — для неоднозначных случаев
                # "other" — для нестандартных статусов
                "description": "Статус оплаты. Используй 'unclear' если невозможно определить."
            },
            "payment_status_details": {
                "type": ["string", "null"],
                "description": "Дополнительное описание если status = 'other' или 'unclear'"
            }
        },
        "required": ["invoice_number", "total_amount", "line_items", "payment_status"]
    }
}

# Принудительный вызов инструмента — гарантированный структурированный вывод
response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=4096,
    tools=[EXTRACTION_SCHEMA],
    tool_choice={"type": "tool", "name": "extract_invoice_data"},
    messages=[{"role": "user", "content": f"Извлеки данные:\n{document}"}]
)

# Получить результат
for block in response.content:
    if block.type == "tool_use" and block.name == "extract_invoice_data":
        extracted_data = block.input
```

**Ограничения tool_use:** устраняет синтаксические ошибки JSON, но не семантические. Проверяй:
- Суммы сходятся (calculated_total == stated_total)
- Значения в правильных полях
- Даты в правильном формате

---

## Validation-Retry

```python
MAX_RETRIES = 3

async def validated_extraction(client, document: str, validator) -> dict:
    messages = [{"role": "user", "content": f"Извлеки данные:\n{document}"}]

    for attempt in range(MAX_RETRIES):
        response = await client.messages.create(
            tools=[EXTRACTION_SCHEMA],
            tool_choice={"type": "tool", "name": "extract_invoice_data"},
            messages=messages
        )
        output = extract_tool_input(response)
        errors = validator(output)

        if not errors:
            return output

        # КРИТИЧНО: передавай конкретные ошибки, не просто "неверно"
        messages.append({"role": "assistant", "content": response.content})
        messages.append({
            "role": "user",
            "content": f"""
Результат содержит ошибки. Конкретные проблемы:
{json.dumps(errors, ensure_ascii=False, indent=2)}

Исправь только указанные ошибки. Остальные поля не меняй.
"""
        })

    raise ValidationError(f"Не удалось получить валидный результат за {MAX_RETRIES} попыток")


def validate_invoice(data: dict) -> list[str]:
    errors = []

    # Семантическая проверка — суммы сходятся
    if "line_items" in data and "total_amount" in data:
        calc_total = sum(
            item["quantity"] * item["unit_price"]
            for item in data["line_items"]
        )
        if abs(calc_total - data["total_amount"]) > 0.01:
            errors.append(
                f"Сумма строк ({calc_total:.2f}) не совпадает с итоговой ({data['total_amount']:.2f})"
            )

    return errors
```

**Когда retry эффективен:** ошибки формата, структуры, неверно заполненные поля.
**Когда retry бесполезен:** информация отсутствует в документе — нужен внешний источник.

---

## Batch

```python
# Message Batches API — экономия ~50%, обработка до 24 часов

# Используй когда:
# ✓ > 10 независимых запросов
# ✓ Нет требования к реальному времени (ночные отчёты, еженедельные аудиты)
# ✗ НЕ для блокирующих pre-merge проверок
# ✗ НЕ поддерживает многоходовые вызовы инструментов в одном запросе

requests = [
    {
        "custom_id": f"invoice-{invoice_id}",  # для корреляции результатов
        "params": {
            "model": "claude-opus-4-6",
            "max_tokens": 2048,
            "tools": [EXTRACTION_SCHEMA],
            "tool_choice": {"type": "tool", "name": "extract_invoice_data"},
            "messages": [{"role": "user", "content": f"Извлеки:\n{doc}"}]
        }
    }
    for invoice_id, doc in invoices.items()
]

batch = client.beta.messages.batches.create(requests=requests)

# Обработка результатов — повторная отправка только неудачных по custom_id
async def process_batch_results(batch_id: str):
    results = {}
    failed = []

    async for result in client.beta.messages.batches.results(batch_id):
        if result.result.type == "succeeded":
            results[result.custom_id] = extract_tool_input(result.result.message)
        else:
            failed.append(result.custom_id)

    return results, failed
```

---

## Ревью

**Ограничение самопроверки:** модель сохраняет контекст рассуждений от генерации — меньше вероятность оспорить собственные решения.

```python
# Многопроходное ревью для больших PR (> 5 файлов)

# Проход 1: пофайловый анализ
file_reviews = {}
for file_path, diff in changed_files.items():
    review = await claude.review_file(diff, context=project_context)
    file_reviews[file_path] = review

# Проход 2: кросс-файловый интеграционный анализ
integration_review = await claude.review_integration(
    file_reviews=file_reviews,
    focus="data_flow_between_files, api_contract_consistency, shared_state_issues"
)

# Независимый инстанс для критического ревью
# Новая сессия БЕЗ контекста генерации кода
independent_review = await new_claude_session.review(
    code=generated_code,
    instructions="Найди баги и уязвимости. Не смотри на историю генерации."
)
```

**Паттерн верификационного прохода:**
```python
# Добавить уверенность к каждой находке для фильтрации шума
verification_prompt = """
Для каждой найденной проблемы укажи:
- confidence: high/medium/low
- evidence: конкретная строка кода доказывающая проблему
- false_positive_risk: причина почему это может быть ложным срабатыванием
"""
```
