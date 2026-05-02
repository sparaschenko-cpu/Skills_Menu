# Атомарный чеклист 16 координат

Используй этот файл как матрицу полноты. Для каждой координаты проверь: главный вопрос, обязательные artifacts, атомарные проверки, блокирующие дефекты.

## 1. Назначение

Главный вопрос: зачем принимается экспертное решение?

Artifacts:
- intended use;
- allowed use;
- prohibited use;
- primary expert/diagnostic/decision-support question;
- affected parties;
- expected outcome;
- non-use conditions.

Atomic checks:
- Назначение не сведено к функции модели, метрике или классификации.
- Указано, что решение должно изменить: маршрут, доступ, приоритет, review, release, restriction, intervention.
- Есть явные prohibited uses.
- Есть условия, при которых решение не должно применяться.

Blocking defects:
- Decision purpose не определен.
- Prohibited use отсутствует при consequential decision.
- Intended use шире evidence, validation or authority scope.

## 2. Объект и цель решения

Главный вопрос: что является объектом, а что именно устанавливается?

Artifacts:
- target object;
- diagnostic/decision target;
- unit of observation;
- unit of analysis;
- unit of decision.

Atomic checks:
- Object, target and units названы отдельно.
- Unit of decision не подменена unit of observation.
- Target не смешивает состояние, риск, маршрут, вину, качество и действие.
- Scope исключает близкие, но другие targets.

Blocking defects:
- Невозможно понять, к чему применяется решение.
- Решение применяется к объекту, который не является unit of decision.

## 3. Смысл и определения

Главный вопрос: какие различения нужны, чтобы решение не было предметно ложным?

Artifacts:
- concept register;
- definition register;
- termbase;
- construct/facet/attribute register;
- semantic boundary register;
- prohibited conflation register;
- prohibited inference register.

Atomic checks:
- Каждый ключевой term имеет concept and definition.
- Есть границы между близкими понятиями.
- Запрещенные смешения названы явно.
- Запрещенные inference названы явно.

Blocking defects:
- Claim использует неопределенный concept.
- Решение основано на запрещенном смешении понятий.

## 4. Источники

Главный вопрос: из чего эксперт имеет право брать материал?

Artifacts:
- source frame;
- source register;
- excluded source register;
- provenance notes;
- source quality status;
- sensitivity/privacy/security classification.

Atomic checks:
- Каждый source входит в source frame.
- Для source есть provenance: кто, когда, как создал/собрал/изменял.
- Sensitive sources имеют ограничения использования.
- Excluded sources не поддерживают claim.

Blocking defects:
- Claim опирается на source вне source frame.
- Provenance отсутствует там, где без него нельзя оценить evidence.

## 5. Наблюдение и evidence

Главный вопрос: что именно наблюдалось и почему это evidence?

Artifacts:
- source segment rules;
- locator convention;
- context unit rules;
- evidence unit definition;
- evidence unit boundary tests;
- missingness taxonomy;
- ambiguity taxonomy;
- deferral rules.

Atomic checks:
- Source segment имеет locator.
- Evidence unit атомарна.
- Observation отделена от interpretation.
- Missing не выдано за absent.
- Ambiguity классифицирована.

Blocking defects:
- Evidence unit неатомарна и поддерживает сильный claim.
- Claim построен на interpretation, представленной как observation.

## 6. Кодирование или экспертная категоризация

Главный вопрос: как наблюдаемое получает управляемую метку?

Artifacts:
- code system;
- code family map;
- code passport register;
- labelbook;
- scale/anchor register;
- code assignment record;
- adjudication protocol;
- reliability plan;
- migration crosswalk.

Atomic checks:
- Каждый code имеет definition, inclusion, exclusion, examples, counterexamples, prohibited uses.
- Label не используется вместо полного code object.
- Assignment связан с target unit.
- Есть adjudication для расхождений.

Blocking defects:
- Code превращен в diagnosis/claim/decision без rules.
- Нет anchors для scale, используемой в claim/action.

## 7. Агрегация и output

Главный вопрос: как отдельные observations/codes превращаются в output?

Artifacts:
- aggregation contract register;
- metric contract register;
- denominator rules;
- threshold object register;
- output type register;
- uncertainty propagation rules;
- output interpretation limits.

Atomic checks:
- Aggregation contract описан.
- Denominator определен.
- Threshold не скрыт в коде/обычае/устной договоренности.
- Output interpretation limits названы.
- Uncertainty propagation учитывает слабые inputs.

Blocking defects:
- Output принят за claim.
- Score/probability/confidence принят за permission.

## 8. Доказательный вывод

Главный вопрос: что можно утверждать на основании evidence and output?

Artifacts:
- hypothesis register;
- alternative hypothesis register;
- evidence relation register;
- counterevidence register;
- falsification matrix;
- evidence class matrix;
- epistemic stage register;
- claim rule register.

Atomic checks:
- Claim имеет scope.
- Evidence relation названа.
- Alternative hypotheses рассмотрены.
- Counterevidence найдено или явно искалось.
- Falsification tests определены.
- Evidence freshness учтена.

Blocking defects:
- Claim scope шире evidence scope.
- Counterevidence проигнорировано.
- Epistemic stage перепутана с release status.

