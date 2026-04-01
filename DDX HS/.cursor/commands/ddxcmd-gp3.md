# ddxcmd-gp3 — Guardian checkpoint GP-3 (post-adversarial)

## Назначение

Проверка после Mode 1 (Adversarial): Axis5-ограничения, недопущение “опасных” альтернатив в роли фактов.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)

## Входы

- Subagent: `/ddx-guardian`
- Rule: `.cursor/rules/ddx-safety.mdc`
- Артефакты: `Artifacts/DDx/<case_id>/05_smh_registry.md`, `Artifacts/DDx/<case_id>/07_evidence_matrix.md`

## Шаги

1. Вызови `/ddx-guardian` и попроси провести GP-3:
   - все H-Adv помечены Axis5=4–5 по умолчанию?
   - есть ли явная процедура валидации H1 vs H-Adv (а не “замена по впечатлению”)?
2. Сохрани результат в `05_smh_registry.md` (заметки/флаги) и при необходимости в `07_evidence_matrix.md` (remediation-список).

## Выходы (Artifacts/DDx/<case_id>/...)

- Обновления в `05_smh_registry.md` (и/или `07_evidence_matrix.md`).

## Quality gates

- Ни одна high-risk альтернатива не становится центральной без независимого подтверждения.
