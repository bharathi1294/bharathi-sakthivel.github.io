---
title: "RAP: Editable Virtual Elements for SAP Long Text"
date: 2025-10-20 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, virtual-element, long-text, save-modified, augment]
---

SAP Long Text (`READ_TEXT` / `SAVE_TEXT`) can't be stored directly in RAP database tables. The solution is an **editable virtual element** backed by a staging field.

## Architecture

```
UI Input → Virtual Element (VirtualLongText)
         → Staging Field (LongText, max 1333 chars) in draft
         → save_modified → SAVE_TEXT function module
         → RAP commit → GET refresh → display updated text
```

## Step 1: Base CDS View — Staging Field

```abap
define view entity ZR_MyEntity
  as select from zmy_table
{
  key id,
  @EndUserText.label: 'Long Text (staging)'
  long_text          -- max 1333 chars in DB table
}
```

## Step 2: Projection View — Virtual Element

```abap
define view entity ZC_MyEntity
  as projection on ZR_MyEntity
{
  key id,

  @ObjectModel.virtualElementCalculatedBy: 'ABAP:ZCL_READ_LONG_TEXT'
  @EndUserText.label: 'Long Text'
  virtual VirtualLongText : abap.char( 1333 )
}
```

## Step 3: Read Class — ZCL_READ_LONG_TEXT

```abap
METHOD if_sadl_exit_calc_element_read~calculate.
  LOOP AT it_original_data ASSIGNING FIELD-SYMBOL(<entity>).
    CALL FUNCTION 'READ_TEXT'
      EXPORTING
        id     = 'ST'
        language = sy-langu
        name   = <entity>-id
        object = 'ZMY_OBJ'
      TABLES
        lines  = DATA(lt_lines).

    <entity>-VirtualLongText = concat_lines_of(
        table = lt_lines sep = cl_abap_char_utilities=>newline ).
  ENDLOOP.
ENDMETHOD.
```

## Step 4: Behavior Definition

```abap
define behavior for ZR_MyEntity
{
  with additional save
  ...
}

define behavior for ZC_MyEntity
{
  augment;
  ...
}
```

## Step 5: Augment Methods — Map Virtual ↔ Staging

```abap
" In projection behavior handler
METHOD augment_create.
  MODIFY ENTITIES OF ZR_MyEntity
    ENTITY MyEntity
    UPDATE FIELDS ( long_text )
    WITH VALUE #(
      FOR entity IN entities
      ( %key      = entity-%key
        long_text = entity-VirtualLongText )
    ).
ENDMETHOD.
```

## Step 6: save_modified — Call SAVE_TEXT

```abap
METHOD save_modified.
  LOOP AT update-myentity ASSIGNING FIELD-SYMBOL(<entity>).
    CHECK <entity>-%control-long_text = if_abap_behv=>mk-on.

    CALL FUNCTION 'SAVE_TEXT'
      EXPORTING
        header = VALUE thead(
          tdid    = 'ST'
          tdspras = sy-langu
          tdname  = <entity>-id
          tdobject = 'ZMY_OBJ' )
      TABLES
        lines = " build from <entity>-long_text
      EXCEPTIONS OTHERS = 4.
  ENDLOOP.
ENDMETHOD.
```

No explicit `COMMIT WORK` needed — RAP handles the commit automatically, then triggers a GET to refresh the display.
