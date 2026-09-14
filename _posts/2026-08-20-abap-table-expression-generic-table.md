---
title: "Table Expressions with Fully Generic Typed Tables in ABAP"
date: 2026-08-20 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, table-expressions, field-symbols, data-references, generic-table]
description: "Learn how to read and update rows in fully generic typed ABAP tables at runtime using dynamic table expressions with FIELD-SYMBOLS."
---

When working with fully generic tables in ABAP (e.g., `FIELD-SYMBOLS <f_table> TYPE ANY TABLE`), the actual structure of the table is determined only at runtime.

For example, assume `<f_table>` is dynamically assigned to an internal table of type `MARA` and contains some data. If you want to read or update the Material Group by providing a specific Material Number, you cannot directly use table expressions like `<f_table>[matnr = '...']` because the structure is not statically known at compile time.

Instead, here's how you can handle such cases dynamically while still leveraging the power of table expressions:

```abap
" Assign a field symbol to a row by dynamic key
ASSIGN <f_table>[ ('MATNR') = lv_matnr ] TO FIELD-SYMBOL(<f_row>).

IF sy-subrc = 0.
  " Read a field from the row dynamically
  ASSIGN COMPONENT 'MATKL' OF STRUCTURE <f_row> TO FIELD-SYMBOL(<f_matkl>).
  IF sy-subrc = 0.
    DATA(lv_matkl) = <f_matkl>.
  ENDIF.
ENDIF.
```

> **Note:** The single quotes around `'MATNR'` in `('MATNR')` are required because we're using a dynamic component name (a string). For statically known structures, you wouldn't need the quotes.

![Table Expressions with Generic Typed Tables](/assets/images/abap_table_expression_generic_table.jpg)
