---
title: "EML COMMIT ENTITIES — Capturing Validation Responses"
date: 2026-08-28 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, eml, commit-entities, validation]
---

When using EML to create or update entries, we typically populate all required fields and then use `MODIFY ENTITIES` followed by `COMMIT ENTITIES` to persist the data.

However, your RAP BO may have a validation defined at the **save** phase. These validations are triggered after `COMMIT ENTITIES` is executed. If the validation fails and returns an error message, simply using `COMMIT ENTITIES` won't give you access to those messages.

This is where you need to use `COMMIT ENTITIES RESPONSES`:

```abap
COMMIT ENTITIES
  RESPONSES
    FAILED   DATA(lt_failed)
    REPORTED DATA(lt_reported).

IF lt_failed IS NOT INITIAL.
  " handle failure
ENDIF.
```

**Example:** A validation at the save phase checks whether the `ValidTo` date is in the past. Without `RESPONSES`, a failed validation would silently discard the data with no way to capture the error.

For more details and examples, refer to the [ABAP EML Cheat Sheet](https://github.com/SAP-samples/abap-cheat-sheets/blob/main/08_EML_ABAP_for_RAP.md#abap-for-rap-entity-manipulation-language-abap-eml).

![COMMIT ENTITIES RESPONSES in EML](/assets/images/eml_commit_responses.jpg)
