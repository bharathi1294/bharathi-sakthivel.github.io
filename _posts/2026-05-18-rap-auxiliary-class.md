---
title: "The Usage of Auxiliary Class in RAP"
date: 2026-05-18 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, auxiliary-class, eml, local-mode]
description: "Learn how to use a RAP Auxiliary Class to execute EML with IN LOCAL MODE from outside a behavior class, such as in a background job."
---

We recently had a requirement to update a field of a RAP BO using an EML `MODIFY` statement from a background job. That field was disabled in the UI using feature instance control, so the EML update failed with errors or dumps.

Normally, we use `IN LOCAL MODE` in EML to bypass feature controls, authorizations, and prechecks. But `IN LOCAL MODE` works only inside behavior classes.

To solve this, we used an **Auxiliary Class** and moved the EML logic (with `IN LOCAL MODE`) into it. Since an auxiliary class behaves like a behavior class and is mapped to a single BO, it allows the update without feature checks.

> **Note:** `COMMIT ENTITIES` is not supported in this class.

**Definition in BDEF:**

```abap
managed implementation in class cl_bil_class unique;
strict ( 2 );
auxiliary class class_name1, class_name2;

define behavior for bdef_name alias BdefName
{
  ...
}
```

**Class Definition:**

```abap
CLASS class_name1 DEFINITION PUBLIC
  FOR BEHAVIOR OF bdef_name.
  PUBLIC SECTION.
    METHODS eml_with_local_mode.
ENDCLASS.

CLASS class_name1 IMPLEMENTATION.
  METHOD eml_with_local_mode.
    MODIFY ENTITIES OF bdef_name IN LOCAL MODE
    ...
  ENDMETHOD.
ENDCLASS.
```

> **Availability:** SAP S/4HANA 2025, latest Public Cloud, and ABAP Environment on SAP BTP.

For more information, refer to the [SAP Help documentation](https://help.sap.com/doc/abapdocu_latest_index_htm/latest/en-US/ABAPRAP_AUXILIARY_CLASS.html).
