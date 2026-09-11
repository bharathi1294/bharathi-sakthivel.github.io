---
title: "ABAP String Templates — All Formatting Options"
date: 2025-09-22 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, string-templates, formatting, new-syntax]
---

String templates in ABAP (`|...|`) are much more powerful than simple concatenation. They support rich formatting options directly inline.

## Syntax

```abap
lv_result = |{ variable OPTION }|.
```

## ALPHA — Leading Zeros

```abap
lv_out = |{ '42' ALPHA = IN  WIDTH = 10 }|.  " 0000000042
lv_out = |{ '0000000042' ALPHA = OUT }|.       " 42
```

## WIDTH — Padding

```abap
lv_out = |{ 'ABC' WIDTH = 10 ALIGN = RIGHT PAD = '0' }|.  " 0000000ABC
lv_out = |{ 'ABC' WIDTH = 10 ALIGN = LEFT  PAD = '-' }|.  " ABC-------
```

## NUMBER — Thousand Separators

```abap
lv_out = |{ 1234567 NUMBER = USER }|.        " 1,234,567 (locale-based)
lv_out = |{ 1234567 NUMBER = ENVIRONMENT }|. " system locale
```

## CURRENCY — Currency Formatting

```abap
lv_out = |{ '1234.5' CURRENCY = 'INR' }|.  " 1,234.50  (2 decimals)
lv_out = |{ '1234.5' CURRENCY = 'OMR' }|.  " 1,234.500 (3 decimals)
```

## DATE / TIME / TIMESTAMP

```abap
lv_out = |{ sy-datum DATE = USER }|.         " locale date format
lv_out = |{ sy-datum DATE = ISO  }|.         " YYYY-MM-DD
lv_out = |{ sy-uzeit TIME = USER }|.         " locale time format
lv_out = |{ lv_ts    TIMESTAMP = ISO }|.     " ISO 8601
```

## DECIMALS — Round to N Places

```abap
lv_out = |{ '3.14159' DECIMALS = 2 }|.  " 3.14
```

## CASE

```abap
lv_out = |{ 'hello world' CASE = UPPER }|.  " HELLO WORLD
lv_out = |{ 'HELLO WORLD' CASE = LOWER }|.  " hello world
```

## SIGN — +/- Positioning

```abap
lv_out = |{ 42  SIGN = LEFTPLUS  }|.  " +42
lv_out = |{ -42 SIGN = RIGHTPLUS }|.  " 42-
```

## Combining Options

```abap
lv_out = |{ lv_amount CURRENCY = 'EUR' NUMBER = USER WIDTH = 15 ALIGN = RIGHT }|.
```

String templates keep your code clean — no more `CONCATENATE`, no more `WRITE TO`.
