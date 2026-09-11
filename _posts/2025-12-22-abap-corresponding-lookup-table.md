---
title: "ABAP: CORRESPONDING Operator with Lookup Table"
date: 2025-12-22 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, corresponding, lookup, internal-table, new-syntax]
---

The `CORRESPONDING` operator can do more than just copy matching field names — it can enrich records by performing a **lookup on another internal table**.

## Use Case

You have an employee table without manager names. A separate manager table has that data. Merge them without a LOOP.

## Old Way (with LOOP)

```abap
LOOP AT lt_employees ASSIGNING FIELD-SYMBOL(<emp>).
  READ TABLE lt_managers INTO DATA(ls_mgr)
    WITH KEY manager_id = <emp>-manager_id.
  IF sy-subrc = 0.
    <emp>-manager_name = ls_mgr-name.
  ENDIF.
ENDLOOP.
```

## New Way (CORRESPONDING with lookup)

```abap
lt_employees = CORRESPONDING #(
    lt_employees
    FROM lt_managers
    USING manager_id = manager_id
    MAPPING manager_name = name
).
```

- `FROM` — the lookup source table
- `USING` — the join key (employee's `manager_id` = manager's `manager_id`)
- `MAPPING` — how to map the looked-up field into the target structure

## Result

Each employee record gets its `manager_name` populated from the manager table — no loop, no READ TABLE, one expression.

## Notes

- Non-matching entries remain as-is (no error, field stays initial)
- Works with `BASE` to preserve existing values: `CORRESPONDING #( BASE ( ls_emp ) ... )`
