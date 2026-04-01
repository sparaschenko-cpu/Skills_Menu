## Coverage checklist (регрессионный контроль покрытия Manual/Apps)

Назначение: быстрый аудит “что именно применено” без перечитывания всего `Manual/DDx-6D_8D.md`.

### Как использовать

1. В начале кейса скопировать шаблон `Artifacts/DDx/_TEMPLATES/case_skeleton/15_coverage_check.md` в `Artifacts/DDx/<case_id>/15_coverage_check.md`.
2. По ходу анализа отмечать выполненные пункты и фиксировать исключения/упрощения.
3. Перед финализацией сверить с обязательными артефактами и чек-листами протоколов A/B/C (`protocol-checklists.md`).

---

### Маппинг: Manual/App → артефакт → где реализовано

| Manual/App | Что требуется | Где в системе | Где в кейсе (Artifacts) |
|---|---|---|---|
| 0.2, 0.3 | Формула + фазы + границы | Skill `ddx-run-analysis` | `00_question_and_scope.md` |
| App.4 | Zero Point + преференции | Skill `ddx-safety-audit` / subagent `ddx-guardian` | `01_zero_point.md` |
| 1.1.1–1.1.2 | Аннотация + кодирование 6D/8D | Skill `ddx-annotate-and-code` | `02_annotations/*` |
| 1.1.3 | Линзы (deferred selection, плюрализм) | Skill `ddx-annotate-and-code` + `ddx-run-analysis` | `04_lenses.md` |
| App.6 | Primary/Counter + integration rule | Rule `ddx-lens-selection-framework.mdc` | `04_lenses.md` |
| 1.1.4 | Операционализация (словари) | Skill `ddx-hypothesis-lifecycle` | (обычно внутри H-cards / Operational Dictionary) |
| 2.1.1 | Exploration → Data Map | Skill `ddx-run-analysis` | `03_data_map.md` |
| 2.1.2 | Fast Falsification | Skill `ddx-hypothesis-lifecycle` | `06_hcards/*` + `07_evidence_matrix.md` |
| 1.3.4 | Bayes update (qualitative) | Skill `ddx-hypothesis-lifecycle` | `06_hcards/*` |
| 1.3.3 | Covariation (correlation/discrimination) | Skill `ddx-hypothesis-lifecycle` | `08_hypothesis_map.md` (+ ссылки A-ID) |
| App.5 | Evidence Matrix / Hypothesis Map / Timeline | Rule `ddx-smh-advanced.mdc` + Skill templates | `07_evidence_matrix.md`, `08_hypothesis_map.md`, `09_hypothesis_timeline.md` |
| App.3 | Validation Funnel (AHP + centrality) | Skill templates | `10_validation_funnel.md` |
| Guide 2.2–2.3 | Deep/serial scenarios | Rule `ddx-deep-scenarios.mdc` / subagent `ddx-synthesizer` | `11_deep_scenarios.md` |
| App.2 Mode 2 | Resource analysis | Skill `ddx-activate-mode` / subagent `ddx-resource-finder` | `12_resources_mode2.md` |
| App.2 Mode 3 | Reliability passport | Skill `ddx-activate-mode` / subagent `ddx-critic` | `13_reliability_passport_mode3.md` |
| 3.3.3 | Conclusion template | Skill `ddx-write-conclusion` | `14_conclusion_3_3_3.md` |
| App.1 | 8D axes + C1–C3 | Skill `ddx-contextual-8d` / subagent `ddx-contextualizer` | `08d_context.md` |
| 0.4.3 | Sufficiency/stop criteria | Rule `ddx-phases.mdc` | `15_coverage_check.md` (примечания) |

