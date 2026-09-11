---
title: "ABAP SQL: STRING_AGG — Concatenate Rows into a Single String"
date: 2026-04-06 08:00:00 +0530
categories: [ABAP SQL]
tags: [abap, sql, string-agg, aggregation, group-by]
---

`STRING_AGG` concatenates values from multiple rows into a single delimited string — perfect for generating readable summaries from normalized tables.

## Syntax

```abap
STRING_AGG( column_name, 'delimiter' )
```

## Example: Employee Course List

```abap
SELECT
    employee_id,
    STRING_AGG( course_name, ', ' ) AS courses
FROM zemp_courses
GROUP BY employee_id
INTO TABLE @DATA(lt_result).
```

**Result:**

| EMPLOYEE_ID | COURSES |
|---|---|
| 00000001 | ABAP, UI5, Fiori |
| 00000002 | RAP, CDS |

## Important: 1333 Character Limit

`STRING_AGG` returns an `SSTRING` — limited to **1333 characters**. Exceeding this throws `CX_SY_OPEN_SQL_DB`.

## Solution: TO_CLOB for Long Strings

```abap
" Requires ABAP 7.54+
SELECT
    employee_id,
    TO_CLOB( STRING_AGG( course_name, ', ' ) ) AS courses
FROM zemp_courses
GROUP BY employee_id
INTO TABLE @DATA(lt_result).
```

`TO_CLOB` wraps the result as a CLOB — no length restriction.

## With ORDER BY Inside Aggregation

```abap
SELECT
    employee_id,
    STRING_AGG( course_name, ', ' ORDER BY course_name ASCENDING ) AS courses
FROM zemp_courses
GROUP BY employee_id
INTO TABLE @DATA(lt_result).
```

## Use Cases

| Use Case | Example |
|---|---|
| List tags/labels per record | `STRING_AGG( tag, ', ' )` |
| Comma-separated IDs for display | `STRING_AGG( doc_id, '; ' )` |
| Audit trail summary | `STRING_AGG( action, ' → ' ORDER BY ts )` |
