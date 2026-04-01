# ddxcmd-new-case — создать/инициализировать кейс DDx

## Назначение

Используй в начале нового кейса, чтобы создать структуру `Artifacts/DDx/<case_id>/` из шаблона и зафиксировать минимальные стартовые артефакты.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- (опционально) `protocol=basic|standard|max`

## Входы

- `Artifacts/DDx/_TEMPLATES/case_skeleton/` (шаблон кейса)

## Шаги

1. **Валидируй `case_id`**:
   - формат: `YYYYMMDD_slug`
   - только латиница/цифры/`_`/`-` (без пробелов)
2. **Создай папку** `Artifacts/DDx/<case_id>/`.
3. **Скопируй скелет** из `Artifacts/DDx/_TEMPLATES/case_skeleton/` в `Artifacts/DDx/<case_id>/` с сохранением структуры подпапок (`02_annotations/`, `06_hcards/`).
4. Проверь, что в кейсе присутствуют (минимум):
   - `00_question_and_scope.md`
   - `01_zero_point.md`
   - `02_annotations/index.md` и `02_annotations/A-0001_A-0100.md`
   - `03_data_map.md`
   - `04_lenses.md`
   - `05_smh_registry.md`
   - `06_hcards/index.md` и `06_hcards/H-0001_H-0050.md`
   - `07_evidence_matrix.md`
   - `08_hypothesis_map.md`
   - `14_conclusion_3_3_3.md`
   - `15_coverage_check.md`
5. Если `protocol=max`, убедись, что присутствуют также (если их нет — создай из шаблона):
   - `09_hypothesis_timeline.md`
   - `10_validation_funnel.md`
   - `11_deep_scenarios.md`
   - `12_resources_mode2.md`
   - `13_reliability_passport_mode3.md`
   - `08d_context.md` (если кейс реляционный/8D вероятен)
   - `external_supervision_log.md` (если высокий риск/супервизия вероятна)

## Выходы (Artifacts/DDx/<case_id>/...)

- Создана/обновлена структура кейса и все файлы скелета.

## Quality gates

- Никаких файловых операций без валидного `case_id`.
- Скелет скопирован полностью, подпапки сохранены.
