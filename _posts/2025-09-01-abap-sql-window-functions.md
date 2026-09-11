---
title: "ABAP SQL Window Functions — Complete Guide with Examples"
date: 2025-09-01 08:00:00 +0530
categories: [ABAP SQL]
tags: [abap, sql, window-functions, hana, abap-sql]
---

Window functions in ABAP SQL let you perform powerful analytical calculations — rankings, running totals, lead/lag comparisons — directly in your SELECT query without loops.

## Available Window Functions

| Function | Description |
|---|---|
| `SUM / MIN / MAX / AVG` | Aggregations over a window |
| `ROW_NUMBER()` | Unique sequential number |
| `RANK()` | Rank with gaps |
| `DENSE_RANK()` | Rank without gaps |
| `LAG() / LEAD()` | Access previous/next row value |
| `FIRST_VALUE() / LAST_VALUE()` | Top/bottom value in partition |
| `NTILE(n)` | Divide rows into n buckets |
| `SUM ... ROWS BETWEEN` | Cumulative/running sum |

## Full Example

```abap
SELECT FROM @lt_students AS lt_students
FIELDS student_id,
       student_name,
       dep_name,
       score,

       " Global aggregations — no PARTITION
       SUM( score ) OVER( ) AS total_score,
       MAX( score ) OVER( ) AS maximum_score,
       MIN( score ) OVER( ) AS minimum_score,
       CAST( AVG( score ) OVER( ) AS INT4 ) AS average_score,

       " Department-level aggregations
       SUM( score ) OVER( PARTITION BY dep_name ORDER BY dep_name ) AS dep_total_score,
       MIN( score ) OVER( PARTITION BY dep_name ORDER BY dep_name ) AS dep_min_score,
       MAX( score ) OVER( PARTITION BY dep_name ORDER BY dep_name ) AS dep_max_score,
       CAST( AVG( score ) OVER( PARTITION BY dep_name ORDER BY dep_name ) AS INT4 ) AS dep_avg_score,

       " Ranking
       ROW_NUMBER( ) OVER( ORDER BY student_name ) AS serial_number,
       RANK( )       OVER( PARTITION BY dep_name ORDER BY score DESCENDING ) AS rank_with_gaps,
       DENSE_RANK( ) OVER( PARTITION BY dep_name ORDER BY score DESCENDING ) AS rank_no_gaps,

       " Lead / Lag
       LAG( score )  OVER( PARTITION BY dep_name ORDER BY score ) AS prev_score,
       LEAD( score ) OVER( PARTITION BY dep_name ORDER BY score ) AS next_score,

       " Cumulative / Running sum
       SUM( score ) OVER( ORDER BY student_id ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW ) AS cumulative_sum,
       SUM( score ) OVER( ORDER BY student_id ) AS running_sum,

       " Top / Bottom performers per department
       FIRST_VALUE( student_id ) OVER( PARTITION BY dep_name ORDER BY score DESCENDING ) AS top_performer,
       LAST_VALUE( student_id )  OVER( PARTITION BY dep_name ORDER BY score DESCENDING ) AS low_performer,

       " Divide into 3 performance tiers
       NTILE( 3 ) OVER( ORDER BY score ) AS performance_tier

ORDER BY dep_name, score DESCENDING
INTO TABLE @DATA(lt_result).
```

## Key Points

- `OVER()` without any clause → entire result set is the window
- `PARTITION BY` → resets the calculation per group (like a group-level loop)
- `ORDER BY` inside `OVER()` → defines the sequence within the partition
- `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` → classic cumulative sum frame
- `RANK()` leaves gaps after ties; `DENSE_RANK()` does not
