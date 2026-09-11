---
title: "JSON Conversion in ABAP Using /ui2/cl_json"
date: 2025-09-15 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, json, serialization, deserialization, ui2-cl-json]
---

The `/ui2/cl_json` class is the go-to tool for JSON serialization and deserialization in ABAP. Here's a complete reference for all its key capabilities.

## 1. Table → JSON (Serialize)

```abap
DATA(lv_json) = /ui2/cl_json=>serialize(
    data        = lt_employees
    pretty_name = /ui2/cl_json=>pretty_mode-camel_case
).
```

## 2. JSON → Table with Field Name Mapping

Use when JSON field names differ from your ABAP structure names:

```abap
/ui2/cl_json=>deserialize(
    EXPORTING
        json          = lv_json
        name_mappings = VALUE #(
            ( abap = 'EMPID'    json = 'EMPLOYEEID'   )
            ( abap = 'EMPNAME'  json = 'EMPLOYEENAME' )
        )
    CHANGING
        data = lt_employees
).
```

## 3. Direct JSON → Table

When JSON field names already match your ABAP field names:

```abap
/ui2/cl_json=>deserialize(
    EXPORTING json = lv_json
    CHANGING  data = lt_employees
).
```

## 4. Conversion Exits

Applies domain conversion — e.g. language code `'E'` → `'EN'`:

```abap
/ui2/cl_json=>deserialize(
    EXPORTING
        json             = lv_json
        conversion_exits = abap_true
    CHANGING
        data = lt_employees
).
```

## 5. Unknown / Dynamic JSON Structure

When you don't know the structure upfront, use `generate()`:

```abap
DATA: lr_data TYPE REF TO data.

/ui2/cl_json=>deserialize(
    EXPORTING json = lv_json
    CHANGING  data = lr_data
).

" Access via dereferencing
DATA(lv_value) = lr_data->*.
```

## Quick Reference

| Method | Use case |
|---|---|
| `serialize()` | ABAP → JSON |
| `deserialize()` | JSON → ABAP |
| `pretty_name = camel_case` | camelCase field names in output |
| `name_mappings` | map different field names |
| `conversion_exits = abap_true` | apply domain conversions |
| `generate()` | dynamic/unknown JSON structures |
