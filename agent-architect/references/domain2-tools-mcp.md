# Domain 2 — Дизайн инструментов и MCP-интеграция

## Содержание
1. [Шаблон описания инструмента](#описание-инструмента)
2. [Структурированные ошибки](#ошибки)
3. [Стратегия tool_choice](#tool_choice)
4. [Конфигурация MCP](#mcp)
5. [Встроенные инструменты Claude Code](#встроенные)

---

## Описание инструмента

Описание — основной механизм выбора инструмента LLM. Минимальные описания → ненадёжный выбор.

```python
tools = [
    {
        "name": "get_customer",
        "description": """
        Используй КОГДА: нужно получить данные клиента по email или phone.
        НЕ используй для поиска заказов — для этого есть lookup_order.
        Принимает: email (формат user@domain.com) или phone (формат +7XXXXXXXXXX).
        Возвращает: {customer_id, name, email, account_status, created_at}.
        Граничные случаи: если клиент не найден — возвращает {"found": false}.
        """,
        "input_schema": {
            "type": "object",
            "properties": {
                "email": {
                    "type": "string",
                    "description": "Email клиента. Формат: user@domain.com"
                },
                "phone": {
                    "type": "string",
                    "description": "Телефон клиента. Формат: +7XXXXXXXXXX"
                }
            }
            # required не указан — принимает email ИЛИ phone
        }
    },
    {
        "name": "lookup_order",
        "description": """
        Используй КОГДА: нужно найти заказ по его номеру или customer_id.
        НЕ используй для поиска данных клиента — для этого get_customer.
        Принимает: order_id (формат ORD-XXXXXX) или customer_id (UUID).
        Возвращает: {order_id, status, items, total, created_at, shipping}.
        """,
        "input_schema": {
            "type": "object",
            "properties": {
                "order_id": {"type": "string", "description": "Формат: ORD-XXXXXX"},
                "customer_id": {"type": "string", "description": "UUID клиента из get_customer"}
            }
        }
    }
]
```

**Принципы описания:**
- Объясняй **КОГДА вызывать**, а не только что делает
- Указывай **НЕ использовать когда** — для похожих инструментов это критично
- Добавляй **форматы входных данных с примерами**
- Описывай **граничные случаи** (пустой результат ≠ ошибка)
- ≤ 5 инструментов на агента — больше снижает надёжность выбора

**Устранение перекрытия инструментов:**
- Переименуй: `analyze_content` и `analyze_document` → `analyze_text_content` и `analyze_file_structure`
- Разделяй универсальные инструменты на специализированные с чёткими контрактами

---

## Ошибки

Структурированные ошибки дают агенту информацию для интеллектуального восстановления.

```python
def tool_error_response(tool_use_id: str, error: Exception) -> dict:
    return {
        "type": "tool_result",
        "tool_use_id": tool_use_id,
        "is_error": True,   # ОБЯЗАТЕЛЬНО — без этого агент примет ошибку за успех
        "content": json.dumps({
            "error_message": str(error),
            "error_category": classify_error(error),
            # transient    → retry после задержки (сеть, таймауты)
            # validation   → исправь входные данные и retry
            # permission   → эскалируй к человеку
            # business     → обработай в бизнес-логике агента
            "is_retryable": is_retryable(error),
            "suggested_action": get_suggested_action(error),
            "partial_results": getattr(error, "partial_results", None)
        })
    }


def classify_error(error: Exception) -> str:
    if isinstance(error, (TimeoutError, ConnectionError)):
        return "transient"
    if isinstance(error, (ValueError, ValidationError)):
        return "validation"
    if isinstance(error, PermissionError):
        return "permission"
    return "business"


def is_retryable(error: Exception) -> bool:
    return classify_error(error) == "transient"
```

**Антипаттерны:**
- ❌ `{"result": "Operation failed"}` — агент не знает что делать
- ❌ Возвращать пустой результат при ошибке доступа (пустой ≠ "не найдено")
- ❌ Поглощать ошибки субагентов молча — координатор теряет контекст

**Разграничение сбоев:**
- Сбой доступа (таймаут, 503) → `is_retryable: true`, предлагай альтернативу
- Валидный пустой результат (поиск без совпадений) → `is_error: false`, `{"found": false}`

---

## tool_choice

```python
# auto — агент выбирает сам (стандарт для большинства случаев)
tool_choice = {"type": "auto"}

# any — агент ОБЯЗАН вызвать инструмент (не текстовый ответ)
# Используй когда: неизвестный тип документа, нужен структурированный вывод
tool_choice = {"type": "any"}

# tool — принудительный вызов конкретного инструмента
# Используй когда: гарантированный структурированный вывод, первый шаг конвейера
tool_choice = {"type": "tool", "name": "extract_metadata"}

# Пример: гарантированное извлечение данных
response = client.messages.create(
    tools=[EXTRACTION_SCHEMA],
    tool_choice={"type": "tool", "name": "extract_data"},
    messages=messages
)
```

---

## MCP

```json
// .mcp.json — уровень проекта (попадает в систему контроля версий)
// Для общих командных инструментов
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

```json
// ~/.claude.json — уровень пользователя
// Для персональных или экспериментальных серверов (не в VCS)
{
  "mcpServers": {
    "my-local-server": {
      "command": "python",
      "args": ["~/tools/my_mcp_server.py"]
    }
  }
}
```

**MCP-ресурсы vs инструменты:**
- **Инструменты** — для действий и запросов с параметрами
- **Ресурсы** — для каталогов контента (схемы БД, иерархии документации, сводки задач), позволяют обзор без исследовательских вызовов

**Выбор MCP-сервера:**
- Существующий community-сервер → для стандартных интеграций (GitHub, Slack, БД)
- Кастомный сервер → для специфичных командных процессов и внутренних API

---

## Встроенные

| Инструмент | Когда использовать |
|-----------|-------------------|
| `Grep` | Поиск по содержимому файлов (имена функций, импорты, сообщения об ошибках) |
| `Glob` | Поиск файлов по шаблонам путей (расширения, имена директорий) |
| `Read` | Чтение полного содержимого файла |
| `Edit` | Точечные изменения по уникальному совпадению текста |
| `Write` | Создание нового файла или полная перезапись |
| `Bash` | Системные команды, которые нельзя выполнить другими инструментами |

**Паттерн изучения кодовой базы:**
1. `Grep` — найти точки входа (названия функций, классов)
2. `Read` — проследить импорты и потоки данных
3. `Glob` — найти все файлы нужного типа
4. `Edit` — если совпадение уникально; иначе `Read + Write`
