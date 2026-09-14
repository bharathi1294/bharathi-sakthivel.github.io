---
title: "ABAP: Create CSV Files Using CL_CSV_FACTORY"
date: 2026-06-15 08:00:00 +0530
categories: [File Handling]
tags: [abap, csv, cl-csv-factory, file-handling, download]
description: "Learn how to create CSV files from ABAP internal tables using CL_CSV_FACTORY with custom headers and separators — no string concatenation needed."
---

`CL_CSV_FACTORY` is the clean, modern way to generate CSV files from internal tables in ABAP — no manual string concatenation needed.

## Simple CSV — Auto Headers from Field Names

```abap
DATA lt_data TYPE TABLE OF zsales_order WITH DEFAULT KEY.
" ... populate lt_data ...

DATA(lo_csv) = cl_csv_factory=>create_csv_writer(
    iv_separator = ';'
).

lo_csv->write_table(
    EXPORTING it_data     = lt_data
    IMPORTING ev_csv_data = DATA(lv_csv_xstring)
).

" lv_csv_xstring is ready to download or attach to email
```

## Advanced CSV — Custom Column Headers

```abap
DATA lt_catalog TYPE cl_csv_factory=>tt_field_catalog.

APPEND VALUE #(
    fieldname = 'VBELN'
    heading   = 'Sales Order'
) TO lt_catalog.

APPEND VALUE #(
    fieldname = 'ERDAT'
    heading   = 'Created On'
    format    = cl_csv_factory=>mc_format_date_iso
) TO lt_catalog.

APPEND VALUE #(
    fieldname = 'NETWR'
    heading   = 'Net Value'
) TO lt_catalog.

DATA(lo_csv) = cl_csv_factory=>create_csv_writer(
    iv_separator = ','
).

lo_csv->write_table(
    EXPORTING
        it_data         = lt_sales_orders
        it_field_catalog = lt_catalog
    IMPORTING
        ev_csv_data     = DATA(lv_csv_xstring)
).
```

## Download to Frontend

```abap
DATA lt_binary TYPE solix_tab.
lt_binary = cl_bcs_convert=>xstring_to_solix( lv_csv_xstring ).

cl_gui_frontend_services=>gui_download(
    EXPORTING
        filename         = 'sales_orders.csv'
        filetype         = 'BIN'
        bin_filesize     = xstrlen( lv_csv_xstring )
    CHANGING
        data_tab         = lt_binary
).
```

## Date Format Options

| Constant | Format |
|---|---|
| `mc_format_date_iso` | `YYYY-MM-DD` |
| `mc_format_date_user` | User locale format |

`CL_CSV_FACTORY` generates the header row automatically from either field names or your custom catalog — no string template gymnastics.
