# ddxcmd-gp2 — Guardian checkpoint GP-2 (per-hypothesis safety in Phase 2)

## Назначение

Проверка безопасности в Phase 2: pathology→adaptive обязательство, contextual grounding, high-risk management.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- (опционально) `scope=` (например: `target=H-0007`, `all-pathology`)

## Входы

- Subagent: `/ddx-guardian`
- Rule: `.cursor/rules/ddx-safety.mdc`
- Артефакты: `Artifacts/DDx/<case_id>/05_smh_registry.md`, `Artifacts/DDx/<case_id>/06_hcards/index.md` (+ чанки)

## Шаги

1. Вызови `/ddx-guardian` и попроси провести GP-2:
   - для каждой H-Pathology: существует ли H-Adaptive (функциональная гипотеза)?
   - есть ли contextual grounding (ограничение обобщений, контекстные допущения)?
   - соблюдается ли протокол high-risk (Axis5=4–5 не становится “центральным” без независимых подтверждений)?
2. Сохрани результаты:
   - кратко в `05_smh_registry.md` (пометки статуса/флагов)
   - при необходимости — в соответствующих H-card (чанк `06_hcards/...`)

## Выходы (Artifacts/DDx/<case_id>/...)

- Обновления в `05_smh_registry.md` и/или H-cards.

## Quality gates

- Любое нарушение = явная блокировка центральных выводов до исправления.
