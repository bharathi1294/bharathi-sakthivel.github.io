---
title: "RAP Type Mapping Constants"
date: 2026-09-06 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, type-mapping, constants, bdef]
---

Have you ever faced a RAP scenario where a field always needs to store the same fixed value in the table? Earlier, we handled this using determinations or `get defaults` to set default values. Now, it's much simpler with **RAP Type Mapping – Constant**.

You can directly define a fixed value in the RAP type mapping using the `constant` syntax. This is especially useful when:
- Multiple entities are mapped to the same persistent table based on one value
- A specific field must always store a constant value

**Example:** In one RAP BO, the employee type is always `'FT'`, and in another it is `'PT'`, both stored in the same table.

```abap
define behavior for ZI_FullTimeEmployee
persistent table zemployee_t
...
{
  mapping for zemployee_t corresponding
  {
    EmployeeId    = employee_id;
    EmployeeName  = employee_name;
    EmployeeType  = constant 'FT';
  }
}

define behavior for ZI_PartTimeEmployee
persistent table zemployee_t
...
{
  mapping for zemployee_t corresponding
  {
    EmployeeId    = employee_id;
    EmployeeName  = employee_name;
    EmployeeType  = constant 'PT';
  }
}
```

> **Availability:** Currently available only in SAP Public Cloud and SAP BTP ABAP Environment.

![RAP Type Mapping Constant](/assets/images/rap_type_mapping_constant.jpg)
