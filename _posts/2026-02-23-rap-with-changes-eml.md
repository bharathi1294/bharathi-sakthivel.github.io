---
title: "Accessing Change Information in EML Using WITH CHANGES"
date: 2026-02-23 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, eml, with-changes, determination, validation]
---

Using `READ ... WITH CHANGES` in ABAP EML lets you access accumulated change information for RAP BO instances within a RAP transaction.

It can be used only for **managed RAP BOs**, inside implementation classes, and must be combined with `IN LOCAL MODE`. It is not supported for unmanaged BOs, read-by-association, or RAP functions.

The read result includes the `%chg` component, which indicates whether an instance was created, updated, deleted, or unchanged. The `%control` structure shows which fields changed.

This is especially useful in determinations or validations involving multiple fields, where you want to know:
- Which operation triggered the logic (create or update)
- Which specific fields were changed

**Variant 1:**

```abap
READ ENTITIES OF ZRootEntity IN LOCAL MODE
  ENTITY RootEntity WITH CHANGES
  ALL FIELDS WITH CORRESPONDING #( keys )
  RESULT DATA(lt_result1).
```

**Variant 2:**

```abap
READ ENTITY IN LOCAL MODE ZRootEntity
  WITH CHANGES
  ALL FIELDS WITH CORRESPONDING #( keys )
  RESULT DATA(lt_result2).
```

**Example:** If `carrname` is changed in the UI from "Air India" to "Air India Updated", the control indicator is set to `01` at the time of a determination, indicating the field change.

> **Availability:** Currently released only in SAP S/4HANA Public Cloud and SAP BTP ABAP Environment.

For more information, refer to the [ABAP EML Cheat Sheet](https://github.com/SAP-samples/abap-cheat-sheets/blob/main/08_EML_ABAP_for_RAP.md#accessing-change-information).

![WITH CHANGES EML result](/assets/images/with_changes.png)
