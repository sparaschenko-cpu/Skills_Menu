# Атомарный чеклист 16 координат

Используй этот файл как матрицу полноты. Для каждой координаты фиксируй:

- `status`: complete | partial | missing | implicit | deferred | out_of_scope | not_applicable | prohibited
- `severity`: BLOCKING | MAJOR | MINOR | NOTE
- `evidence`: что подтверждает статус
- `gap`: чего не хватает
- `required_fix`: минимальный шаг исправления

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

Required evidence for status `complete`:
- явный документ назначения;
- список prohibited use;
- связь intended use с scope evidence и authority.

Atomic checks:
- Назначение не сведено к функции модели, метрике или классификации.
- Указано, что решение должно изменить: маршрут, доступ, приоритет, review, release, restriction, intervention.
- Есть явные prohibited use.
- Есть условия non-use.

Blocking defects:
- decision purpose не определен;
- prohibited use отсутствует при consequential decision;
- intended use шире evidence, validation или authority scope.

## 2. Объект и цель решения

Главный вопрос: что является объектом, а что именно устанавливается?

Artifacts:
- target object;
- diagnostic/decision target;
- unit of observation;
- unit of analysis;
- unit of decision.

Required evidence for status `complete`:
- отдельные определения object, target и всех unit;
- связь target с unit of decision.

Atomic checks:
- Object, target и units названы отдельно.
- Unit of decision не подменена unit of observation.
- Target не смешивает состояние, риск, маршрут, вину, качество и действие.
- Scope исключает близкие, но другие targets.

Blocking defects:
- невозможно понять, к чему применяется решение;
- решение применяется к объекту, который не является unit of decision.

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

Required evidence for status `complete`:
- ключевые claims используют только определенные concepts;
- semantic boundaries и prohibited inference задокументированы.

Atomic checks:
- Каждый ключевой term имеет concept and definition.
- Есть границы между близкими понятиями.
- Запрещенные смешения названы явно.
- Запрещенные inference названы явно.

Blocking defects:
- claim использует неопределенный concept;
- решение основано на запрещенном смешении понятий.

## 4. Источники

Главный вопрос: из чего эксперт имеет право брать материал?

Artifacts:
- source frame;
- source register;
- excluded source register;
- provenance notes;
- source quality status;
- sensitivity/privacy/security classification.

Required evidence for status `complete`:
- каждый source связан с provenance;
- sensitive sources имеют use restrictions;
- excluded sources не участвуют в support для claim.

Atomic checks:
- Каждый source входит в source frame.
- Для source есть provenance: кто, когда, как создал/собрал/изменял.
- Sensitive sources имеют ограничения использования.
- Excluded sources не поддерживают claim.

Blocking defects:
- claim опирается на source вне source frame;
- provenance отсутствует там, где без него нельзя оценить evidence.

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

Required evidence for status `complete`:
- каждая evidence unit атомарна и имеет locator;
- observation отделено от interpretation;
- missingness/ambiguity классифицированы.

Atomic checks:
- Source segment имеет locator.
- Evidence unit атомарна.
- Observation отделена от interpretation.
- Missing не выдано за absent.
- Ambiguity классифицирована.

Blocking defects:
- evidence unit неатомарна и поддерживает сильный claim;
- claim построен на interpretation, представленной как observation.

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

Required evidence for status `complete`:
- code passport включает definition/inclusion/exclusion/examples/prohibited uses;
- assignments связаны с target unit;
- adjudication зафиксирован для расхождений.

Atomic checks:
- Каждый code имеет definition, inclusion, exclusion, examples, counterexamples, prohibited uses.
- Label не используется вместо полного code object.
- Assignment связан с target unit.
- Есть adjudication для расхождений.

Blocking defects:
- code превращен в diagnosis/claim/decision без rules;
- нет anchors для scale, используемой в claim/action.

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

Required evidence for status `complete`:
- aggregation contract и denominator явно определены;
- thresholds управляемы и не скрыты;
- output limits и uncertainty propagation задокументированы.

Atomic checks:
- Aggregation contract описан.
- Denominator определен.
- Threshold не скрыт в коде/обычае/устной договоренности.
- Output interpretation limits названы.
- Uncertainty propagation учитывает слабые inputs.

Blocking defects:
- output принят за claim;
- score/probability/confidence принят за permission.

## 8. Доказательный вывод

Главный вопрос: что можно утверждать на основании evidence и output?

Artifacts:
- hypothesis register;
- alternative hypothesis register;
- evidence relation register;
- counterevidence register;
- falsification matrix;
- evidence class matrix;
- epistemic stage register;
- claim rule register.

Required evidence for status `complete`:
- claim имеет явный scope;
- есть связь evidence relation для главных тезисов;
- alternatives, counterevidence и falsification отражены.

