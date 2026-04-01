# ddxcmd-phase0-scope — Phase 0: вопрос, границы, риск-политика, Zero Point (GP-0)

## Назначение

Используй в начале кейса, чтобы качественно зафиксировать вопрос/цель, границы анализа (Axis3/Axis4), риск-политику (Axis5) и пройти Zero Point.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- `input=` (обязательно: что анализируем / где материал)
- (опционально) `protocol=basic|standard|max`

## Входы

- `Artifacts/DDx/<case_id>/00_question_and_scope.md`
- `Artifacts/DDx/<case_id>/01_zero_point.md`
- Rules: `.cursor/rules/ddx-phases.mdc`, `.cursor/rules/ddx-axes-formula.mdc`, `.cursor/rules/ddx-safety.mdc`
- Skill: `/ddx-safety-audit`
- Subagent (при необходимости): `/ddx-guardian` (GP-0)

## Шаги

1. Заполни `00_question_and_scope.md`:
   - диагностический вопрос / цель
   - границы объекта (Axis3) и масштаба (Axis4)
   - политика Axis5 (что считаем 1–2 vs 4–5)
   - решение о вероятности 8D (если материал реляционный)
   - ссылки: `Manual: 0.2, 0.3` (и релевантные приложения при Max)
2. Заполни `01_zero_point.md`:
   - выполни `/ddx-safety-audit` (Zero Point + преференции)
3. Выполни **GP-0**:
   - вызови `/ddx-guardian` и попроси провести GP-0 (готовность, преференции, риск-флаги)
   - зафиксируй результат в `01_zero_point.md` (PASS/ISSUES/VETO)

## Выходы (Artifacts/DDx/<case_id>/...)

- `00_question_and_scope.md` (обновлён)
- `01_zero_point.md` (обновлён, включая GP-0)

## Quality gates

- Вопрос/границы сформулированы так, чтобы гипотезы были фальсифицируемыми (без “всё обо всём”).
- Любые ранние интерпретации помечены Axis5 и не masquerade как факты.
