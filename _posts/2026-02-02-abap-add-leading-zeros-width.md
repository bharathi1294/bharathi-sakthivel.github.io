---
title: "ABAP: Add Dynamic Leading Zeros with String Templates and WIDTH"
date: 2026-02-02 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, string-templates, leading-zeros, alpha, width, new-syntax]
---

When adding leading zeros with `ALPHA = IN`, the total length is fixed. But what if you need different lengths for different fields (sales order = 10, contract = 13)?

Use `WIDTH` to make the length dynamic.

## Static Length (Fixed)

```abap
" Always pads to 10 characters
DATA(lv_so) = |{ lv_input ALPHA = IN WIDTH = 10 }|.
" '123' → '0000000123'
```

## Dynamic Length (Variable)

```abap
CLASS lcl_add_leading_zeros DEFINITION CREATE PUBLIC.
  PUBLIC SECTION.
    CLASS-METHODS add_leading_zeros
      IMPORTING iv_input  TYPE string
                iv_length TYPE int4
      RETURNING VALUE(rv_output) TYPE string.
ENDCLASS.

CLASS lcl_add_leading_zeros IMPLEMENTATION.
  METHOD add_leading_zeros.
    rv_output = |{ iv_input ALPHA = IN WIDTH = iv_length }|.
  ENDMETHOD.
ENDCLASS.

" Sales Order (VBELN) — 10 chars
DATA(lv_so) = lcl_add_leading_zeros=>add_leading_zeros(
    iv_input  = '123'
    iv_length = 10
).
" → '0000000123'

" Contract Number (RANL) — 13 chars
DATA(lv_cn) = lcl_add_leading_zeros=>add_leading_zeros(
    iv_input  = '123'
    iv_length = 13
).
" → '0000000000123'
```

## Why WIDTH Matters Here

`ALPHA = IN` alone always uses the field's **defined length** (type length). `WIDTH` overrides that, letting you pad to any length at runtime — perfect when your method serves multiple field types.
