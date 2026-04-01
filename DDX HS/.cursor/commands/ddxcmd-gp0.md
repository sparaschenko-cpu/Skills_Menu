# ddxcmd-gp0 — Guardian checkpoint GP-0 (pre-analysis)

## Назначение

Zero Point + преференции + готовность к старту анализа (до Phase 1).

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)

## Входы

- Subagent: `/ddx-guardian`
- Skill: `/ddx-safety-audit`
- Rule: `.cursor/rules/ddx-safety.mdc`
- Артефакт: `Artifacts/DDx/<case_id>/01_zero_point.md`

## Шаги

1. Выполни `/ddx-safety-audit` (Zero Point + преференции).
2. Вызови `/ddx-guardian` и попроси провести GP-0, вернуть PASS/ISSUES/VETO и рекомендации минимизации.
3. Сохрани результат в `01_zero_point.md` (явно пометь GP-0).

## Выходы (Artifacts/DDx/<case_id>/...)

- `01_zero_point.md` (обновлён, включая GP-0)

## Quality gates

- При VETO: не продолжать Phase 1, пока не выполнены шаги минимизации.
