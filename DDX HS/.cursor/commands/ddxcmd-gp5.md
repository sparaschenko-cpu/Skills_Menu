# ddxcmd-gp5 — Guardian checkpoint GP-5 (pre-conclusion / internal red teaming)

## Назначение

Проверка перед финальным заключением: internal red teaming, overfitting, скрытые допущения, high-risk management.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)

## Входы

- Subagent: `/ddx-guardian`
- Rule: `.cursor/rules/ddx-safety.mdc`
- Артефакты: `Artifacts/DDx/<case_id>/07_evidence_matrix.md`, `Artifacts/DDx/<case_id>/08_hypothesis_map.md`, `Artifacts/DDx/<case_id>/13_reliability_passport_mode3.md` (если есть)

## Шаги

1. Вызови `/ddx-guardian` и попроси провести GP-5 (internal red teaming):
   - контрдоказательства и альтернативы учтены?
   - есть ли признаки overfitting/нарративной ошибки?
   - есть ли “слишком гладкая” модель → триггер Mode 3?
   - high-risk утверждения изолированы в методологическом приложении, если нужно?
2. Сохрани результат в виде блока “GP-5” в `14_conclusion_3_3_3.md` (как часть методологического приложения) или отдельной секцией, если так удобнее.

## Выходы (Artifacts/DDx/<case_id>/...)

- Обновление `14_conclusion_3_3_3.md` (добавлен блок GP-5) или подготовка списка правок перед записью заключения.

## Quality gates

- При критических issues: не финализировать заключение до исправлений.
