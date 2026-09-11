---
title: "ABAP: Build Range Tables Directly in SELECT"
date: 2025-12-29 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, range-table, select, new-syntax, sql]
---

Building range tables used to require a SELECT followed by a LOOP to construct sign/option/low. Modern ABAP lets you do it in one step.

## Old Way

```abap
SELECT vbeln FROM vbak INTO TABLE @DATA(lt_vbeln).

DATA lt_range TYPE RANGE OF vbeln_d.
LOOP AT lt_vbeln INTO DATA(wa).
  APPEND VALUE #( sign = 'I' option = 'EQ' low = wa-vbeln ) TO lt_range.
ENDLOOP.
```

## New Way — Build Range Directly in SELECT

```abap
SELECT FROM vbak
  FIELDS 'I'   AS sign,
         'EQ'  AS option,
         vbeln AS low
  INTO TABLE @DATA(lt_range).
```

The SELECT populates the range structure fields directly — no intermediate table, no loop.

## Use It Immediately

```abap
SELECT * FROM vbap
  WHERE vbeln IN @lt_range
  INTO TABLE @DATA(lt_items).
```

## With a WHERE Condition

```abap
SELECT FROM vbak
  FIELDS 'I' AS sign, 'EQ' AS option, vbeln AS low
  WHERE erdat = @sy-datum
  INTO TABLE @DATA(lt_today_orders).
```

Clean, concise, and faster to read. One less loop in your codebase.
