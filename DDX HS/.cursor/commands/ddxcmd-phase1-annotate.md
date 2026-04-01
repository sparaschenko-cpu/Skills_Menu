# ddxcmd-phase1-annotate — Phase 1: аннотирование, коды, Data Map, линзы

## Назначение

Используй для выполнения Exploration (Phase 1): разметка корпуса (A-####), первичные коды/категории, карта данных, первичный выбор линз.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- `input=` (обязательно: что анализируем / где материал)
- (опционально) `protocol=basic|standard|max`
- (опционально) `scope=` (например: `sample-first`, `full-corpus`, `no-lenses`)

## Входы

- `Artifacts/DDx/<case_id>/02_annotations/` (index + чанки)
- `Artifacts/DDx/<case_id>/03_data_map.md`
- `Artifacts/DDx/<case_id>/04_lenses.md`
- Skill: `/ddx-annotate-and-code`
- Subagent (рекомендовано при больших корпусах): `/ddx-explorer`
- Rules: `.cursor/rules/ddx-annotation.mdc`, `.cursor/rules/ddx-lens-selection-framework.mdc`

## Шаги

1. Если кейс не инициализирован — выполни `/ddxcmd-new-case case_id=...`.
2. Выбери стратегию аннотирования:
   - **basic**: минимально достаточный проход (акцент на Axis3/Axis5 + выводы)
   - **standard/max**: полный проход, index-first + chunking обязательно
3. Выполни аннотирование:
   - Вызови `/ddx-annotate-and-code` (шаблон + дисциплина кодирования).
   - Для больших корпусов делегируй `/ddx-explorer` и требуй **index-first** (Summary → Index → Top evidence → Chunk plan → Payload).
4. Сохраняй в артефакты:
   - Обновляй `02_annotations/index.md` (список A-IDs, топ-репрезентанты, anomalies-first).
   - Записывай чанки в `02_annotations/A-0001_A-0100.md` и далее по мере роста.
5. Построй/обнови `03_data_map.md` (коды → кластеры → “горячие точки”, противоречия, аномалии).
6. Если `scope` не запрещает линзы:
   - Зафиксируй в `04_lenses.md` (Primary/Counter, Paradigm→Theory→Construct→Model).
   - Важно: любые линзовые интерпретации помечай повышенным риском (Axis5).
7. Заверши Phase 1 “выходом”:
   - список ключевых `A-####` (10–25)
   - список аномалий/контрпримеров
   - предварительные понятия/кластеры, готовые для Phase 2

## Выходы (Artifacts/DDx/<case_id>/...)

- `02_annotations/index.md` + 1+ чанков `02_annotations/A-....md`
- `03_data_map.md`
- `04_lenses.md` (если применялись линзы)

## Quality gates

- Не возвращать “весь корпус” в чат: только index-first + chunk plan.
- Каждая существенная интерпретация квалифицирована Axis5 и при необходимости привязана к A-ID.
