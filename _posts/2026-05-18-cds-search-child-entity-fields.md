---
title: "CDS: Enable Global Search on Child Entity Fields in List Report"
date: 2026-05-18 08:00:00 +0530
categories: [CDS]
tags: [cds, fiori, annotations, search, list-report, child-entity, odata-v4]
description: "Learn how to extend the global search in a Fiori List Report to search child entity fields using @Search.defaultSearchElement on associations."
---

By default, the global search in a Fiori List Report only searches root entity fields. You can extend it to search fields on child/associated entities too.

## Step 1: Mark the Association in the Root Entity

```abap
" ZC_Travel — Root Entity
@Search.defaultSearchElement: true
_Booking;    " expose the association as searchable
```

## Step 2: Mark Searchable Fields in the Child Entity

```abap
" ZC_Booking — Child Entity
@Search.defaultSearchElement: true
CarrierName;

@Search.defaultSearchElement: true
ConnectionId;
```

## Full Example

```abap
" Root CDS Metadata Extension
annotate view ZC_Travel with
{
  @Search.defaultSearchElement: true
  TravelId;

  @Search.defaultSearchElement: true
  _Booking;
}

" Child CDS Metadata Extension
annotate view ZC_Booking with
{
  @Search.defaultSearchElement: true
  CarrierName;

  @Search.defaultSearchElement: true
  BookingId;
}
```

## Result

When a user types in the global search bar of the Travel List Report, it searches across:
- `TravelId` (root)
- `CarrierName` (booking child)
- `BookingId` (booking child)

## Optional: Fuzzy Search

```abap
@Search.fuzzinessThreshold: 0.8
CarrierName;
```

Values between 0 and 1 — higher = more strict matching. Add this to child entity fields for typo-tolerant search.
