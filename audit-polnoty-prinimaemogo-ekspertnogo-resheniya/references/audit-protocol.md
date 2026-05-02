# Протокол аудита полноты экспертного решения

## 0. Рамка аудита

Аудит полноты проверяет не "истинность мира", а управляемость экспертного перехода:

```text
неопределенная ситуация
-> source material
-> source segment
-> context unit
-> evidence unit
-> code/output
-> evidence relation
-> hypothesis
-> claim
-> use constraint
-> recommendation / decision support
-> human review
-> authority decision
-> release/use condition
-> audit record
-> monitoring / incident / CAPA / revision
```

Главная проверка: каждый переход должен быть назван, ограничен, проверен и трассируем.

## 1. Intake

Сначала восстанови карту запроса:

1. `decision_statement`: какое решение, рекомендация или заключение проверяется.
2. `decision_context`: домен, ситуация, stakes, affected parties.
3. `decision_target`: что должно быть установлено или разрешено.
4. `proposed_action`: что хотят сделать после решения.
5. `authority`: кто имеет право утвердить действие.
6. `evidence_package`: какие sources, observations, documents, data, expert notes есть.
7. `time_boundary`: период, свежесть, deadline, lifecycle phase.
8. `risk_profile`: какой вред возможен при false positive, false negative, delay, misuse.
9. `audit_scope`: что входит и что не входит в аудит.
10. `known_constraints`: legal, ethical, privacy, security, technical, organizational.

Если элемент не указан, не выдумывай. Пометь статусом `missing` или `implicit`.

## 2. Построение карты решения

Собери короткую таблицу:

| Узел | Содержание | Статус | Комментарий |
|---|---|---|---|
| Object | К чему применяется решение | complete/partial/missing | ... |
| Target | Что устанавливается | complete/partial/missing | ... |
| Evidence | Что поддерживает target | complete/partial/missing | ... |
| Claim | Что утверждается | complete/partial/missing | ... |
| Action | Что предполагается делать | complete/partial/missing | ... |
| Authority | Кто разрешает действие | complete/partial/missing | ... |
| Risk | Какой вред возможен | complete/partial/missing | ... |
| Trace | Можно ли восстановить путь | complete/partial/missing | ... |

Не переходи к verdict, пока не отделены `claim`, `recommendation`, `decision` и `release/use condition`.

## 3. Аудит трассы решения

Проверяй трассу последовательно. Для каждого шага фиксируй:

- `input`: что входит в шаг.
- `operation`: какой переход сделан.
- `output`: что появилось после шага.
- `rule`: какое правило разрешает переход.
- `owner/reviewer`: кто отвечает за шаг.
- `evidence`: чем подтверждено, что шаг выполнен.
- `defect`: что отсутствует, смешано или не трассируется.

### 3.1 Source -> source segment

Проверь:
- источник входит в source frame;
- provenance известен;
- есть locator для фрагмента;
- sensitivity/privacy/security ограничения учтены;
- excluded sources не использованы.

Блокирующий дефект: claim опирается на источник вне source frame или без provenance, когда provenance нужен для доверия.

### 3.2 Source segment -> context unit -> evidence unit

Проверь:
- наблюдение отделено от интерпретации;
- evidence unit атомарна;
- контекст не потерян;
- missingness не выдан за absence;
- ambiguity явно описана.

Блокирующий дефект: evidence unit объединяет несколько независимых содержаний так, что на ней строится сильный claim.

### 3.3 Evidence unit -> code/output

Проверь:
- code имеет definition, inclusion, exclusion, examples, counterexamples, prohibited uses;
- assignment записан и имеет target unit;
- disagreements/adjudication оформлены;
- output не выдан за claim;
- thresholds и denominators не скрыты.

Блокирующий дефект: score/class/output становится основанием действия без claim rule и use constraint.

### 3.4 Output -> hypothesis -> claim

