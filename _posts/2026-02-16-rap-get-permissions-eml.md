---
title: "RAP EML: Read Instance Permissions with GET PERMISSIONS ONLY"
date: 2026-02-16 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, eml, permissions, get-permissions, instance-features]
---

When you need to check what operations are enabled for a RAP entity instance — without reading or modifying data — use `GET PERMISSIONS ONLY` in EML.

## Syntax

```abap
READ ENTITIES OF ZR_MyEntity
  ENTITY MyEntity
  ALL FIELDS WITH CORRESPONDING #( keys )
  RESULT DATA(lt_entities)
  FAILED DATA(lt_failed)
  REPORTED DATA(lt_reported)
  IN LOCAL MODE.
```

For permissions only (no data fetch):

```abap
READ ENTITIES OF ZR_MyEntity
  ENTITY MyEntity
  GET PERMISSIONS ONLY
  WITH CORRESPONDING #( keys )
  RESULT DATA(lt_permissions)
  FAILED DATA(lt_failed).
```

## Check Specific Permissions

```abap
LOOP AT lt_permissions ASSIGNING FIELD-SYMBOL(<perm>).
  " Check if update is enabled
  IF <perm>-%update = if_abap_behv=>fc-o-enabled.
    " Update is allowed
  ENDIF.

  " Check if delete is enabled
  IF <perm>-%delete = if_abap_behv=>fc-o-enabled.
    " Delete is allowed
  ENDIF.

  " Check if a custom action is enabled
  IF <perm>-%action-edit = if_abap_behv=>fc-o-enabled.
    " Edit action is available
  ENDIF.

  " Check if a field is read-only
  IF <perm>-%field-id = if_abap_behv=>fc-f-read_only.
    " ID field is read-only
  ENDIF.
ENDLOOP.
```

## Feature Control Constants

| Constant | Meaning |
|---|---|
| `fc-o-enabled` | Operation/action is enabled |
| `fc-o-disabled` | Operation/action is disabled |
| `fc-f-read_only` | Field is read-only |
| `fc-f-mandatory` | Field is mandatory |
| `fc-f-unrestricted` | Field is editable |

## When to Use

- Dynamically show/hide buttons in custom UI before calling an action
- Validate authorization before attempting a write operation
- Build conditional logic based on current instance state
