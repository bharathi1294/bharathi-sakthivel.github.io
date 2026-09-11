---
title: "ABAP: STEP Keyword — Loop Forward, Backward, and Skip Rows"
date: 2026-01-12 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, step, loop, internal-table, new-syntax]
---

The `STEP` keyword gives you direct control over loop step size and direction — no more manual `sy-tabix` arithmetic.

## Syntax

```abap
LOOP AT lt_table ... STEP <n> [FROM <start>] [TO <end>].
```

- Positive `n` → forward
- Negative `n` → backward

## Skip Every Other Row (Step 2)

```abap
LOOP AT lt_numbers ASSIGNING FIELD-SYMBOL(<n>) STEP 2.
  " Processes rows: 1, 3, 5, 7, 9
ENDLOOP.
```

## Reverse Loop

```abap
LOOP AT lt_numbers ASSIGNING FIELD-SYMBOL(<n>) STEP -1.
  " Processes rows: 10, 9, 8, 7 ... 1
ENDLOOP.
```

## Reverse from a Specific Position

```abap
LOOP AT lt_numbers ASSIGNING FIELD-SYMBOL(<n>) FROM 6 STEP -1.
  " Processes rows: 6, 5, 4, 3, 2, 1
ENDLOOP.
```

## Works in VALUE / FOR Expressions Too

```abap
DATA(lt_odds) = VALUE int4_table(
    FOR <n> IN lt_numbers STEP 2
    ( <n> )
).
```

## LINES OF with STEP

```abap
LOOP AT lt_table USING KEY primary_key
    FROM 1 TO lines( lt_table ) STEP 2
    INTO DATA(wa).
ENDLOOP.
```

Before `STEP`, reversing a table required sorting a copy or tracking `sy-tabix`. Now it's one keyword.
