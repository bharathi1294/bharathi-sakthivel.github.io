---
title: "RETURN Statement in ABAP Methods"
date: 2026-01-05 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, return, new-syntax]
description: "Learn how to use the RETURN statement in ABAP 7.58+ to return values directly from methods, including structures and internal tables."
---

In ABAP, the `RETURN` statement has been used to exit a procedure such as a method or function module. Unlike many other programming languages where `return` is used to pass a value back from a function, earlier versions of ABAP did not support this functionality.

However, starting with **ABAP 7.58**, the `RETURN` statement can now be used to return a value directly from a method.

> **Note:** This applies only to methods that have a returning parameter.

For more details, refer to the [ABAP RETURN Statement Documentation](https://help.sap.com/doc/abapdocu_latest_index_htm/latest/en-US/index.htm?file=abapreturn.htm).

```abap
CLASS lcl_demo DEFINITION.
  PUBLIC SECTION.
    CLASS-METHODS:
      return            RETURNING VALUE(rv_value) TYPE text100,
      return_with_value RETURNING VALUE(rv_value) TYPE text100,
      return_structure  RETURNING VALUE(rs_vbak)  TYPE vbak,
      return_table      RETURNING VALUE(rt_table) TYPE tt_vbak.
ENDCLASS.

CLASS lcl_demo IMPLEMENTATION.
  METHOD return.
    rv_value = 'Something'.
    RETURN.                        " classic exit — no value passed
  ENDMETHOD.

  METHOD return_with_value.
    RETURN 'something'.            " return value directly
  ENDMETHOD.

  METHOD return_structure.
    RETURN VALUE #( vbeln = '1' erdat = '20231212' ).
  ENDMETHOD.

  METHOD return_table.
    RETURN VALUE #( ( vbeln = '1' erdat = '20231212' )
                    ( vbeln = '2' erdat = '20231212' ) ).
  ENDMETHOD.
ENDCLASS.
```
