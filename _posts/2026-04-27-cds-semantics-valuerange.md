---
title: "Semantics Value Range in CDS"
date: 2026-04-27 08:00:00 +0530
categories: [CDS]
tags: [cds, abap, semantics, value-range]
---

### Annotation: Semantics.valueRange
> You can use this annotation to inform consumers about the value range of a CDS element, allowing to specify minimal and/or maximal values and indicate whether these are exclusive or inclusive. The annotation can be applied to all fully ordered data types, i.e. Characters, Strings, Numbers, or Data Types.
```abap
annotate view ZCRootEntity with {
  ...
  @Semantics.valueRange.minimum: '20260101'
  @Semantics.valueRange.maximum: '20261231'
  BeginDate;

  @Semantics.valueRange.minimum: '0'
  @Semantics.valueRange.maximum: '100'
  BookingFee;
 
}
```