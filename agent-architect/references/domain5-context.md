# Domain 5 — Управление контекстом и надёжность

## Содержание
1. [Стратегии управления контекстом](#стратегии)
2. [Иерархия эскалации](#эскалация)
3. [Human-in-the-loop паттерны](#human-loop)
4. [Ошибки в многоагентных системах](#multi-agent)
5. [Происхождение данных (Provenance)](#провенанс)
6. [Управление контекстом в больших кодовых базах](#кодовые-базы)

---

## Стратегии

```python
# Стратегия A — Stateless
# Когда: высокий throughput, нет сессионного состояния, каждый запрос независим
def get_context_stateless(request_data: dict) -> list:
    return [{"role": "user", "content": format_request(request_data)}]


# Стратегия B — Sliding Window
# Когда: диалог с пользователем, короткий временной фокус
def get_context_sliding(messages: list, window: int = 20) -> list:
    return messages[-window:]


# Стратегия C — Summarization
# Когда: длинные сессии с накопленным контекстом
async def get_context_summarized(client, messages: list, keep_last: int = 5) -> list:
    if len(messages) <= keep_last + 2:
        return messages

    # Сжать старые сообщения
    summary_response = await client.messages.create(
        model="claude-haiku-4-5-20251001",  # дешевле для суммаризации
        max_tokens=1024,
        messages=[{
            "role": "user",
            "content": f"Сожми эту историю диалога в 3-5 ключевых пунктов:\n{format_messages(messages[:-keep_last])}"
        }]
    )
    summary = extract_text(summary_response)

    return [
        {"role": "user", "content": f"Сводка предыдущей беседы:\n{summary}"}
    ] + messages[-keep_last:]


# Стратегия D — Hybrid (для агентов с длинными сессиями)
# Фиксированный блок критических фактов + скользящее окно истории
def get_context_hybrid(case_facts: dict, messages: list, window: int = 10) -> list:
    facts_block = {
        "role": "user",
        "content": f"""
## КРИТИЧЕСКИЕ ФАКТЫ (не сжимать)
- Customer ID: {case_facts.get('customer_id')}
- Order ID: {case_facts.get('order_id')}
- Сумма: {case_facts.get('amount')}
- Статус: {case_facts.get('status')}
- Дата: {case_facts.get('date')}
"""
    }
    return [facts_block] + messages[-window:]


# Мониторинг давления контекста
def check_context_pressure(response) -> float:
    utilization = response.usage.input_tokens / 200_000
    if utilization > 0.8:
        logger.warning(f"Контекст на {utilization:.0%} — нужна компрессия")
        trigger_compression()
    return utilization
```

**Эффект "потеря в середине":** модели надёжно обрабатывают данные в начале и конце длинного контекста, но могут пропускать данные в середине. Помещай критические факты в начало.

**Обрезка результатов инструментов:**
```python
# Инструменты возвращают много лишнего — обрезай до релевантного
def trim_tool_result(result: dict, relevant_fields: list) -> dict:
    return {k: v for k, v in result.items() if k in relevant_fields}
```

---

## Эскалация

```python
ESCALATION_LEVELS = {
    1: {
        "name": "auto_retry",
        "trigger": "transient error (network, timeout)",
        "action": "exponential backoff, max 3 попытки",
        "delay": lambda attempt: min(2 ** attempt, 60)
    },
    2: {
        "name": "alternative_approach",
        "trigger": "retry исчерпан или validation error",
        "action": "альтернативный инструмент или упрощённая задача"
    },
    3: {
        "name": "human_in_the_loop",
        "trigger": "business error, permission error, явный запрос клиента",
        "action": "передать человеку с полным контекстом"
    },
    4: {
        "name": "graceful_degradation",
        "trigger": "невозможно получить часть данных",
        "action": "частичный результат + явная аннотация пробелов"
    },
    5: {
        "name": "hard_failure",
        "trigger": "критическая системная ошибка",
        "action": "завершить процесс + алерт команде"
    }
}


async def handle_error(error: Exception, context: dict, attempt: int) -> dict:
    level = classify_escalation_level(error, attempt)

    if level == 1:
        delay = ESCALATION_LEVELS[1]["delay"](attempt)
        await asyncio.sleep(delay)
        raise error  # повторить

    elif level == 2:
        return await try_alternative_approach(context)

    elif level == 3:
        return await request_human_decision(
            summary=build_case_summary(context),
            options=["одобрить", "отклонить", "передать специалисту"],
            timeout_seconds=300
        )

    elif level == 4:
        return {
            "partial_result": context.get("partial_results"),
            "coverage_gaps": [{"reason": str(error), "impact": "низкая уверенность"}]
        }

    else:
        raise CriticalSystemError(str(error))
```

---

## Human-loop

```python
# Паттерн структурированного запроса на человека
async def request_human_decision(
    summary: str,
    options: list[str],
    timeout_seconds: int = 300
) -> str:
    ticket = await create_review_ticket(
        title="Требуется решение агента",
        body=summary,
        options=options,
        urgency="high" if "финанс" in summary.lower() else "normal"
    )

    # Ждать ответа с таймаутом
    decision = await wait_with_timeout(
        ticket.wait_for_decision(),
        timeout=timeout_seconds
    )

    if decision is None:
        # Таймаут — эскалировать дальше
        await escalate_to_supervisor(ticket)
        return "escalated_to_supervisor"

    return decision


def build_case_summary(context: dict) -> str:
    return f"""
## Данные клиента
- ID: {context.get('customer_id')}
- Email: {context.get('email')}

## Проблема
{context.get('issue_description')}

## Предпринятые действия
{format_actions(context.get('actions_taken', []))}

## Анализ первопричины
{context.get('root_cause_analysis')}

## Рекомендуемые действия
{format_recommendations(context.get('recommendations', []))}
"""
```

**Когда эскалировать немедленно (не пытаться решить):**
- Клиент явно просит "переключи на человека" / "хочу говорить с оператором"
- Политика молчит по данному запросу (пробел в политике)
- Множественные совпадения клиентов — запрашивай идентификатор, не выбирай эвристически

**Почему самооценка уверенности LLM ненадёжна:** агент переоценивает уверенность именно в сложных случаях. Используй явные бизнес-правила как триггеры, не "я не уверен".

---

## Multi-agent

```python
# Субагент ВСЕГДА возвращает структурированный контекст ошибки
def subagent_error_response(task: str, error: Exception, partial_results=None) -> dict:
    return {
        "is_error": True,
        "failure_type": classify_error(error),
        # transient → координатор может повторить
        # access    → нужно альтернативное решение
        # data_gap  → информация не существует
        "task_attempted": task,
        "partial_results": partial_results,   # отдавать что успел
        "suggested_alternatives": get_alternatives(task, error),
        "is_retryable": is_retryable(error)
    }


# Координатор: синтез с аннотацией пробелов
def synthesize_with_gap_annotation(results: list) -> dict:
    successful = [r for r in results if not r.get("is_error")]
    failed = [r for r in results if r.get("is_error")]

    return {
        "findings": successful,
        "coverage_gaps": [
            {
                "topic": r["task_attempted"],
                "reason": r["failure_type"],
                "impact": "данные отсутствуют в финальном отчёте"
            }
            for r in failed
        ],
        "confidence": len(successful) / len(results) if results else 0
    }
```

**Антипаттерны:**
- ❌ Тихое поглощение — возвращать пустой результат как успех
- ❌ Полное завершение процесса при единичном сбое субагента
- ❌ Общий статус `"search unavailable"` — скрывает контекст от координатора

---

## Провенанс

```python
# Каждый результат субагента должен содержать источник
def wrap_with_provenance(
    claim: str,
    source_url: str,
    doc_name: str,
    pub_date: str,
    confidence: float
) -> dict:
    return {
        "claim": claim,
        "provenance": {
            "source_url": source_url,
            "document_name": doc_name,
            "publication_date": pub_date,  # критично для темпоральных данных
            "confidence": confidence,       # 0-1
            "retrieved_at": datetime.now().isoformat()
        }
    }


# При противоречивых данных — аннотировать конфликт, не выбирать произвольно
def handle_conflicting_data(
    source_a: str, value_a,
    source_b: str, value_b
) -> dict:
    return {
        "conflicting_values": [
            {"value": value_a, "source": source_a},
            {"value": value_b, "source": source_b}
        ],
        "resolution": "requires_human_review",
        "note": "Значения различаются — возможно разные периоды или методологии"
    }


# Структура финального отчёта с разделением уверенности
def build_report_with_provenance(findings: list) -> dict:
    confirmed = [f for f in findings if f["provenance"]["confidence"] >= 0.8]
    uncertain = [f for f in findings if f["provenance"]["confidence"] < 0.8]

    return {
        "well_supported_findings": confirmed,
        "uncertain_findings": uncertain,
        "coverage_summary": {
            "total_sources": len(set(f["provenance"]["source_url"] for f in findings)),
            "date_range": get_date_range(findings)
        }
    }
```

---

## Кодовые базы

```python
# Деградация контекста в длинных сессиях:
# модель начинает ссылаться на "типичные паттерны" вместо конкретных классов

# Паттерн: файл-блокнот для ключевых выводов
SCRATCHPAD_PATH = ".agent_scratchpad.md"

def save_finding(finding: str, file_path: str, line: int = None):
    entry = f"\n## {datetime.now().strftime('%H:%M')}\n"
    entry += f"**Файл:** {file_path}"
    if line:
        entry += f":{line}"
    entry += f"\n{finding}\n"

    with open(SCRATCHPAD_PATH, "a") as f:
        f.write(entry)


# Делегирование исследования субагентам
async def explore_codebase(coordinator, questions: list[str]) -> dict:
    # Основной агент координирует на высоком уровне
    # Субагенты исследуют конкретные вопросы
    tasks = [
        coordinator.spawn_subagent(
            task=f"Исследуй: {question}",
            allowed_tools=["Read", "Grep", "Glob"]
        )
        for question in questions
    ]
    results = await asyncio.gather(*tasks)

    # Суммаризировать перед следующей фазой
    summaries = [r["summary"] for r in results]
    return inject_summaries_into_context(summaries)


# /compact — сократить использование контекста в длинных сессиях
# Команда Claude Code для компрессии истории
```

**Структурированная персистентность состояния для восстановления после сбоев:**
```python
# Каждый агент экспортирует состояние периодически
def export_agent_state(agent_id: str, completed_tasks: list, partial_results: dict):
    state = {
        "agent_id": agent_id,
        "timestamp": datetime.now().isoformat(),
        "completed_tasks": completed_tasks,
        "partial_results": partial_results
    }
    with open(f".agent_state_{agent_id}.json", "w") as f:
        json.dump(state, f)

# Координатор загружает манифест при возобновлении
def resume_from_manifest(manifest_path: str) -> dict:
    with open(manifest_path) as f:
        return json.load(f)
```
