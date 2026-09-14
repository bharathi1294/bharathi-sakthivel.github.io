---
title: "Range Tables in ABAP"
date: 2025-12-29 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, range-table]
description: "Learn how to build ABAP range tables directly from an SQL SELECT using FIELDS with CAST, replacing the traditional LOOP approach."
---

```abap
"Old way
DATA: lr_salesorder TYPE RANGE OF vbeln.
SELECT vbeln FROM vbak WHERE kunnr = '0000000272' INTO TABLE @DATA(lt_customers_orders).
"Loop
LOOP AT lt_customers_orders INTO DATA(ls_customer_order).
   "....
ENDLOOP.
"For
lr_salesorder = VALUE #( FOR ls_order IN lt_customers_orders (
                          sign = 'I'
                          option = 'EQ'
                          low = ls_order-vbeln ) ).

"New way
SELECT FROM vbak
       FIELDS  'I'   AS sign,
               'EQ'  AS option,
               vbeln AS low,
               CAST( @space AS CHAR( 10 ) ) AS high
      WHERE kunnr = '0000000272'
      INTO TABLE @DATA(lr_salesorder).

"Logic with lr_saleorder
```
