---
title: "RAP: Hide/Show Fields Immediately Based on Another Field Value"
date: 2026-03-02 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, cds, dynamic-ui, hide-fields, determination, side-effects, field-control]
---

Show or hide a field the **moment** the user changes a related field — no page refresh, no save required.

## Scenario

When `EmployeeType` changes to `'FT'` (Full Time), show `MobileNumber`. For `'PT'` (Part Time), hide it.

## Step 1: CDS — Derived Boolean Field

```abap
define view entity ZR_Employee
  as select from zemp_table
{
  key emp_id,
  employee_type,
  mobile_number,

  " Derived boolean: true when Full Time
  case employee_type
    when 'FT' then cast( 'X' as abap_boolean preserving type )
    else            cast( '' as abap_boolean preserving type )
  end as IsFullTimeEmployee
}
```

## Step 2: Projection View — Expose with @UI.hidden

```abap
define view entity ZC_Employee
  as projection on ZR_Employee
{
  key emp_id,
  employee_type,

  @UI.hidden: #( IsFullTimeEmployee )   " hide when false
  mobile_number,

  IsFullTimeEmployee
}
```

## Step 3: Behavior Definition — Side Effect

```abap
define behavior for ZR_Employee
{
  determination setVisibility on modify { field EmployeeType; }

  side effects {
    field EmployeeType affects field IsFullTimeEmployee;
  }
}
```

## Step 4: Determination Implementation

```abap
METHOD setVisibility.
  READ ENTITIES OF ZR_Employee IN LOCAL MODE
    ENTITY Employee
    FIELDS ( EmployeeType )
    WITH CORRESPONDING #( keys )
    RESULT DATA(lt_emp).

  MODIFY ENTITIES OF ZR_Employee IN LOCAL MODE
    ENTITY Employee
    UPDATE FIELDS ( IsFullTimeEmployee )
    WITH VALUE #(
      FOR emp IN lt_emp
      ( %key             = emp-%key
        IsFullTimeEmployee = xsdbool( emp-EmployeeType = 'FT' ) )
    ).
ENDMETHOD.
```

## Result

| User types `EmployeeType` | `MobileNumber` |
|---|---|
| `FT` | Immediately visible |
| `PT` | Immediately hidden |

The side effect triggers the UI to re-read `IsFullTimeEmployee` after the determination runs — the field appears or disappears without any navigation or page reload.
