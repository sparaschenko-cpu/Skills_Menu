# ddxcmd-regression-check — регресс-чек системы (Rules/Skills/Subagents/Commands)

## Назначение

Используй после изменений в `.cursor/` (rules/skills/agents/commands), чтобы быстро убедиться, что система не деградировала: имена, кросс-ссылки, каноничность, RU-primary, коллизии.

## Параметры

- (опционально) `scope=` (например: `commands-only`, `rules-only`, `all`)

## Входы

- `.cursor/rules/*.mdc`
- `.cursor/skills/*/SKILL.md`
- `.cursor/agents/*.md`
- `.cursor/commands/*.md`

## Шаги

1. Проверка имён:
   - Commands: все начинаются с `ddxcmd-`
   - Skills: корректный `name:` и отсутствие коллизий с Commands/Subagents
2. Проверка RU-primary:
   - команды/субагенты не скатываются в EN-only
3. Проверка каноничности rules:
   - правила заканчиваются строкой “Канонический источник: Manual/DDx-6D_8D.md …” (если это принятый стандарт проекта)
4. Проверка кросс-ссылок:
   - упоминания `/ddx-...` соответствуют реальным skills/subagents
   - `Artifacts/DDx/<case_id>/...` соответствует скелету `_TEMPLATES`
5. Выведи список проблем и минимальные remediation-шаги.

## Выходы

- Отчёт в чате: PASS/ISSUES по категориям.

## Quality gates

- Никаких “молчаливых” правок: только отчёт и конкретные рекомендации.

