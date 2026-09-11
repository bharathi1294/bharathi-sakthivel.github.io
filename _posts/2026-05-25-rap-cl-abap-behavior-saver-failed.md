---
title: "CL_ABAP_BEHAVIOR_SAVER_FAILED in RAP"
date: 2026-05-25 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, behavior-saver, save-modified, bapi]
---

We recently had a scenario in RAP (Managed with Unmanaged Save) where we needed to call a BAPI inside `save_modified`. This BAPI performs its own validations, and based on the result, we wanted to stop the save process if any errors occurred.

The challenge was related to the method signature of `save_modified`. By default, it only provides the `reported` parameter — there is no `failed` parameter available. Because of this, even when the BAPI returned an error, we had no direct way to populate `failed` and properly stop the save sequence.

To handle this, we changed the saver class inheritance from `cl_abap_behavior_saver` to `cl_abap_behavior_saver_failed`.

After doing this, the `failed` parameter became available. This allowed us to populate `failed` when the BAPI returned an error and correctly stop the execution, which was then reflected properly in the UI.

**Inheriting from `cl_abap_behavior_saver` (no `failed` parameter):**

```abap
CLASS lsc_class DEFINITION INHERITING FROM cl_abap_behavior_saver.
  PROTECTED SECTION.
    METHODS save_modified REDEFINITION.
ENDCLASS.

CLASS lsc_class IMPLEMENTATION.
  METHOD save_modified.
    LOOP AT update-entity_name ASSIGNING FIELD-SYMBOL(<fs>).
      reported-entity_name = VALUE #( BASE reported-entity_name
        ( %key = <fs>-%key
          %msg = new_message_with_text(
                   severity = if_abap_behv_message=>severity-error
                   text     = 'some error' ) ) ).
    ENDLOOP.
  ENDMETHOD.
ENDCLASS.
```

**Inheriting from `cl_abap_behavior_saver_failed` (`failed` parameter available):**

```abap
CLASS lsc_class DEFINITION INHERITING FROM cl_abap_behavior_saver_failed.
  PROTECTED SECTION.
    METHODS save_modified REDEFINITION.
ENDCLASS.

CLASS lsc_class IMPLEMENTATION.
  METHOD save_modified.
    LOOP AT update-entity_name ASSIGNING FIELD-SYMBOL(<fs>).
      failed-entity_name = VALUE #( BASE failed-entity_name
        ( %key = <fs>-%key ) ).
      reported-entity_name = VALUE #( BASE reported-entity_name
        ( %key = <fs>-%key
          %msg = new_message_with_text(
                   severity = if_abap_behv_message=>severity-error
                   text     = 'some error' ) ) ).
    ENDLOOP.
  ENDMETHOD.
ENDCLASS.
```

> **Note:** The use of `cl_abap_behavior_saver_failed` is recommended only for exceptional cases. For more details on when and how to use it, refer to the [SAP Help documentation](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/ABENABP_CL_ABAP_BEH_SAVER_FAILED.html).

📄 [View PDF](/bharathi-sakthivel.github.io/assets/pdf/rap_behavior_saver_failed.pdf)

<iframe src="/bharathi-sakthivel.github.io/assets/pdf/rap_behavior_saver_failed.pdf" width="100%" height="600px" style="border:1px solid #ccc;"></iframe>
