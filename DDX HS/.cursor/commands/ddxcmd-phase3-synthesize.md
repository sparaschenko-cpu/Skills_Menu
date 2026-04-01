# ddxcmd-phase3-synthesize — Phase 3: модели, сценарии, заключение

## Назначение

Используй для выполнения Integration (Phase 3): построение структурной/динамической модели, углублённые сценарии (2.2–2.3), написание заключения 3.3.3, финальные safety-checks.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- (опционально) `protocol=basic|standard|max`
- (опционально) `scope=` (например: `models-only`, `conclusion-only`, `include-deep-scenarios`)

## Входы

- `Artifacts/DDx/<case_id>/05_smh_registry.md`
- `Artifacts/DDx/<case_id>/07_evidence_matrix.md`
- `Artifacts/DDx/<case_id>/08_hypothesis_map.md`
- (опционально) `Artifacts/DDx/<case_id>/08d_context.md`
- Subagent: `/ddx-synthesizer`
- Skill: `/ddx-write-conclusion`
- Rules: `.cursor/rules/ddx-integration-conclusion.mdc`, `.cursor/rules/ddx-deep-scenarios.mdc`

## Шаги

1. Убедись, что Phase 2 готов: есть SMH + Evidence Matrix + Hypothesis Map.
2. Делегируй синтез субагенту:
   - Вызови `/ddx-synthesizer` и потребуй **index-first** (модели/сценарии/заключение частями, chunk plan при объёме).
3. Углублённые сценарии (если `protocol=standard|max` или `scope=include-deep-scenarios`):
   - Заполни `11_deep_scenarios.md` (worksheets 2.2.1–2.3.2 по необходимости/протоколу).
4. Режимы по триггерам:
   - если модель pathology-only → Mode 2 (обнови `12_resources_mode2.md`)
   - если “слишком гладко”/высокие ставки → Mode 3 (обнови `13_reliability_passport_mode3.md`)
5. Финальный safety gate:
   - Вызови `/ddx-guardian` (pre-conclusion/GP-5) и зафиксируй PASS/ISSUES/VETO.
6. Напиши/обнови заключение:
   - Вызови `/ddx-write-conclusion` и сохрани результат в `14_conclusion_3_3_3.md`.

## Выходы (Artifacts/DDx/<case_id>/...)

- `11_deep_scenarios.md` (если выполнялись сценарии)
- `12_resources_mode2.md` (если Mode 2 активирован)
- `13_reliability_passport_mode3.md` (если Mode 3 активирован)
- `14_conclusion_3_3_3.md`

## Quality gates

- Каждый существенный вывод в заключении: A-ID + Axis5.
- Модели/связи не “в воздухе”: опора на Evidence Matrix/корреляционные основания и явная рационализация.
