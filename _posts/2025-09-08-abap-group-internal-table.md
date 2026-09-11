---
title: "ABAP New Syntax: Group Internal Table Data with LOOP AT GROUP"
date: 2025-09-08 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, internal-table, loop, group-by, new-syntax]
---

The old `AT NEW <field>` and `AT END OF <field>` statements inside loops are now **obsolete** in modern ABAP. The replacement is `LOOP AT GROUP` and `FOR GROUPS OF`.

## Old Way (Obsolete)

```abap
LOOP AT lt_courses INTO wa.
  AT NEW employee_id.
    " start of group logic
  ENDAT.
  AT END OF employee_id.
    " end of group logic
  ENDAT.
ENDLOOP.
```

## New Way: LOOP AT GROUP

```abap
LOOP AT lt_courses INTO DATA(wa)
    GROUP BY ( employee_id = wa-employee_id
               size  = GROUP SIZE
               index = GROUP INDEX )
    ASSIGNING FIELD-SYMBOL(<course>).

    DATA(ls_employee) = VALUE ty_employee(
        employee_id   = <course>-employee_id
        no_of_courses = <course>-size
        total_amount  = 0 ).

    " Iterate group members
    LOOP AT GROUP <course> ASSIGNING FIELD-SYMBOL(<member>).
        ls_employee-total_amount += <member>-amount.
    ENDLOOP.

    APPEND ls_employee TO lt_result.
ENDLOOP.
```

## Even Cleaner: REDUCE inside GROUP

```abap
LOOP AT lt_courses INTO DATA(wa)
    GROUP BY ( employee_id = wa-employee_id size = GROUP SIZE )
    ASSIGNING FIELD-SYMBOL(<course>).

    ls_employee-total_amount = REDUCE #(
        INIT amount = 0
        FOR <member> IN GROUP <course>
        NEXT amount = amount + <member>-amount
    ).
ENDLOOP.
```

## Most Concise: FOR GROUPS OF

```abap
DATA(lt_result) = VALUE tt_employee(
    FOR GROUPS course OF wa IN lt_courses
    GROUP BY ( employee_id = wa-employee_id
               size  = GROUP SIZE
               index = GROUP INDEX )
    ( employee_id   = course-employee_id
      no_of_courses = course-size
      total_amount  = REDUCE #(
          INIT amount = 0
          FOR member IN GROUP course
          NEXT amount = amount + member-amount
      )
    )
).
```

## Output

| EMPLOYEE_ID | NO_OF_COURSES | TOTAL_AMOUNT |
|---|---|---|
| 00000001 | 3 | 6000 |
| 00000002 | 3 | 6000 |
| 00000003 | 1 | 1000 |

`GROUP SIZE` and `GROUP INDEX` are special group attributes — no extra SELECT needed.
