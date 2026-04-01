# ddxcmd-mode2 — Mode 2 (Resource) при pathology-only модели

## Назначение

Используй, когда модель стала “pathology-only” или требуется системная салютогенная перспектива (ресурсы/резильентность/исключения).

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- (опционально) `scope=` (например: `swot-only`, `full`)

## Входы

- Skill: `/ddx-activate-mode`
- Subagent: `/ddx-resource-finder`
- Rules: `.cursor/rules/ddx-resource-metacritique.mdc`, `.cursor/rules/ddx-safety.mdc`
- Артефакт: `Artifacts/DDx/<case_id>/12_resources_mode2.md`

## Шаги

1. Вызови Skill `/ddx-activate-mode` и формально активируй Mode 2.
2. Делегируй ресурсный анализ:
   - вызови `/ddx-resource-finder` и потребуй **index-first** (SWOT + ресурсная карта + интеграция с основной моделью).
3. Сохрани результат в `12_resources_mode2.md`.
4. Safety gate Mode 2:
   - вызови `/ddx-guardian` (GP-4 логика) и проверь, что ресурсные гипотезы не являются “wishful thinking” без опоры на данные.

## Выходы (Artifacts/DDx/<case_id>/...)

- `12_resources_mode2.md` (создан/обновлён)

## Quality gates

- Ресурсная модель интегрирована с пато-ориентированной (компенсация/защита/уязвимость/потенциал), а не существует параллельно.
