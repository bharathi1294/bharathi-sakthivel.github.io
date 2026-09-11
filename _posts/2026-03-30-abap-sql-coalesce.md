---
title: "ABAP SQL: COALESCE — Return the First Non-Null Value from Multiple Joins"
date: 2026-03-30 08:00:00 +0530
categories: [ABAP SQL]
tags: [abap, sql, coalesce, left-outer-join, null-handling]
---

`COALESCE` returns the **first non-null value** from a list of up to 255 arguments. It's essential when you have multiple LEFT OUTER JOINs and want to merge their results into a single field.

## Syntax

```abap
COALESCE( expr1, expr2, ..., exprN )
```

Returns `expr1` if it's not null, otherwise `expr2`, and so on. Returns the last argument if all are null.

## Example: Merge Two Customer Sources

```abap
SELECT
    so~vbeln,
    COALESCE( c1~kunnr, c2~kunnr )       AS customer_id,
    COALESCE( c1~name1, c2~name1, '' )   AS customer_name
FROM vbak AS so
LEFT OUTER JOIN kna1 AS c1 ON so~kunnr = c1~kunnr AND c1~ktokd = 'Z001'
LEFT OUTER JOIN kna1 AS c2 ON so~kunnr = c2~kunnr AND c2~ktokd = 'Z002'
INTO TABLE @DATA(lt_result).
```

## Result

| Sales Order | Customer ID | Source |
|---|---|---|
| 0000000001 | CUST_1 | c1 match |
| 0000000002 | CUST_2 | c2 match (c1 was null) |
| 0000000003 | (null) | No match in either join |

## Use Cases

| Scenario | COALESCE Usage |
|---|---|
| Merge data from two source tables | `COALESCE( source1~field, source2~field )` |
| Default value when join finds nothing | `COALESCE( joined~field, '' )` |
| Priority lookup (try primary, then fallback) | `COALESCE( primary~val, secondary~val, 'default' )` |

## Difference from CASE

```abap
" COALESCE equivalent in CASE:
CASE WHEN c1~kunnr IS NOT NULL THEN c1~kunnr
     WHEN c2~kunnr IS NOT NULL THEN c2~kunnr
     ELSE NULL
END

" COALESCE is more concise:
COALESCE( c1~kunnr, c2~kunnr )
```

Use `COALESCE` when the condition is simply "use the first non-null value".
