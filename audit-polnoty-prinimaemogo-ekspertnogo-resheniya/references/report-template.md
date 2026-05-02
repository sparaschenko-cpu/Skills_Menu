# Шаблоны результата

## 1. Итоговый отчет аудита

Используй этот шаблон, если пользователь просит провести аудит.

```markdown
# Аудит полноты принимаемого экспертного решения

## Итог аудита

**Verdict:** ready | conditional | not ready | blocked | insufficient material

**Короткое основание:** 2-5 предложений. Укажи, что именно мешает или разрешает дальнейшее использование.

**Главный риск неполноты:** одно предложение.

## Карта решения

| Узел | Что установлено | Статус | Комментарий |
|---|---|---|---|
| Решение / claim | ... | complete/partial/missing | ... |
| Object | ... | ... | ... |
| Target | ... | ... | ... |
| Evidence package | ... | ... | ... |
| Proposed action | ... | ... | ... |
| Review | ... | ... | ... |
| Authority | ... | ... | ... |
| Risk acceptance | ... | ... | ... |
| Release/use condition | ... | ... | ... |
| Traceability | ... | ... | ... |

## Критические дефекты

| Severity | Дефект | Где проявляется | Почему важно | Что исправить |
|---|---|---|---|---|
| BLOCKING/MAJOR | ... | ... | ... | ... |

Если критических дефектов нет, напиши: "Blocking/Major дефекты по предоставленному материалу не выявлены", но не скрывай minor gaps.

## Атомарная матрица полноты

| Координата | Статус | Severity | Evidence | Gap | Required fix |
|---|---|---|---|---|---|
| 1. Назначение | ... | ... | ... | ... | ... |
| 2. Объект и цель | ... | ... | ... | ... | ... |
| 3. Смысл | ... | ... | ... | ... | ... |
| 4. Источники | ... | ... | ... | ... | ... |
| 5. Evidence | ... | ... | ... | ... | ... |
| 6. Кодирование | ... | ... | ... | ... | ... |
| 7. Output | ... | ... | ... | ... | ... |
| 8. Claim | ... | ... | ... | ... | ... |
| 9. Решение | ... | ... | ... | ... | ... |
| 10. Качество | ... | ... | ... | ... | ... |
| 11. Риск | ... | ... | ... | ... | ... |
| 12. Governance | ... | ... | ... | ... | ... |
| 13. Gates | ... | ... | ... | ... | ... |
| 14. Изменения | ... | ... | ... | ... | ... |
| 15. Schema | ... | ... | ... | ... | ... |
| 16. Audit | ... | ... | ... | ... | ... |

## Трасса решения

Опиши восстановленную или недостающую трассу:

```text
source material -> source segment -> context unit -> evidence unit -> code/output -> evidence relation -> hypothesis -> claim -> use constraint -> decision support -> human review -> authority decision -> release/use condition -> audit record
```

Для каждого разрыва укажи:
- missing edge;
- affected claim/action;
- required artifact;
- responsible role.

## Недостающие evidence и artifacts

Сгруппируй по dependency order:

1. Object/target/scope.
2. Sources/provenance/evidence.
3. Claim boundaries/counterevidence/falsification.
4. Use constraints/authority/risk acceptance.
5. Gates/traceability/monitoring.

## Условия допустимого использования

Укажи:
- что можно делать сейчас;
- что нельзя делать сейчас;
- какие conditions before use обязательны;
- кто должен review;
- кто должен approve;
- какие stop-rules действуют.

## Следующие шаги

Дай последовательный план исправлений:

1. ...
2. ...
3. ...

Каждый шаг должен иметь artifact/output, owner role, acceptance criterion.
```

## 2. Короткий отчет

Если пользователь просит кратко:

```markdown
**Verdict:** ready | conditional | not ready | blocked | insufficient material

**Почему:** ...

**Blocking/Major gaps:**
- ...

**Что нужно до допустимого use:**
1. ...
2. ...
3. ...

**Главное ограничение claim:** ...
```

## 3. Master Prompt для другой модели

Если пользователь просит создать prompt, используй этот шаблон и заполни его конкретным контекстом пользователя. Не оставляй незаполненных placeholder.

```text
Ты действуешь как Decision Completeness Auditor.

Твоя задача: провести исчерпывающий последовательный аудит полноты принимаемого экспертного решения. Ты не принимаешь решение за authority и не подменяешь предметного эксперта. Ты проверяешь, достаточно ли полно описан путь от источников и evidence до claim, допустимого действия, ответственности, release/use condition, аудита и пересмотра.

Контекст решения:
[вставь конкретный контекст пользователя без placeholder]

Материалы для аудита:
[вставь список предоставленных материалов, evidence, источников, аргументов]

Обязательная логика аудита:
1. Сначала восстанови карту решения: decision statement, object, target, evidence package, proposed action, review, authority, risk, release/use condition, traceability.
2. Затем восстанови трассу: source material -> source segment -> context unit -> evidence unit -> code/output -> evidence relation -> hypothesis -> claim -> use constraint -> decision support -> human review -> authority decision -> release/use condition -> audit record.
3. Для каждого перехода проверь: input, operation, output, rule, owner/reviewer, evidence, defect.
4. Проверь 16 координат: назначение; объект и target; смысл; источники; observation/evidence; coding/categorization; aggregation/output; доказательный вывод; решение/action; качество; риск/harm; governance; gates; lifecycle/change; machine-readable schema; traceability/audit.
5. Отделяй material от evidence, observation от interpretation, code/output от claim, recommendation от decision, review от approval, confidence от permission, mitigation от risk acceptance, readiness от release.
6. Помечай каждый элемент статусом: complete, partial, missing, implicit, deferred, not_applicable, prohibited.
7. Классифицируй дефекты: BLOCKING, MAJOR, MINOR, NOTE.
8. Применяй stop-rules: claim не может быть шире evidence; confidence не дает permission; decision требует authority; review не равен approval; residual risk не равен accepted risk; release/use condition не равен readiness; trace must be reconstructable.

Вердикт выбери только один:
- ready: нет blocking/major дефектов, trace восстановима, use constraints and authority ясны.
- conditional: нет blocking дефектов, но есть major defects с conditions before use.
- not ready: есть major defects, мешающие responsible use.
- blocked: есть хотя бы один blocking defect.
- insufficient material: данных недостаточно для восстановления карты решения.

Формат ответа:
1. Итог аудита.
2. Карта решения.
3. Критические дефекты.
4. Атомарная матрица полноты по 16 координатам.
5. Трасса решения и разрывы traceability.
6. Недостающие evidence/artifacts.
7. Условия допустимого использования.
8. Следующие шаги с artifact, owner role, acceptance criterion.

Не раскрывай скрытые рассуждения. Показывай только выводы, проверочные вопросы, матрицы, дефекты, обоснование и actionable steps.
```
