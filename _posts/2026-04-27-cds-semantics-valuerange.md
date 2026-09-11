---
title: "CDS: @Semantics.valueRange — Define Acceptable Min/Max Boundaries"
date: 2026-04-27 08:00:00 +0530
categories: [CDS]
tags: [cds, abap, rap, annotations, validation, semantics, value-range]
---

`@Semantics.valueRange` communicates valid value boundaries for a CDS field to consumers — clients can use this to drive UI validation without a round-trip to the backend.

## Syntax

```abap
@Semantics.valueRange.minimum: '<value>'
@Semantics.valueRange.maximum: '<value>'
FieldName;
```

Works on fully ordered types: characters, strings, numbers, and custom types.

## Date Range Example

```abap
annotate view ZC_TravelEntity with
{
  @Semantics.valueRange.minimum: '20260101'
  @Semantics.valueRange.maximum: '20261231'
  BeginDate;
}
```

Only dates in 2026 are acceptable.

## Numeric Range Example

```abap
annotate view ZC_BookingEntity with
{
  @Semantics.valueRange.minimum: '0'
  @Semantics.valueRange.maximum: '100'
  BookingFee;
}
```

## Combined Example

```abap
annotate view ZC_ProductEntity with
{
  @Semantics.valueRange.minimum: '1'
  @Semantics.valueRange.maximum: '9999'
  Quantity;

  @Semantics.valueRange.minimum: '0.00'
  Price;

  @Semantics.valueRange.minimum: '20260101'
  ValidFrom;
}
```

## Notes

- Values must be passed as **strings** even for numeric fields
- The annotation is **informational** — enforcement still happens in validations
- Consumers (Fiori, OData clients) can read these boundaries from metadata to provide early UI feedback
- Works alongside RAP validations — they complement each other
