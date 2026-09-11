---
title: "RAP EML: READ ENTITIES WITH CHANGES — Access Draft Modifications"
date: 2026-02-23 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, eml, with-changes, draft, read-entities]
---

`WITH CHANGES` in EML lets you read entity data **including uncommitted draft changes** — what the user has typed but not yet activated.

## Two Syntax Variants

### Variant 1 — Plural `READ ENTITIES OF`

```abap
READ ENTITIES OF ZRootEntity IN LOCAL MODE
  ENTITY RootEntity WITH CHANGES
  ALL FIELDS WITH CORRESPONDING #( keys )
  RESULT DATA(lt_result).
```

### Variant 2 — Singular `READ ENTITY`

```abap
READ ENTITY IN LOCAL MODE ZRootEntity WITH CHANGES
  ALL FIELDS WITH CORRESPONDING #( keys )
  RESULT DATA(lt_result).
```

Both are equivalent — use whichever matches your existing code style.

## Without vs With CHANGES

```abap
" Without WITH CHANGES → reads the active (activated) version
READ ENTITIES OF ZRootEntity IN LOCAL MODE
  ENTITY RootEntity
  ALL FIELDS WITH CORRESPONDING #( keys )
  RESULT DATA(lt_active).

" With WITH CHANGES → reads current draft (includes unsaved edits)
READ ENTITIES OF ZRootEntity IN LOCAL MODE
  ENTITY RootEntity WITH CHANGES
  ALL FIELDS WITH CORRESPONDING #( keys )
  RESULT DATA(lt_with_draft).
```

## When to Use

| Scenario | Use |
|---|---|
| Determination triggered by field change | `WITH CHANGES` — see what the user just typed |
| Validation on save | Standard read — active data |
| Side effect that needs current draft state | `WITH CHANGES` |
| Reading in a normal action | Standard read |

## Note on Local Mode

`IN LOCAL MODE` bypasses authorization checks — use it inside behavior implementations where the framework has already done the authorization check.