Atomic checks:
- Claim имеет scope.
- Evidence relation названа.
- Alternative hypotheses рассмотрены.
- Counterevidence найдено или явно искалось.
- Falsification tests определены.
- Evidence freshness учтена.

Blocking defects:
- claim scope шире evidence scope;
- counterevidence проигнорировано;
- epistemic stage перепутана с release status.

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

Required evidence for status `complete`:
- recommendation и decision разделены;
- есть use constraints и automation boundary;
- recourse доступен affected parties.

Atomic checks:
- Recommendation отделена от decision.
- Use constraints привязаны к claim/output.
- Automation boundary описана.
- Human oversight mode выбран.
- Override и recourse доступны.

Blocking defects:
- действие выполняется без authority;
- consequential decision не имеет recourse;
- confidence используется как permission.

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

Required evidence for status `complete`:
- качество раскрыто по нескольким измерениям, не одним score;
- validation scope сопоставлен с intended use/deployment;
- defects имеют operational effect и обработку.

Atomic checks:
- Validity, reliability, accuracy, calibration и uncertainty не смешаны.
- Validation scope сравнен с deployment/use scope.
- Transportability проверена для данного context.
- Quality defects имеют operational effect.

Blocking defects:
- validation scope уже intended use scope;
- качество сведено к одному confidence/accuracy score.

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

Required evidence for status `complete`:
- risk scenarios учитывают false positive и false negative harm;
- residual risk отделен от accepted risk;
- risk acceptance подписан authority role.

Atomic checks:
- False positive и false negative harms рассмотрены отдельно.
- Residual risk не выдан за accepted risk.
- Risk acceptance имеет authority.
- Reversibility оценена.
- Safety case не сведен к mitigation list.

Blocking defects:
- consequential decision без risk acceptance;
- невозможность исправить ошибку не отражена в risk profile.

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

Required evidence for status `complete`:
- review, approval и authority зафиксированы раздельно;
- есть контроль conflict of interest;
- известен veto-holder.

Atomic checks:
- Responsible, accountable, reviewer, approver и authority различены.
- Reviewer independence проверена.
- Conflict of interest описан.
- Veto-holder известен.

Blocking defects:
- review принят за approval;
- responsibility принята за authority;
- expertise принята за independence.

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

Required evidence for status `complete`:
- gate sheet содержит inputs, pass/fail criteria, roles, release effect;
- stop-rules исполнимы;
- waiver/exception ограничены scope/time/authority.

Atomic checks:
- Gate имеет inputs, pass/fail criteria, reviewer, approver, release effect, audit record.
- Stop-rules исполнимы.
- Waiver ограничен scope/time/authority.
- Exception не является молчаливым обходом.
- CAPA имеет owner и acceptance criteria.

Blocking defects:
- нет gate перед release/use;
- waiver используется как постоянное разрешение;
- checklist выдан за gate.

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

Required evidence for status `complete`:
- change classes выделены и связаны с impact;
- drift/incident triggers ведут к revalidation;
- rollback имеет owner и criteria.

Atomic checks:
- Lifecycle phase известна.
- Change classes различены: semantic, evidence, threshold, model, policy, release-critical.
- Drift and incident triggers ведут к revalidation.
- Rollback возможен и ответственен.

Blocking defects:
- release допускается без monitoring/revalidation triggers;
- threshold/source/schema changes не считаются potentially release-critical.

## 15. Машинная форма

Главный вопрос: как человекочитаемый смысл сохраняется в records, schemas и validators?

Artifacts:
- schema registry;
- object schemas;
- field dictionary;
- enum registry;
- referential integrity rules;
- validation rules;
- migration rules;
- schema parity tests.

Required evidence for status `complete`:
- human-readable specification отражена в schema;
- enums и boundaries согласованы;
- migrations не меняют смысл молча.

Atomic checks:
- Human-readable specification отражена в schema.
- Enums совпадают с semantic boundaries.
- Validators проверяют не только форму, но и ключевые constraints.
- Migration не меняет смысл молча.

Blocking defects:
- schema validity принята за semantic validity;
- schema меняет meaning без human-readable supersession.

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

Required evidence for status `complete`:
- trace edges покрывают путь source -> evidence -> output -> claim -> decision -> release;
- audit record связывает decision, evidence и authority;
- reconstructability test пройден или запланирован с датой.

Atomic checks:
- Есть trace edges между source, evidence, code/output, claim, decision и release.
- Evidence ledger содержит locators и provenance.
- Step log фиксирует review/calculation/change/release.
- Audit pack позволяет независимую проверку.
- Reconstructability test пройден или запланирован.

Blocking defects:
- аудитор не может восстановить путь от source material до release/use condition;
- audit record не связывает decision с evidence и authority.
