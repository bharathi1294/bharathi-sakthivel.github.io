---
title: "ABAP 7.58: RETURN a Value Directly from a Method"
date: 2026-01-05 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, return, methods, new-syntax, abap-758]
---

Before ABAP 7.58, `RETURN` only exited a method — you had to assign to the returning parameter separately. From 7.58 onwards, you can return a value directly.

## Old Way

```abap
METHOD get_greeting.
  rv_text = 'Hello World'.
  RETURN.
ENDMETHOD.
```

## New Way — Return a Simple Value

```abap
METHOD get_greeting.
  RETURN 'Hello World'.
ENDMETHOD.
```

## Return a Structure

```abap
METHOD get_order.
  RETURN VALUE #(
    vbeln = '0000000001'
    erdat = '20240101'
  ).
ENDMETHOD.
```

## Return a Table

```abap
METHOD get_orders.
  RETURN VALUE #(
    ( vbeln = '0000000001' erdat = '20240101' )
    ( vbeln = '0000000002' erdat = '20240102' )
  ).
ENDMETHOD.
```

## Early Return (Guard Clause Pattern)

```abap
METHOD get_discount.
  IF iv_amount <= 0.
    RETURN 0.
  ENDIF.

  IF iv_customer_type = 'VIP'.
    RETURN 20.
  ENDIF.

  RETURN 10.
ENDMETHOD.
```

This brings ABAP in line with Java, C#, and JavaScript — cleaner guard clauses, no intermediate variable needed.

> Requires **ABAP 7.58** or later (BTP ABAP Environment / S/4HANA 2023+).
