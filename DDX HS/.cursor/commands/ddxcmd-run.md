# ddxcmd-run — полный DDx-анализ (оркестрация)

## Назначение

Используй для полного сквозного DDx-анализа от входного текста до заключения, с сохранением артефактов в `Artifacts/DDx/<case_id>/...`.

## Параметры

- `case_id=YYYYMMDD_slug` (обязательно)
- `input=` (обязательно: что анализируем / где материал)
- (опционально) `protocol=basic|standard|max`
- (опционально) `scope=` (например: `no-8d`, `phase1-only`, `phase2-only`)

## Входы

- Canonical: `Manual/DDx-6D_8D.md`
- Rules: `.cursor/rules/ddx-workspace-context.mdc`
- Skills: `/ddx-run-analysis`

## Шаги

1. Если кейс ещё не создан — выполни `/ddxcmd-new-case case_id=...`.
2. Запусти основной процедурный оркестратор: **вызови Skill** `/ddx-run-analysis` и следуй ему как “backbone”.
3. Для контекст-изоляции делегируй фазы субагентам (как в `.cursor/rules/ddx-workspace-context.mdc`):
   - Phase 1: `/ddx-explorer`
   - Phase 2: `/ddx-analyst`
   - Phase 3: `/ddx-synthesizer`
   - Safety gates: `/ddx-guardian` по контрольным точкам
4. Всегда соблюдай **index-first output** от субагентов: сохраняй payload в файлы кейса по чанкам, обновляй `index.md` в подпапках.
5. В конце обнови `15_coverage_check.md` (или запусти `/ddxcmd-coverage-check case_id=...`).

## Выходы (Artifacts/DDx/<case_id>/...)

- Минимальный пакет артефактов (A/B/C) + расширения по триггерам (8D/Mode 1–3/Max).

## Quality gates

- Ни один “сильный” вывод без `Axis5` и без ссылок на `A-####`.
- Корпуса не возвращать “одним куском” в чат: только index-first + chunk plan.
