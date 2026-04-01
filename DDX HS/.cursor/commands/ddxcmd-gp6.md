# ddxcmd-gp6 — Guardian checkpoint GP-6 (post-conclusion)

## Назначение

Финальная safety-проверка после заключения: соответствие протоколам, триггер внешней супервизии, финальная маркировка рисков.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)

## Входы

- Subagent: `/ddx-guardian`
- Rule: `.cursor/rules/ddx-safety.mdc`
- Артефакт: `Artifacts/DDx/<case_id>/14_conclusion_3_3_3.md`
- (опционально) `Artifacts/DDx/<case_id>/external_supervision_log.md`

## Шаги

1. Вызови `/ddx-guardian` и попроси провести GP-6:
   - соблюдены ли ограничения Axis5 (особенно 4–5)?
   - есть ли явные оговорки границ применимости?
   - требуется ли внешняя супервизия (и почему)?
2. Если супервизия нужна — создай/обнови `external_supervision_log.md` (кто/когда/что ревалидировать).
3. Сохрани результат GP-6 как финальный блок в `14_conclusion_3_3_3.md` или в `external_supervision_log.md` (если вопрос супервизии центральный).

## Выходы (Artifacts/DDx/<case_id>/...)

- Обновление `14_conclusion_3_3_3.md` (GP-6) и/или `external_supervision_log.md`.

## Quality gates

- При VETO: заключение помечается как предварительное до супервизии/ревалидирования.
