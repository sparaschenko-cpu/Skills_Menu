# ddxcmd-gp4 — Guardian checkpoint GP-4 (post-resource)

## Назначение

Проверка после Mode 2 (Resource): валидность ресурсных гипотез и отсутствие “wishful thinking”.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)

## Входы

- Subagent: `/ddx-guardian`
- Rule: `.cursor/rules/ddx-safety.mdc`
- Артефакт: `Artifacts/DDx/<case_id>/12_resources_mode2.md`

## Шаги

1. Вызови `/ddx-guardian` и попроси провести GP-4:
   - какие ресурсы действительно поддержаны данными/фрагментами?
   - где есть риск идеализации/нормативности?
2. Сохрани результат как секцию “GP-4” внутри `12_resources_mode2.md`.

## Выходы (Artifacts/DDx/<case_id>/...)

- `12_resources_mode2.md` (обновлён, включая GP-4)

## Quality gates

- Ресурсные выводы имеют наблюдаемые маркеры (операционализация), а не только интерпретации.
