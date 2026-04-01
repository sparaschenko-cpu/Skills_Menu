# ddxcmd-phase2-validation-funnel — Funnel (App.3): pruning → prioritization → decisive falsification

## Назначение

Используй, когда поле гипотез стало слишком большим/шумным (например >7–10) или требуется формальная приоритизация и “решающее” тестирование гипотез.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- (опционально) `scope=` (например: `stage1-only`, `stage2-only`, `full`)

## Входы

- `Artifacts/DDx/<case_id>/05_smh_registry.md`
- `Artifacts/DDx/<case_id>/07_evidence_matrix.md`
- Rules: `.cursor/rules/ddx-validation-funnel.mdc`
- Skill: `/ddx-hypothesis-lifecycle` (шаблоны/worksheet AHP + Centrality уже внутри skill/templates)

## Шаги

1. Убедись, что SMH-реестр актуален: гипотезы имеют IDs и статусы.
2. Выполни Funnel Stage 1 (Pruning):
   - фильтры: фальсифицируемость / правдоподобие / релевантность / добавленная ценность
3. Выполни Funnel Stage 2 (Prioritization):
   - AHP (текстовые попарные сравнения по критериям)
   - Centrality (degree/betweenness) и интерпретация
4. Выполни Funnel Stage 3 (Decisive falsification):
   - для топ-гипотез сформулируй “решающий тест”/дискриминатор
   - обнови Evidence Matrix и статусы в SMH
5. Сохрани итоговый worksheet Funnel в `10_validation_funnel.md` (включая ссылки на `Manual/App` секции).

## Выходы (Artifacts/DDx/<case_id>/...)

- `10_validation_funnel.md` (обновлён/создан)
- Обновления в `05_smh_registry.md` и при необходимости `07_evidence_matrix.md`

## Quality gates

- В Stage 2 нет “магических чисел”: только текстовая аргументация и прозрачные критерии.
- Любая решающая проверка привязана к A-ID и/или ясной процедуре поиска в материале.
