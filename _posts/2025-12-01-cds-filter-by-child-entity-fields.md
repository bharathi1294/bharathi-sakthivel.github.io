---
title: "CDS: Filter List Report by Child Entity Fields"
date: 2025-12-01 08:00:00 +0530
categories: [CDS]
tags: [cds, fiori, rap, odata-v4, list-report, filter, annotations]
---

Need a filter bar filter that targets a field on a **child/associated entity** rather than the root? One annotation does it.

> Works with **OData V4** only.

## Annotation — on Root Metadata Extension

```abap
@UI.selectionField: [
  {
    element:  '_Booking.AirlineID',
    position: 50
  }
]
TravelId;
```

The `element` uses the **association path** notation: `_AssocName.FieldName`.

## How it Works

When the user selects an AirlineID in the filter bar, the OData V4 query filters `Travel` records that have at least one `Booking` with that `AirlineID`. No custom filter logic needed — Fiori Elements and OData V4 handle the join automatically.

## Full Example

```abap
" ZC_Travel — Metadata Extension
@Metadata.layer: #CUSTOMER

annotate view ZC_Travel with
{
  @UI.selectionField: [
    { element: 'TravelId',         position: 10 },
    { element: 'AgencyId',         position: 20 },
    { element: '_Booking.AirlineID', position: 50 }   " <-- child field
  ]
  TravelId;
}
```

## Requirements

| Requirement | Detail |
|---|---|
| OData version | V4 only (V2 not supported) |
| Association | Must be a to-N or to-1 association on the root CDS view |
| Fiori floorplan | List Report Object Page |
