---
title: "RAP: Distinguish UI vs API Calls with cl_abap_behv_aux"
date: 2025-10-13 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, cl-abap-behv-aux, projection, api, ui]
---

When your RAP Business Object has both a **UI projection** and an **API projection**, you often need validations or determinations to run **only for UI calls** — not for API calls.

## The Problem

```
R_SomeName  (Root BO)
  ├── C_SomeName  (UI Projection)
  └── A_SomeName  (API Projection)
```

Validations in `R_SomeName` fire for both projections. But some checks (e.g., mandatory field warnings) only make sense for the UI.

## The Solution: `cl_abap_behv_aux=>get_current_context()`

```abap
METHOD validate_for_ui_only.
  cl_abap_behv_aux=>get_current_context(
    IMPORTING from_projection = DATA(lv_projection)
  ).

  " Only run for UI projection calls
  CHECK lv_projection = 'C_SOMENAME'.

  " Your validation logic here
  READ ENTITIES OF R_SomeName ...
ENDMETHOD.
```

`lv_projection` is populated with the **name of the calling projection** — so you can branch based on which projection triggered the call.

## Practical Use Cases

| Scenario | Use |
|---|---|
| Mandatory field check only for UI | `CHECK lv_projection = 'C_...'` |
| Skip heavy validation for API batch loads | `CHECK lv_projection <> 'A_...'` |
| Different determination logic per channel | `IF lv_projection = 'C_...'` |

## Note

The projection name is returned in **uppercase**. Make sure your comparison matches the actual projection view name in your system.
