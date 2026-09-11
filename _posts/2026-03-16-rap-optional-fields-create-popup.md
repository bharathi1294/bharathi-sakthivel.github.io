---
title: "RAP: Show Optional Fields in the Create Popup"
date: 2026-03-16 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, fiori, create-popup, optional-fields, determination]
---

By default, Fiori Elements only shows mandatory fields in the create dialog. If you need optional fields there too, use **temporary display fields** backed by a determination.

## Scenario

Create popup should show: `Id` (required) + `FirstName` (optional) + `LastName` (optional).

## Step 1: Add Display Fields to the Entity

```abap
" In your DB table or extension — display-only staging fields
fname_d : first_name_d;
lname_d : last_name_d;
```

## Step 2: Projection View — Annotate Display Fields

```abap
@Consumption.filter.hidden: true
@UI.lineItem: [{ hidden: true }]
@UI.identification: [{ position: 20, label: 'First Name' }]
FnameD;

@Consumption.filter.hidden: true
@UI.lineItem: [{ hidden: true }]
@UI.identification: [{ position: 30, label: 'Last Name' }]
LnameD;
```

- Hidden from list/filter — only visible in the create popup
- `@UI.identification` makes them appear in the dialog

## Step 3: Behavior Definition

```abap
define behavior for ZR_MyEntity
{
  " Display fields are read-only on update (only for create)
  field ( read only : update ) FnameD, LnameD;

  determination setNames on modify { create; }
}
```

## Step 4: Determination — Copy to Real Fields

```abap
METHOD setNames.
  READ ENTITIES OF ZR_MyEntity IN LOCAL MODE
    ENTITY MyEntity
    FIELDS ( FnameD LnameD )
    WITH CORRESPONDING #( keys )
    RESULT DATA(lt_entities).

  MODIFY ENTITIES OF ZR_MyEntity IN LOCAL MODE
    ENTITY MyEntity
    UPDATE FIELDS ( Fname Lname )
    WITH VALUE #(
      FOR entity IN lt_entities
      ( %key  = entity-%key
        Fname = entity-FnameD
        Lname = entity-LnameD )
    ).
ENDMETHOD.
```

## Result

| Create Dialog Shows | After Creation |
|---|---|
| Id (required) | Id stored |
| First Name (optional) | Fname populated from FnameD |
| Last Name (optional) | Lname populated from LnameD |

The display fields (`FnameD`, `LnameD`) are hidden after creation — the real fields (`Fname`, `Lname`) are editable on the object page.
