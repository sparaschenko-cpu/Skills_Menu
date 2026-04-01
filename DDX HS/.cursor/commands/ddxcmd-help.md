# ddxcmd-help — карта команд DDX

## Назначение

Используй, чтобы быстро выбрать правильную `/ddxcmd-*` команду и увидеть обязательные параметры.

## Параметры

- (опционально) `scope=analysis|safety|maintenance|all` (по умолчанию `all`)

## Входы

- не требуется

## Шаги

1. Выведи список команд, сгруппированный по категориям:
   - **Анализ кейса**: `/ddxcmd-new-case`, `/ddxcmd-case-status`, `/ddxcmd-run`, `/ddxcmd-phase0-scope`, `/ddxcmd-phase1-annotate`, `/ddxcmd-phase2-hypotheses`, `/ddxcmd-phase2-validation-funnel`, `/ddxcmd-phase3-synthesize`, `/ddxcmd-coverage-check`
   - **Режимы/контекст**: `/ddxcmd-8d`, `/ddxcmd-mode1`, `/ddxcmd-mode2`, `/ddxcmd-mode3`
   - **Safety checkpoints**: `/ddxcmd-gp0`…`/ddxcmd-gp6`
   - **Обслуживание системы**: `/ddxcmd-audit-rules-coverage`, `/ddxcmd-audit-skills-coverage`, `/ddxcmd-audit-subagents-coverage`, `/ddxcmd-regression-check`, `/ddxcmd-artifacts-lint`
2. Для каждой команды укажи:
   - требуется ли `case_id=...`
   - какие ключевые артефакты (файлы) она создаёт/обновляет
   - какие Skills/Subagents обычно вызывает (коротко)

## Выходы

- Сообщение в чате: “карта команд” (без создания файлов).

## Quality gates

- Команды перечислены без коллизий имён (строго `/ddxcmd-*`).
