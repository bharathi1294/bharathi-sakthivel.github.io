---
title: "CDS: Control Value Help Sort Order and Data Load Behavior"
date: 2026-05-11 08:00:00 +0530
categories: [CDS]
tags: [cds, abap, rap, annotations, value-help, sort, performance]
description: "Learn how to control value help sort order and lazy loading in CDS using @UI.presentationVariant and @Consumption.valueHelpDefault annotations."
---

Two annotations that improve value help UX: control **when data loads** and **how it's sorted**.

## 1. Lazy Load — Don't Fetch on Open

By default, value help dialogs fetch all data when opened. For large datasets, this is slow. Force explicit user action first:

```abap
@Consumption.valueHelpDefault.fetchValues: #ON_EXPLICIT_REQUEST
define view entity ZVH_Customer
  as select from kna1
{
  key kunnr as CustomerID,
      name1 as CustomerName
}
```

| Value | Behavior |
|---|---|
| `#INITIAL_AND_EXPLICIT_REQUEST` (default) | Loads all data on open |
| `#ON_EXPLICIT_REQUEST` | Loads only after user searches or clicks "Go" |

Use `#ON_EXPLICIT_REQUEST` for value helps with 10k+ records.

## 2. Default Sort Order

```abap
@UI.presentationVariant: [{
  sortOrder: [{
    by:        'CustomerName',
    direction: #DESC
  }]
}]
define view entity ZVH_Customer
  as select from kna1
{
  key kunnr as CustomerID,
      name1 as CustomerName
}
```

Multiple sort fields:

```abap
@UI.presentationVariant: [{
  sortOrder: [
    { by: 'CountryCode', direction: #ASC  },
    { by: 'CustomerName', direction: #ASC }
  ]
}]
```

## Combining Both

```abap
@Consumption.valueHelpDefault.fetchValues: #ON_EXPLICIT_REQUEST

@UI.presentationVariant: [{
  sortOrder: [{
    by:        'CustomerName',
    direction: #ASC
  }]
}]
define view entity ZVH_Customer
  as select from kna1
{
  key kunnr as CustomerID,
      name1 as CustomerName,
      land1 as CountryCode
}
```

Data only loads when the user explicitly requests it, and results are sorted alphabetically by name.
