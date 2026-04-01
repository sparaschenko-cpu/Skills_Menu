# ddxcmd-mode1 — Mode 1 (Adversarial) при тупике/неразрешимости

## Назначение

Используй, когда Phase 2 зашла в тупик (H1 vs H0 неразрешимо) или нужна состязательная генерация альтернатив.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- (опционально) `scope=` (например: `target=H-0012`, `long-list`, `short-list-only`)

## Входы

- Subagent: `/ddx-challenger`
- Skill: `/ddx-activate-mode`
- Rules: `.cursor/rules/ddx-advanced-modes.mdc`, `.cursor/rules/ddx-adversarial-heuristics.mdc`, `.cursor/rules/ddx-adversarial-transfer-paradox.mdc`
- Артефакты: `05_smh_registry.md`, `07_evidence_matrix.md`, `08_hypothesis_map.md`

## Шаги

1. Вызови Skill `/ddx-activate-mode` и формально активируй Mode 1 (мишень, риск, план возврата).
2. Делегируй генерацию альтернатив:
   - вызови `/ddx-challenger` и потребуй **index-first** (long list → short list → финалисты + план валидации).
3. Сохрани результаты в:
   - `05_smh_registry.md` (H-Adv как отдельные гипотезы со статусами и Axis5=4–5 по умолчанию)
   - обнови `07_evidence_matrix.md` (H1 vs H-Adv)
4. Safety gate Mode 1:
   - вызови `/ddx-guardian` (GP-3 логика) и зафиксируй ограничения high-risk.

## Выходы (Artifacts/DDx/<case_id>/...)

- обновлённые `05_smh_registry.md`, `07_evidence_matrix.md` (+ при необходимости `08_hypothesis_map.md`)

## Quality gates

- Все H-Adv помечены Axis5=4–5 до появления независимых подтверждений.
