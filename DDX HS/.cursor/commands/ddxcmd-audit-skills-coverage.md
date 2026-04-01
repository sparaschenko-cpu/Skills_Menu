# ddxcmd-audit-skills-coverage — аудит покрытия Manual/Apps навыками

## Назначение

Используй, чтобы проверить, какие части Manual/Apps действительно “операционализированы” в Skills (`.cursor/skills/*/SKILL.md`, templates) и где остаётся зависимость от ручного чтения правил.

## Параметры

- (опционально) `scope=` (например: `phase1`, `phase2`, `appendix3`, `all`)

## Входы

- `Manual/DDx-6D_8D.md`
- Skills: `.cursor/skills/*/SKILL.md` (+ связанные файлы в skill-папках)
- Rules: `.cursor/rules/*.mdc` (для кросс-проверки ссылок)

## Шаги

1. Построй матрицу: Manual/App секция → Skill/файл → артефакт (если есть) → степень операционализации.
2. Отметь пробелы 2 типов:
   - “есть rule, нет procedural steps/templates”
   - “есть steps, но нет требуемых выходных артефактов/quality gates”
3. Предложи конкретные доработки (какой skill/файл, что добавить).

## Выходы

- Отчёт в чате: матрица + приоритизированный backlog.

## Quality gates

- Не смешивать Skills и Subagents: аудит Skills отдельно от `.cursor/agents/*.md`.
