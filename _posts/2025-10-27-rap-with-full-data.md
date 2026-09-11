---
title: "RAP: with full data in save_modified — Get All Fields Without an Extra READ"
date: 2025-10-27 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, save-modified, with-full-data, unmanaged-save]
---

By default, when `save_modified` is called, you only receive the **changed fields** in the payload — not the full entity. If your save logic needs all fields (e.g. to call an external API or function module), you'd normally need an extra `READ ENTITIES` call.

`with full data` eliminates that extra round-trip.

## Syntax

```abap
" Additional save — runs after managed save
define behavior for ZR_MyEntity
{
  with additional save with full data
}

" Unmanaged save — replaces managed save entirely
define behavior for ZR_MyEntity
{
  with unmanaged save with full data
}
```

## Without full data (default behavior)

```abap
METHOD save_modified.
  LOOP AT update-myentity ASSIGNING FIELD-SYMBOL(<entity>).
    " Only changed fields are populated here
    " Need to READ to get full data:
    READ ENTITIES OF ZR_MyEntity
      ENTITY MyEntity ALL FIELDS WITH ...
  ENDLOOP.
ENDMETHOD.
```

## With full data

```abap
METHOD save_modified.
  LOOP AT update-myentity ASSIGNING FIELD-SYMBOL(<entity>).
    " All fields are already populated — no READ needed
    CALL FUNCTION 'ZBAPI_SAVE'
      EXPORTING
        id     = <entity>-id
        name   = <entity>-name
        amount = <entity>-amount.
  ENDLOOP.
ENDMETHOD.
```

## Important: %control Still Uses Delta

Even with `with full data`, the `%control` component group still only flags **changed** fields. The entity data is full; the control flags are not.

```abap
" This still reflects only changed fields:
IF <entity>-%control-name = if_abap_behv=>mk-on.
  " name was changed
ENDIF.
```

## When to Use

| Scenario | Use |
|---|---|
| Calling external BAPI / function module | `with full data` — saves a READ |
| Posting to a legacy system needing complete data | `with full data` |
| Only need to know which fields changed | Default (no `with full data`) |
