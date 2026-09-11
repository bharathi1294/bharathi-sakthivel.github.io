---
title: "ABAP SQL: Structure Indicators — Partial Updates Without Overwriting Fields"
date: 2026-04-13 08:00:00 +0530
categories: [ABAP SQL]
tags: [abap, sql, update, structure-indicators, partial-update]
---

SQL Indicators let you update **only specific fields** in a database table — similar to RAP's `%control` component group, but for direct ABAP SQL updates.

## The Problem Without Indicators

A regular `UPDATE FROM TABLE` overwrites **all fields**, even ones you didn't change:

```abap
" This updates ALL fields — overwrites unchanged ones too
UPDATE sflight FROM TABLE @lt_sflight.
```

## Solution: Structure Indicators

```abap
" Define a type with indicator structure
TYPES ty_sflight TYPE sflight WITH INDICATORS set_ind.

DATA lt_sflight TYPE TABLE OF ty_sflight WITH DEFAULT KEY.

" Read data
SELECT FROM sflight
  FIELDS *
  INTO CORRESPONDING FIELDS OF TABLE @lt_sflight.

" Mark only the fields you want to update
LOOP AT lt_sflight ASSIGNING FIELD-SYMBOL(<row>).
  IF <row>-price > 1000.
    <row>-price    = <row>-price * '0.9'.  " 10% discount
    <row>-set_ind-price = '01'.            " mark for update
  ENDIF.
ENDLOOP.

" Only flagged fields are updated
UPDATE sflight FROM TABLE @lt_sflight INDICATORS SET STRUCTURE set_ind.
```

## How Flags Work

| Flag value | Effect |
|---|---|
| `'01'` | Field IS updated |
| `' '` (initial) | Field is NOT updated |

## Use Cases

| Scenario | Benefit |
|---|---|
| Mass update of one field | Only that field changes — no risk of clearing others |
| Conditional field update | Set indicator only when condition met |
| Performance-sensitive updates | Smaller UPDATE payload |

## Relationship to RAP %control

This is the direct SQL equivalent of RAP's `%control` group — same concept, used outside the RAP framework when you're doing traditional ABAP SQL updates.
