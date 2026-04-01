# Domain 1 — Агентная архитектура и оркестрация

## Содержание
1. [Полный агентный цикл](#агентный-цикл)
2. [Coordinator–Subagent паттерн](#coordinator-subagent)
3. [Хуки PostToolUse](#хуки)
4. [Управление сессиями](#сессии)
5. [Декомпозиция задач](#декомпозиция)

---

## Агентный цикл

Полная реализация с обработкой всех значений `stop_reason`:

```python
async def agent_loop(client, messages, tools, max_iterations=50):
    for i in range(max_iterations):
        response = await client.messages.create(
            model="claude-opus-4-6",
            max_tokens=8096,
            tools=tools,
            messages=messages
        )

        if response.stop_reason == "end_turn":
            # Агент завершил — вернуть финальный текст
            return extract_text(response)

        elif response.stop_reason == "tool_use":
            # Выполнить все запрошенные инструменты
            tool_results = []
            for block in response.content:
                if block.type == "tool_use":
                    result = await execute_tool(block.name, block.input)
                    tool_results.append({
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": json.dumps(result)
                    })
            # Добавить ответ ассистента и результаты в историю
            messages.append({"role": "assistant", "content": response.content})
            messages.append({"role": "user", "content": tool_results})

        elif response.stop_reason == "max_tokens":
            raise ContextOverflowError(
                "Превышен лимит токенов — нужна компрессия контекста"
            )

        elif response.stop_reason == "pause_turn":
            # Агент ждёт human-in-the-loop подтверждения
            decision = await wait_for_human_approval(response)
            messages.append({"role": "assistant", "content": response.content})
            messages.append({"role": "user", "content": decision})

    raise MaxIterationsError(f"Превышен лимит итераций: {max_iterations}")


def extract_text(response):
    for block in response.content:
        if hasattr(block, "text"):
            return block.text
    return ""
```

**Антипаттерны:**
- ❌ Парсить `response.content[0].text` для определения завершения — используй `stop_reason`
- ❌ Произвольный лимит итераций как основной механизм остановки
- ❌ Не добавлять ответ ассистента в messages перед tool_results

---

## Coordinator–Subagent

Архитектура "хаб и спицы" для многоагентных систем:

```python
# Системный промпт координатора
COORDINATOR_SYSTEM = """
Ты координатор. Декомпозируй задачу на независимые подзадачи.
Для каждой подзадачи вызови инструмент Task.
Передавай субагентам только необходимый контекст — не всю историю.
Дожидайся всех результатов перед финальным синтезом.
"""

# Конфигурация координатора — allowedTools ОБЯЗАТЕЛЬНО включает "Task"
coordinator = AgentDefinition(
    system=COORDINATOR_SYSTEM,
    allowed_tools=["Task", "final_report"]  # Task = механизм порождения субагентов
)

# Промпт для субагента — контекст передаётся явно
def build_subagent_prompt(task: str, relevant_data: str) -> str:
    return f"""
Задача: {task}

Релевантные данные из предыдущих шагов:
{relevant_data}

Выполни задачу и верни структурированный результат с:
- findings: список ключевых выводов
- sources: список источников с URL и датами
- confidence: оценка уверенности (0-1)
- gaps: что не удалось найти
"""

# Параллельное выполнение субагентов — несколько Task в одном ответе
# Координатор может вызвать Task несколько раз в одном turn
```

**Ключевые принципы:**
- Субагенты работают с **изолированным контекстом** — не наследуют историю координатора
- Контекст передаётся **явно в промпте** субагента, не автоматически
- Координатор маршрутизирует **всю** межагентную коммуникацию
- Глубина вложенности: не более **2 уровней** (координатор → субагент)

**Слишком узкая декомпозиция** — риск: координатор создаёт подзадачи только по 1-2 аспектам широкой темы, остальные пропускаются. Проектируй промпт координатора так, чтобы он явно перечислял все области для покрытия.

---

## Хуки

PostToolUse хуки для детерминированных гарантий (в отличие от промпт-инструкций):

```json
// .claude/settings.json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "process_refund|delete_|drop_table",
        "hooks": [{
          "type": "command",
          "command": "python scripts/audit.py '${CLAUDE_TOOL_NAME}' '${CLAUDE_TOOL_INPUT}'"
        }]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "process_refund",
        "hooks": [{
          "type": "command",
          "command": "python scripts/check_policy.py '${CLAUDE_TOOL_INPUT}'"
        }]
      }
    ]
  }
}
```

```python
# scripts/check_policy.py — хук блокировки возвратов выше порога
import json, sys

tool_input = json.loads(sys.argv[1])
amount = tool_input.get("amount", 0)

if amount > 500:
    # Выход с ненулевым кодом = блокировка инструмента
    print(json.dumps({
        "block": True,
        "reason": f"Возврат {amount} превышает порог 500 — требует ручного одобрения",
        "escalate_to": "human_review_queue"
    }))
    sys.exit(1)
```

**Когда использовать хуки (не промпт):**
- Бизнес-правила требуют **гарантированного** соблюдения
- Финансовые операции, верификация личности, удаление данных
- Нормализация разнородных форматов данных от разных MCP-инструментов

---

## Сессии

```python
# Стратегия A — Stateless (высокий throughput)
# Каждый запрос независим, состояние хранится во внешней БД
def get_stateless_context(session_id: str) -> list:
    return db.get_session_messages(session_id, limit=0)  # без истории

# Стратегия B — Sliding Window (диалог, короткий фокус)
def get_sliding_window(messages: list, window_size: int = 20) -> list:
    return messages[-window_size:]

# Стратегия C — Summarization (длинные сессии)
async def compress_context(client, messages: list, keep_last: int = 5):
    if len(messages) <= keep_last + 2:
        return messages
    to_compress = messages[:-keep_last]
    summary = await summarize(client, to_compress)
    return [{"role": "user", "content": f"История сессии:\n{summary}"}] + messages[-keep_last:]

# Мониторинг использования контекста
def check_context_pressure(response) -> float:
    utilization = response.usage.input_tokens / 200_000  # claude-opus-4-6 limit
    if utilization > 0.8:
        trigger_compression()
    return utilization

# Возобновление именованной сессии
# claude --resume <session-name>
# fork_session — для параллельного исследования альтернативных подходов
```

---

## Декомпозиция

**Prompt chaining** (предсказуемые многоаспектные задачи):
```
Шаг 1: Анализ каждого файла отдельно → локальные проблемы
Шаг 2: Кросс-файловый интеграционный проход → архитектурные проблемы
Шаг 3: Синтез и приоритизация
```

**Динамическая декомпозиция** (открытые исследовательские задачи):
```
Шаг 1: Картирование структуры темы
Шаг 2: Определение приоритетных областей
Шаг 3: Адаптивный план на основе найденного
```

**Выбор паттерна:**
- Prompt chaining → предсказуемые ревью, многоаспектный анализ, известная структура
- Динамическая декомпозиция → исследование, exploratory задачи, неизвестная область
