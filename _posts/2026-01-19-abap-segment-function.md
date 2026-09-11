---
title: "ABAP: SEGMENT Function — Split Strings by Delimiter"
date: 2026-01-19 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, segment, string, split, new-syntax]
---

The `segment()` function extracts a specific part of a string based on a delimiter — without needing `SPLIT INTO TABLE` followed by a READ.

## Parameters

| Parameter | Description |
|---|---|
| `val` | Input string |
| `index` | Position (1 = first, -1 = last) |
| `sep` | Substring delimiter |
| `space` | Individual characters each treated as a delimiter |

## Examples with `sep`

```abap
" Get first segment
DATA(lv_first) = segment( val = 'Hallo,world,123' index = 1 sep = ',' ).
" Result: Hallo

" Get last segment (negative index)
DATA(lv_last) = segment( val = 'Hallo,world,123' index = -1 sep = ',' ).
" Result: 123

" Get second segment
DATA(lv_mid) = segment( val = 'Hallo,world,123' index = 2 sep = ',' ).
" Result: world
```

## Multi-character Delimiter

```abap
DATA(lv_part) = segment( val = 'Hallo<br>world<br>123' index = 2 sep = '<br>' ).
" Result: world
```

## Multiple Single-char Delimiters with `space`

```abap
" Any of: . # / or space acts as delimiter
DATA(lv_b) = segment( val = 'a/b#c d.e' index = 2 space = '. #/' ).
" Result: b
```

## Loop Through All Segments

```abap
DATA lt_parts TYPE string_table.
DATA(lv_idx) = 1.

DO.
  TRY.
    APPEND segment( val = 'a,b,c,d,e' index = lv_idx sep = ',' ) TO lt_parts.
    lv_idx += 1.
  CATCH cx_sy_strg_par_val.
    EXIT.
  ENDTRY.
ENDDO.
" lt_parts: [ a, b, c, d, e ]
```

Cleaner than `SPLIT` when you need just one specific segment.
