## 08 — Hypothesis Map (App.5 / §1.2)

**case_id**: `<case_id>`

### Нотация (текстовая спецификация графа)

- Узел: `H-#### {status=..., axis5=..., axis4=..., mode=...}`
- Ребро: `H-#### --(supports/refutes/modifies/competes)--> H-####`
- Граница узла (режим генерации):
  - `solid` = стандартный
  - `double` = ресурсный (Mode 2)
  - `dashed` = состязательный (Mode 1)
  - `dotted` = мета-критический (Mode 3)

### Узлы

- `H-0001 {status=working(H1), axis5=3, axis4=3, mode=standard, border=solid}`

### Рёбра

- `H-0001 --(supports)--> H-0003`

### Кластеры/контекст (если 8D)

- Контекстные области: `Axis7:<...>`, `Axis8:<...>` (см. `08d_context.md`)
