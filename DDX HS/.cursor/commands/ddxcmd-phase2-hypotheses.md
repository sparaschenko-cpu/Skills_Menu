# ddxcmd-phase2-hypotheses — Phase 2: гипотезы (генерация → фальсификация → обновление)

## Назначение

Используй для выполнения Explication (Phase 2): генерация поля гипотез, формализация H-cards, fast falsification, байесовские обновления, Evidence Matrix и Hypothesis Map.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- (опционально) `protocol=basic|standard|max`
- (опционально) `scope=` (например: `hypotheses-only`, `include-funnel`, `include-timeline`)

## Входы

- `Artifacts/DDx/<case_id>/03_data_map.md`
- `Artifacts/DDx/<case_id>/04_lenses.md`
- `Artifacts/DDx/<case_id>/02_annotations/index.md` (+ чанки)
- Skill: `/ddx-hypothesis-lifecycle`
- Subagent: `/ddx-analyst`
- Rules: `.cursor/rules/ddx-hypothesis-risk.mdc`, `.cursor/rules/ddx-validation-funnel.mdc`, `.cursor/rules/ddx-smh-advanced.mdc`

## Шаги

1. Убедись, что Phase 1 завершён: есть Data Map + индекс аннотаций.
2. Запусти процедурный backbone Phase 2:
   - Вызови `/ddx-hypothesis-lifecycle` (генерация из 3 источников, Axis2 как двигатель, fast falsification, Bayes).
3. Делегируй тяжёлую работу субагенту:
   - Вызови `/ddx-analyst` и потребуй **index-first** с явными выходными артефактами.
4. Сохраняй артефакты Phase 2:
   - `05_smh_registry.md` (реестр гипотез, статусы)
   - `06_hcards/index.md` + чанки `06_hcards/H-0001_H-0050.md` (и далее)
   - `07_evidence_matrix.md`
   - `08_hypothesis_map.md`
5. Если `protocol=max` или `scope=include-timeline`:
   - обнови `09_hypothesis_timeline.md`
6. Если пул гипотез большой (например >7–10) или `scope=include-funnel`:
   - подготовь `10_validation_funnel.md` (или запусти `/ddxcmd-phase2-validation-funnel case_id=...`)
7. Safety checkpoint по Phase 2:
   - Вызови `/ddx-guardian` для проверки критических мест (high-risk, H-Adaptive для pathology, contextual grounding), результаты зафиксируй в релевантных артефактах.

## Выходы (Artifacts/DDx/<case_id>/...)

- `05_smh_registry.md`
- `06_hcards/index.md` + чанки
- `07_evidence_matrix.md`
- `08_hypothesis_map.md`
- (опционально) `09_hypothesis_timeline.md`
- (опционально) `10_validation_funnel.md`

## Quality gates

- Каждая гипотеза фальсифицируема и имеет операциональные индикаторы (не “поэтические” формулировки).
- Evidence Matrix и Hypothesis Map ссылаются на A-ID.
- Axis5 проставлен на ключевые утверждения/гипотезы.
