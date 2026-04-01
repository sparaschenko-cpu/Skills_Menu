# Domain 3 — Конфигурация Claude Code

## Содержание
1. [Иерархия CLAUDE.md](#иерархия)
2. [Rules — условная загрузка](#rules)
3. [Slash-команды и навыки](#команды)
4. [Plan Mode vs Direct Execution](#plan-mode)
5. [CI/CD интеграция](#ci-cd)

---

## Иерархия

```
~/.claude/CLAUDE.md              ← глобальные правила (стиль, язык ответов)
  └── [repo]/CLAUDE.md           ← правила проекта (стек, соглашения)
        └── [module]/CLAUDE.md   ← правила модуля (специфика сервиса)
```

**Шаблон проектного CLAUDE.md:**

```markdown
# [Название проекта]

## Контекст
[1-2 предложения о назначении — что делает система, для кого]

## Технический стек
- Язык: Python 3.11+
- Framework: FastAPI
- БД: PostgreSQL + SQLAlchemy
- Тесты: pytest

## Соглашения кода
- Async/await для всех I/O операций
- Pydantic v2 для валидации данных
- Явные type hints везде

## Обязательные правила
- Все ошибки инструментов: is_error + error_category + is_retryable
- Критичные операции (DELETE, финансы > 500) → human-in-the-loop
- API ключи только через переменные окружения, никогда хардкодом
- Логировать все вызовы внешних API

## Структура проекта
src/
  agents/     ← агентный код
  tools/      ← MCP-инструменты
  models/     ← Pydantic схемы
tests/
```

**Синтаксис @import** для модуляризации:
```markdown
# CLAUDE.md
@import .claude/standards/testing.md
@import .claude/standards/api-conventions.md
```

**Диагностика:** команда `/memory` — проверить какие файлы памяти загружены.

---

## Rules

Файлы `.claude/rules/` активируются условно по glob-паттернам путей.

```markdown
// .claude/rules/agent-safety.md
---
description: Правила безопасности для агентных операций
---

- Перед деструктивными операциями (DELETE, rm -rf, DROP TABLE) — запрашивай подтверждение
- При is_error=true — не retry более 3 раз без эскалации к человеку
- Логировать все вызовы внешних API с параметрами и результатами
- Никогда не коммитить .env файлы или API ключи
```

```markdown
// .claude/rules/api-conventions.md
---
description: Конвенции для API-обработчиков
paths: ["src/api/**/*"]
---

- Все эндпоинты: async def
- Обработка ошибок: HTTPException с кодом и detail
- Валидация входных данных через Pydantic схемы
- Логирование: structlog с request_id
```

```markdown
// .claude/rules/testing.md
---
description: Конвенции для тестов (все тест-файлы в проекте)
paths: ["**/*.test.*", "**/*_test.py", "**/tests/**"]
---

- Fixture-ы в conftest.py, не в тест-файлах
- Один assert на тест (принцип единственной ответственности)
- Моки только для внешних сервисов, не для внутреннего кода
- Имена: test_[что]_when_[условие]_should_[ожидаемое]
```

**Преимущество path-scoped rules:** применяются к файлам в любой директории, в отличие от CLAUDE.md в поддиректориях.

**Разделение большого CLAUDE.md:**
```
.claude/rules/
  agent-safety.md
  api-conventions.md
  testing.md
  deployment.md
```

---

## Команды

### Slash-команды

```markdown
// .claude/commands/run-agent.md  ← проектные команды (в VCS, для команды)
---
description: Запустить агента с полной трассировкой инструментов
---

Запусти агента с аргументом $ARGUMENTS.
Покажи каждый вызов инструмента: имя, входные данные, результат.
В конце — сводка: количество вызовов, ошибки по категориям, итоговый результат.
```

```markdown
// ~/.claude/commands/debug-agent.md  ← персональные команды (не в VCS)
---
description: Отладочный режим с подробным логированием
---

[персональные инструкции для отладки]
```

### Навыки (Skills)

```markdown
// .claude/skills/my-skill/SKILL.md
---
name: my-skill
description: [когда использовать]
context: fork          ← изолирует навык в отдельном подагенте
allowed-tools: Read, Grep, Glob    ← ограничение инструментов
argument-hint: <описание аргумента>
---

[инструкции навыка]
```

**Выбор между навыком и CLAUDE.md:**
- Навык → вызов по запросу для конкретной задачи, объёмный вывод
- CLAUDE.md → постоянно загружаемые универсальные стандарты

**`context: fork`** — изолирует объёмный вывод навыка от основной сессии.

---

## Plan Mode

```
Используй /plan (режим плана) когда:
✓ Изменения затрагивают > 5 файлов или > 1 сервис
✓ Архитектурные решения с несколькими допустимыми подходами
✓ Миграция библиотеки на 45+ файлов
✓ Первый запуск новой функциональности в production
✓ Операция на production данных (необратима)

Прямое выполнение когда:
✓ Чётко определённый баг со стектрейсом
✓ Добавление одного условия валидации
✓ Read-only операция (анализ, объяснение)
✓ Однофайловое изменение с очевидным решением
```

**Подагент Explore** — для изоляции объёмного исследования:
- Делегирует исследование кодовой базы в отдельный контекст
- Основной агент получает только структурированную сводку
- Сохраняет основной контекст для реализации

---

## CI/CD

```bash
# Флаг -p (--print) — неинтерактивный режим для CI
claude -p "Analyze this pull request for security issues"

# Структурированный вывод для машинной обработки
claude -p "Review PR" --output-format json --json-schema review_schema.json
```

```yaml
# .github/workflows/claude-review.yml
- name: Claude Code Review
  run: |
    claude -p "
      Review the changed files for:
      1. Security vulnerabilities (SQL injection, XSS, secrets in code)
      2. Breaking changes to public API
      3. Missing error handling for external calls

      For each issue: file, line, severity (critical/high/medium), description, fix.
      Skip style issues — linter handles those.
    " --output-format json > review.json
```

**Изоляция контекста в CI:** та же сессия, что генерировала код, менее эффективна при его ревью. Запускай ревью в новой сессии без предыдущего контекста генерации.

**CLAUDE.md в CI контексте:**
- Документируй стандарты тестирования, критерии ревью, доступные фикстуры
- Включай предыдущие результаты ревью при повторном запуске после новых коммитов
