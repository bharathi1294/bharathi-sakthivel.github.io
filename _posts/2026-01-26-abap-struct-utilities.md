---
title: "ABAP: CL_ABAP_STRUCT_UTILITIES — Get Non-Initial Fields from a Structure"
date: 2026-01-26 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, cl-abap-struct-utilities, structure, reflection, new-syntax]
---

`cl_abap_struct_utilities=>filled_components()` returns only the **non-initial (filled) components** of a structure — useful when you want to process only the fields that actually have values.

## Use Case

You have a structure with 5 user ID fields, but only 4 are filled. You want to collect just those 4 into a table.

```abap
TYPES: BEGIN OF ty_user,
         user_id1 TYPE uname,
         user_id2 TYPE uname,
         user_id3 TYPE uname,
         user_id4 TYPE uname,
         user_id5 TYPE uname,
       END OF ty_user.

DATA(ls_user) = VALUE ty_user(
    user_id1 = 'BHARAT'
    user_id2 = 'ADMIN'
    user_id4 = 'USER1'
    user_id5 = 'USER2'
    " user_id3 intentionally left empty
).
```

## Get Only Filled Components

```abap
DATA(lt_users) = REDUCE string_table(
    INIT result = VALUE string_table( )
    FOR comp IN cl_abap_struct_utilities=>filled_components( ls_user )
    NEXT result = VALUE #( BASE result ( CONV string( comp-value->* ) ) )
).

" lt_users: [ BHARAT, ADMIN, USER1, USER2 ]
" user_id3 is skipped — it was initial
```

## Related Methods

| Method | Description |
|---|---|
| `filled_components()` | Returns non-initial components |
| `filled_components_c()` | Case-sensitive variant |
| `filled_components_x()` | Alternative processing option |

## Practical Use

Handy when building dynamic WHERE conditions, collecting agent lists, or processing any structure where only some fields will be populated at runtime.
