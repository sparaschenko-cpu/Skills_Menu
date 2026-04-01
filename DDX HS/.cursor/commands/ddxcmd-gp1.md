# ddxcmd-gp1 — Guardian checkpoint GP-1 (post-exploration)

## Назначение

Проверка после первого прохода Phase 1: аффективный резонанс, первичный резонанс, риск ранних выводов.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)

## Входы

- Subagent: `/ddx-guardian`
- Rule: `.cursor/rules/ddx-safety.mdc`
- Артефакты: `Artifacts/DDx/<case_id>/02_annotations/index.md`, `Artifacts/DDx/<case_id>/03_data_map.md`

## Шаги

1. Вызови `/ddx-guardian` и попроси провести GP-1:
   - аффективный резонанс (что это может исказить?)
   - первичный резонанс (что “слишком знакомо”?)
   - риск ранней теоретизации (Axis5 и deferred selection дисциплина)
2. Сохрани результат как блок “GP-1” в `02_annotations/index.md` (или отдельным разделом в `03_data_map.md`, если так удобнее).

## Выходы (Artifacts/DDx/<case_id>/...)

- Обновлён `02_annotations/index.md` (или `03_data_map.md`) с результатами GP-1.

## Quality gates

- Выводы/гипотезы на этом этапе остаются предварительными и маркированы Axis5.
