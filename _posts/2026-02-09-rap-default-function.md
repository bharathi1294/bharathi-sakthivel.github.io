---
title: "Default Function RAP"
date: 2026-02-09 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, default-function]
description: "Learn how to use a default function in ABAP RAP to automatically pre-fill field values when a new record is created."
---

```abap
"Step 1: Define a default function in your Behavior Definition (BDEF)
define behavior for ZR_ROOT_ENTITY alias Travel
....
{
  ...
  create{ default function GetDefaultsForCreate; }
  
}

"Step 2: Implement the logic in the behavior class
METHOD GetDefaultsForCreate.
    result = VALUE #( FOR key IN keys (
          %cid = key-%cid
          %param = VALUE #( CurrencyCode = 'INR'
                            BeginDate = cl_abap_context_info=>get_system_date( )
                            OverallStatus = 'O' ) ) ).
ENDMETHOD.

"Step 3: Use the function in your Projection BDEF(If you defined)
projection;
strict(2);

define behavior for ZC_ROOT_ENTITY alias Travel
...
{
  ...
  use function GetDefaultsForCreate;
}

"Step 4: That’s it! Now when you try to create a new record, the values will be pre-filled automatically 
```
