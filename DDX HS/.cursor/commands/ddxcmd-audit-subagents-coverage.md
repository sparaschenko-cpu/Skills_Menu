# ddxcmd-audit-subagents-coverage — аудит качества/покрытия Subagents

## Назначение

Используй, чтобы проверить Subagents (`.cursor/agents/*.md`) на соответствие стандартам: RU-primary, index-first, ссылки Manual/App, корректные контракты входов/выходов.

## Параметры

- (опционально) `scope=` (например: `ru-primary-only`, `index-first-only`, `all`)

## Входы

- Subagents: `.cursor/agents/*.md`
- Canonical: `Manual/DDx-6D_8D.md`
- Workspace rule: `.cursor/rules/ddx-workspace-context.mdc`

## Шаги

1. Для каждого субагента проверь чек-лист:
   - RU-primary (EN в скобках)
   - index-first output контракт (Summary/Index/Top evidence/Chunk plan/Payload)
   - ссылки на `Manual:`/`App.` в ключевых шагах
   - явные входы/выходы (какие артефакты производит)
2. Выведи список несоответствий и предложи точечные правки по каждому файлу.

## Выходы

- Отчёт в чате: таблица “subagent → PASS/ISSUES → что исправить”.

## Quality gates

- Не требовать от субагентов писать в workspace напрямую; сохранение выполняет родительский агент.
