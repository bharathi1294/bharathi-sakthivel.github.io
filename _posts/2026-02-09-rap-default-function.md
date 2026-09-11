---
title: "RAP: Pre-fill Create Form Fields with Default Functions"
date: 2026-02-09 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, fiori, default-function, create, bdef]
---

When users open the create dialog in a Fiori app, fields are blank. A **default function** in RAP lets you pre-fill fields automatically — currency, date, status, or any business default.

## Step 1: Behavior Definition

```abap
define behavior for ZR_MyEntity
{
  create {
    default function GetDefaultsForCreate;
  }
  ...
}
```

## Step 2: Projection Behavior Definition

```abap
define behavior for ZC_MyEntity
{
  use function GetDefaultsForCreate;
  ...
}
```

## Step 3: Implementation

```abap
METHOD GetDefaultsForCreate.
  result = VALUE #(
    FOR key IN keys
    (
      %cid   = key-%cid
      %param = VALUE #(
        CurrencyCode = 'INR'
        ValidFrom    = sy-datum
        Status       = 'OPEN'
      )
    )
  ).
ENDMETHOD.
```

## How it Works

- `keys` contains the `%cid` (client-side ID) for each new record being created
- `%param` carries the default field values
- The UI receives these defaults before the create dialog renders — fields appear pre-filled

## Result

When the user clicks **"New"**, the create dialog opens with:
- **Currency Code** = INR
- **Valid From** = today's date
- **Status** = OPEN

No determination needed — defaults are set before the record even exists in the draft.
