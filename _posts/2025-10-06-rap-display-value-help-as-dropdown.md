---
title: "RAP: Display Value Help as Radio Buttons Instead of Dropdown"
date: 2025-10-06 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, fiori, value-help, radio-buttons, annotations]
---

By default, fixed value helps in SAP Fiori open a dialog. With two annotations and a manifest setting, you can turn them into **radio buttons** — no dialog, single click.

## Step 1: Add Annotations to `annotation.xml`

```xml
<Annotations Target="ZC_MyEntity/Status">
    <Annotation Term="Common.ValueListWithFixedValues"/>
    <Annotation Term="Common.ValueListShowValuesImmediately"/>
</Annotations>
```

- `ValueListWithFixedValues` — marks this as a closed list (no free text)
- `ValueListShowValuesImmediately` — renders values as radio buttons directly

## Step 2: Configure `manifest.json`

```json
"controlConfiguration": {
    "Status": {
        "fieldEditStyle": "RadioButtons",
        "radioButtonsHorizontalLayout": true
    }
}
```

Set `radioButtonsHorizontalLayout: false` (or omit) for vertical layout.

## Result

| Before | After |
|---|---|
| Dropdown → opens dialog | Radio buttons inline in the form |

## Bonus: Set a Default Value via Determination

```abap
METHOD set_default_status.
  READ ENTITIES OF ZR_MyEntity
    ENTITY MyEntity
    FIELDS ( Status )
    WITH CORRESPONDING #( keys )
    RESULT DATA(lt_entities).

  MODIFY ENTITIES OF ZR_MyEntity
    ENTITY MyEntity
    UPDATE FIELDS ( Status )
    WITH VALUE #(
      FOR entity IN lt_entities
      WHERE ( Status IS INITIAL )
      ( %key  = entity-%key
        Status = 'OPEN' )
    ).
ENDMETHOD.
```

The determination also prevents users from clearing the selection — the field always has a value.