Проверь:
- claim scope не превышает evidence scope;
- есть alternative hypotheses;
- есть counterevidence или явная отметка, что его не найдено;
- есть falsification tests;
- evidence relation названа: supports, weakens, contradicts, discriminates, contextualizes, qualifies;
- uncertainty не замаскирована confidence.

Блокирующий дефект: claim сформулирован сильнее, чем позволяет evidence.

### 3.5 Claim -> recommendation -> decision/action

Проверь:
- есть use constraint;
- recommendation не равна decision;
- automation boundary определена;
- human oversight mode определен;
- override и recourse описаны;
- affected parties и reversibility учтены.

Блокирующий дефект: consequential action выполняется без authority или без recourse.

### 3.6 Review -> approval -> release/use

Проверь:
- reviewer и approver различены;
- authority boundary описан;
- conflict of interest и independence проверены;
- gates имеют inputs, pass/fail criteria, effect and audit record;
- release/use condition отделена от readiness;
- waiver/exception/CAPA не смешаны.

Блокирующий дефект: review принят за approval или conditional pass превращен в бессрочное разрешение.

### 3.7 Use -> monitoring -> revision

Проверь:
- есть monitoring signals;
- drift/revalidation triggers заданы;
- incident path и rollback rules описаны;
- CAPA имеет owner, due date, acceptance criteria;
- audit record позволяет восстановить историю.

Блокирующий дефект: решение допускает use, но не имеет условий пересмотра при change, drift or incident.

## 4. Проверка 16 координат

После трассы проверь 16 координат из `coordinate-checklist.md`. Для каждой координаты дай:

```text
[координата]
status: complete | partial | missing | implicit | deferred | not_applicable | prohibited
severity: BLOCKING | MAJOR | MINOR | NOTE
evidence: что найдено
gap: чего не хватает
required_fix: что сделать
```

Если времени мало, не пропускай координаты молча. Отметь кратко.

## 5. Матрица полноты

Итоговая оценка полноты должна включать:

| Плоскость | Вопрос | Статус | Дефект | Исправление |
|---|---|---|---|---|
| Meaning | Что различаем? | ... | ... | ... |
| Evidence | На каком основании? | ... | ... | ... |
| Claim | Что можно утверждать? | ... | ... | ... |
| Action | Что можно делать? | ... | ... | ... |
| Authority | Кто имеет право? | ... | ... | ... |
| Risk | Кто несет риск? | ... | ... | ... |
| Trace | Как восстановить путь? | ... | ... | ... |

## 6. Правила вердикта

Выбери один verdict:

- `ready`: нет blocking/major дефектов; trace восстановима; use constraints и authority ясны.
- `conditional`: нет blocking дефектов, но есть major defects с ясными conditions before use.
- `not ready`: есть major defects, которые мешают responsible use, но не показывают немедленный запрет.
- `blocked`: есть хотя бы один blocking defect.
- `insufficient material`: данных недостаточно даже для восстановления карты решения.

Нельзя ставить `ready`, если:
- есть `missing` в evidence, claim scope, authority, risk acceptance или traceability;
- risk mitigation описана без risk acceptance;
- use/release не имеет gate или authority;
- audit trail не позволяет восстановить решение.

## 7. Приоритизация исправлений

Предлагай исправления в порядке зависимостей:

1. Уточнить object, target, scope.
2. Закрыть source/evidence/provenance gaps.
3. Ограничить claim.
4. Проверить alternatives/counterevidence/falsification.
5. Описать use constraints and automation boundary.
6. Назначить review/approval/authority/veto.
7. Оформить risk acceptance and safety case.
8. Поставить gates, stop-rules, CAPA.
9. Создать traceability/audit pack.
10. Определить monitoring/revalidation/rollback.

## 8. Язык аудита

Формулируй строго:

- Не "решение неверное", а "путь к решению неполон в таких-то узлах".
- Не "нет доказательств", а "в предоставленном пакете отсутствуют evidence units, которые поддерживают claim X".
- Не "можно использовать", а "use допустим только при conditions A/B/C и authority D".
- Не "эксперт уверен", а "confidence не создает permission; требуется authority/review/release condition".
