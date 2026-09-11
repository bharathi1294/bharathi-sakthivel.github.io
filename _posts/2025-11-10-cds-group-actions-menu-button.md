---
title: "CDS: Group Multiple Actions into a Menu Button with #FOR_ACTION_GROUP"
date: 2025-11-10 08:00:00 +0530
categories: [CDS]
tags: [cds, fiori, rap, actions, annotations, menu-button]
---

When you have multiple related actions (e.g. "Accept" and "Reject"), showing them as separate buttons clutters the toolbar. Use `#FOR_ACTION_GROUP` to group them under a single **menu button**.

## Annotation

```abap
@UI.lineItem: [
  {
    type:          #FOR_ACTION_GROUP,
    label:         'Change Status',
    actionGroupId: 'menu-ChangeStatus',
    position:      10
  },
  {
    type:          #FOR_ACTION,
    dataAction:    'acceptTravel',
    label:         'Accept',
    actionGroupId: 'menu-ChangeStatus'
  },
  {
    type:          #FOR_ACTION,
    dataAction:    'rejectTravel',
    label:         'Reject',
    actionGroupId: 'menu-ChangeStatus'
  }
]
TravelStatus;
```

## How it Works

1. The entry with `type: #FOR_ACTION_GROUP` creates the **menu button container** with a label
2. Each action entry with the same `actionGroupId` becomes a **menu item** inside it
3. The `position` on the group entry controls where the menu button appears in the toolbar

## Result

Instead of:
```
[ Accept ]  [ Reject ]
```

You get:
```
[ Change Status ▼ ]
  ├── Accept
  └── Reject
```

## Works on Both

- **List Report** toolbar (multi-select)
- **Object Page** header toolbar

> Tested on SAP BTP ABAP Environment and S/4HANA Public Cloud (latest version).
