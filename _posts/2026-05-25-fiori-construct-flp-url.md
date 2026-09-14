---
title: "Fiori: Construct FLP URLs Programmatically with CL_LSAPI_MANAGER"
date: 2026-05-25 08:00:00 +0530
categories: [Fiori]
tags: [fiori, abap, flp, launchpad, url, navigation, cl-lsapi-manager]
description: "Learn how to programmatically construct Fiori Launchpad URLs with parameters from ABAP using CL_LSAPI_MANAGER."
---

Need to launch a Fiori app from ABAP — or generate a dynamic FLP URL with parameters? Use `CL_LSAPI_MANAGER`.

## Get an Instance

```abap
DATA(lo_lsapi) = cl_lsapi_manager=>get_instance( ).
```

## Create an FLP App URL

```abap
DATA(lv_url) = cl_lsapi_manager=>create_flp_url(
    object       = 'MaintenanceObject'
    action       = 'listFunctionalLocStructure'
    system_alias = 'LOCAL'
    parameters   = VALUE #(
        ( name = 'DY_TPLNR' value = '1000-001' )
    )
).
```

## Create URL with Multiple Parameters

```abap
DATA(lv_url) = cl_lsapi_manager=>create_flp_url(
    object     = 'SalesOrder'
    action     = 'display'
    parameters = VALUE #(
        ( name = 'VBELN'    value = '0000000042' )
        ( name = 'BUKRS'    value = '1000' )
    )
).
```

## Navigate to the URL

```abap
" In-place navigation
lo_lsapi->navigate( iv_url = lv_url ).

" Open in new window/tab
lo_lsapi->open_url( iv_url = lv_url ).
```

## Common Use Cases

| Use Case | Approach |
|---|---|
| Email with deep link to a Fiori app | `create_flp_url` + append to email body |
| Launch Fiori from SAPGUI transaction | `create_flp_url` + `open_url` |
| Workflow task links | `create_flp_url` with entity key parameters |
| QR code with Fiori link | `create_flp_url` → encode as QR |

## Finding Semantic Object and Action

In SAP Fiori Launchpad Designer → open any tile → the **Semantic Object** and **Action** are shown in the tile configuration.