## 9. Решение и действие

Главный вопрос: что можно делать с claim?

Artifacts:
- claim eligibility matrix;
- decision taxonomy;
- decision eligibility matrix;
- use constraint register;
- automation boundary matrix;
- human oversight mode register;
- override rule register;
- recourse path.

Atomic checks:
- Recommendation отделена от decision.
- Use constraints привязаны к claim/output.
- Automation boundary описана.
- Human oversight mode выбран.
- Override и recourse доступны.

Blocking defects:
- Действие выполняется без authority.
- Consequential decision не имеет recourse.
- Confidence используется как permission.

## 10. Качество

Главный вопрос: насколько надежно решение и в каких пределах?

Artifacts:
- quality dimension register;
- validation plan;
- reliability plan;
- calibration plan;
- uncertainty model;
- robustness/transportability assessment;
- completeness checklist;
- quality defect register.

Atomic checks:
- Validity, reliability, accuracy, calibration and uncertainty не смешаны.
- Validation scope сравнен с deployment/use scope.
- Transportability проверена для данного context.
- Quality defects имеют operational effect.

Blocking defects:
- Validation scope уже intended use scope.
- Quality сведено к одному confidence/accuracy score.

## 11. Риск и вред

Главный вопрос: что может пойти не так и кому может быть причинен вред?

Artifacts:
- harm taxonomy;
- hazard taxonomy;
- risk scenario register;
- false positive/false negative harm model;
- mitigation/control register;
- residual risk register;
- risk acceptance record;
- safety case;
- recourse and reversibility rules.

Atomic checks:
- False positive and false negative harms рассмотрены отдельно.
- Residual risk не выдан за accepted risk.
- Risk acceptance имеет authority.
- Reversibility оценена.
- Safety case не сведен к mitigation list.

Blocking defects:
- Consequential decision без risk acceptance.
- Невозможность исправить ошибку не отражена в risk profile.

## 12. Governance и ответственность

Главный вопрос: кто имеет право создавать, проверять, утверждать, выпускать, останавливать и пересматривать?

Artifacts:
- actor register;
- role contract register;
- RACI;
- review matrix;
- approval matrix;
- authority boundary register;
- veto right register;
- conflict of interest register;
- independence register.

Atomic checks:
- Responsible, accountable, reviewer, approver and authority различены.
- Reviewer independence проверена.
- Conflict of interest описан.
- Veto-holder известен.

Blocking defects:
- Review принят за approval.
- Responsibility принята за authority.
- Expertise принята за independence.

## 13. Контроль и gates

Главный вопрос: где решение должно быть проверено или остановлено?

Artifacts:
- gate family register;
- gate sheet set;
- gate run register;
- stop-rule register;
- waiver rule register;
- exception rule register;
- CAPA rule register;
- enforcement layer.

Atomic checks:
- Gate имеет inputs, pass/fail criteria, reviewer, approver, release effect, audit record.
- Stop-rules исполнимы.
- Waiver ограничен scope/time/authority.
- Exception не является молчаливым обходом.
- CAPA имеет owner and acceptance criteria.

Blocking defects:
- Нет gate перед release/use.
- Waiver используется как постоянное разрешение.
- Checklist выдан за gate.

## 14. Время и изменения

Главный вопрос: как решение стареет, меняется, деградирует и пересматривается?

Artifacts:
- lifecycle model;
- change request process;
- change impact matrix;
- revalidation triggers;
- monitoring signal register;
- incident register;
- rollback rules;
- retirement rules.

Atomic checks:
- Lifecycle phase известна.
- Change classes различены: semantic, evidence, threshold, model, policy, release-critical.
- Drift and incident triggers ведут к revalidation.
- Rollback возможен и ответственен.

Blocking defects:
- Release допускается без monitoring/revalidation triggers.
- Threshold/source/schema changes не считаются potentially release-critical.

## 15. Машинная форма

Главный вопрос: как человекочитаемый смысл сохраняется в records, schemas and validators?

Artifacts:
- schema registry;
- object schemas;
- field dictionary;
- enum registry;
- referential integrity rules;
- validation rules;
- migration rules;
- schema parity tests.

Atomic checks:
- Human-readable specification отражена в schema.
- Enums совпадают с semantic boundaries.
- Validators проверяют не только форму, но и ключевые constraints.
- Migration не меняет смысл молча.

Blocking defects:
- Schema validity принята за semantic validity.
- Schema меняет meaning без human-readable supersession.

## 16. Трассируемость и аудит

Главный вопрос: можно ли восстановить путь от материала до решения?

Artifacts:
- traceability model;
- trace edge catalog;
- evidence ledger;
- step log;
- audit record schema;
- audit pack;
- reconstructability test.

Atomic checks:
- Есть trace edges между source, evidence, code/output, claim, decision and release.
- Evidence ledger содержит locators and provenance.
- Step log фиксирует review/calculation/change/release.
- Audit pack позволяет независимую проверку.
- Reconstructability test пройден или запланирован.

Blocking defects:
- Аудитор не может восстановить путь от source material до release/use condition.
- Audit record не связывает decision with evidence and authority.
