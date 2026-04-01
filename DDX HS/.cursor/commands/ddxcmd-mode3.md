# ddxcmd-mode3 — Mode 3 (Meta-critique) и “паспорт надёжности”

## Назначение

Используй, когда модель “слишком гладкая”, ставки высокие, или требуется максимальная надёжность (robustness/sensitivity/failure modes).

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- (опционально) `scope=` (например: `robust-only`, `full`)

## Входы

- Skill: `/ddx-activate-mode`
- Subagent: `/ddx-critic`
- Rules: `.cursor/rules/ddx-resource-metacritique.mdc`
- Артефакт: `Artifacts/DDx/<case_id>/13_reliability_passport_mode3.md`

## Шаги

1. Вызови Skill `/ddx-activate-mode` и формально активируй Mode 3.
2. Делегируй аудит модели:
   - вызови `/ddx-critic` и потребуй **index-first** (уязвимости, чувствительность, робастное ядро, границы применимости).
3. Сохрани “паспорт надёжности” в `13_reliability_passport_mode3.md`.
4. Обнови (если нужно) `14_conclusion_3_3_3.md`, добавив методологическое приложение/ограничения на основе паспорта.

## Выходы (Artifacts/DDx/<case_id>/...)

- `13_reliability_passport_mode3.md` (создан/обновлён)
- (опционально) обновления `14_conclusion_3_3_3.md`

## Quality gates

- Любые уязвимости/чувствительность привязаны к конкретным A-ID/свидетельствам, а не “в общем”.
