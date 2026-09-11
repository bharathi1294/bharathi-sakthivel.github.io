---
title: "CDS: Input Masks and Regex Validation with @UI.inputMask"
date: 2025-11-24 08:00:00 +0530
categories: [CDS]
tags: [cds, fiori, rap, annotations, input-mask, validation, ui]
---

A new CDS annotation `@UI.inputMask` lets you define input masks and regex validation directly — no custom validation logic needed.

> Available in **SAP S/4HANA Public Cloud** (latest) and **BTP ABAP Environment (Steampunk)**.

## Basic Mask

```abap
@UI.inputMask: {
  mask:              'AAAA-AA-XX-XXX',
  placeholderSymbol: '_',
  rules: [
    { maskSymbol: 'A', regularExpression: '[a-zA-Z]'   },
    { maskSymbol: 'X', regularExpression: '[a-zA-Z0-9]' }
  ]
}
reference_number;
```

**Input placeholder shown:** `____-__-__-___`  
**Valid input:** `ABCD-EF-A1-B2C`

- `A` → letters only
- `X` → alphanumeric

## Fixed Prefix

Characters not in any rule are treated as **literals**:

```abap
@UI.inputMask: {
  mask:  'CONST-AA-XX-XXX',
  rules: [
    { maskSymbol: 'A', regularExpression: '[a-zA-Z]'   },
    { maskSymbol: 'X', regularExpression: '[a-zA-Z0-9]' }
  ]
}
swift_code;
```

`CONST` is auto-filled — user only types the variable part.

## Escaping a Mask Symbol

Use `^` to treat a mask symbol as a literal:

```abap
@UI.inputMask: {
  mask:  'C^AR-AA-XX-XXX',   " ^ escapes the A in CAR
  rules: [
    { maskSymbol: 'A', regularExpression: '[a-zA-Z]'   },
    { maskSymbol: 'X', regularExpression: '[a-zA-Z0-9]' }
  ]
}
car_reference;
```

**Shown:** `CAR-__-__-___`

## Key Points

- Mask applies at **UI layer only** — stored value has no mask characters
- **No dynamic masks** yet — must be defined statically
- Replaces custom JS/ABAP validation for common format patterns (SWIFT, ref numbers, IDs)
