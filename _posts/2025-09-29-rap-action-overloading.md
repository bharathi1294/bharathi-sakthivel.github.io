---
title: "RAP: Show Parent Entity Actions in Child Entity Table"
date: 2025-09-29 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, fiori, actions, behavior-definition]
---

A common requirement: you have a Root entity and a Child entity, and you want an action button to appear in the **Child's table** — but the action logic lives on the **Root entity**.

This is called **Action Overloading** in RAP.

## Step 1: Behavior Definition — Define Action on Root

```abap
define behavior for ZR_Root
{
  action createItemFromRoot result [1] $self;

  side effects {
    field AnyField affects entity _Child;
  }
}
```

The `side effects` block tells RAP to refresh the Child table after the action runs.

## Step 2: Projection Behavior — Expose the Root Action

```abap
define behavior for ZC_Root
{
  use action createItemFromRoot;
}
```

## Step 3: Child Metadata Extension — Add Button via Annotation

In the Child entity's metadata extension, reference the Root action using its **qualified name**:

```abap
@UI.lineItem: [
  {
    type:   #FOR_ACTION,
    dataAction: 'ZC_Root.createItemFromRoot',
    label: 'Create Item'
  }
]
SomeField;
```

The dot notation `ZC_Root.createItemFromRoot` is the key — it tells Fiori the action belongs to the Root projection but should appear in the Child table.

## Step 4: Action Implementation

```abap
METHOD createitemfromroot.
  " keys contains the Root instance
  LOOP AT keys ASSIGNING FIELD-SYMBOL(<key>).
    MODIFY ENTITIES OF ZR_Root
      ENTITY Root
      CREATE BY _Child
      FIELDS ( Date Name )
      WITH VALUE #( (
          %cid      = |NEWITEM{ sy-index }|
          %key      = <key>-%key
          Date      = sy-datum
          Name      = |Item { sy-index }|
      ) ).
  ENDLOOP.
ENDMETHOD.
```

## Result

Users see an **"Create Item"** button in the Child table. Clicking it triggers Root-level logic that creates child records, and the table auto-refreshes via the side effect.
