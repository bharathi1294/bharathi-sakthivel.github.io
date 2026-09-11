---
title: "ABAP: Send an Internal Table as an Excel Attachment via Email"
date: 2026-06-01 08:00:00 +0530
categories: [Email]
tags: [abap, email, excel, bcs, salv, attachment, xlsx]
---

Send any internal table as an `.xlsx` email attachment using SAP's BCS framework and SALV export.

## Architecture

```
Internal Table → SALV ALV → export to XLSX (xstring) → BCS attachment → Email
```

## Step 1: Convert Internal Table to XLSX

```abap
" Use SALV to convert the table to Excel format
DATA lo_result_data TYPE REF TO cl_salv_ex_result_data_table.
DATA lo_columns     TYPE REF TO cl_salv_columns_table.

cl_salv_table=>factory(
    IMPORTING r_salv_table = DATA(lo_salv)
    CHANGING  t_table      = lt_employees
).

lo_columns     = lo_salv->get_columns( ).
lo_result_data = cl_salv_ex_result_data_table=>create(
    r_salv_table = lo_salv
    t_data       = lt_employees
).

" Option 1: XLSX format
DATA lv_xlsx TYPE xstring.
cl_salv_bs_lex=>export_from_result_data_table(
    EXPORTING
        io_result_data_table = lo_result_data
        iv_format            = if_salv_bs_lex_format=>mc_format_xlsx
    IMPORTING
        ev_file_data         = lv_xlsx
).
```

## Step 2: Create and Send Email via BCS

```abap
DATA lo_send_request TYPE REF TO cl_bcs.
DATA lo_document     TYPE REF TO cl_document_bcs.

" Create email
lo_send_request = cl_bcs=>create_persistent( ).

" Create document (email body)
lo_document = cl_document_bcs=>create_document(
    i_type    = 'RAW'
    i_text    = VALUE bcsy_text( ( line = 'Please find the report attached.' ) )
    i_subject = 'Employee Report'
).

" Attach Excel file
lo_document->add_attachment(
    i_attachment_type    = 'XLS'
    i_attachment_subject = 'Employees.xlsx'
    i_att_content_hex    = cl_bcs_convert=>xstring_to_solix( lv_xlsx )
).

lo_send_request->set_document( lo_document ).

" Set sender
DATA lo_sender TYPE REF TO if_sender_bcs.
lo_sender = cl_cam_address_bcs=>create_internet_address( 'sender@company.com' ).
lo_send_request->set_sender( lo_sender ).

" Set recipient
DATA lo_recipient TYPE REF TO if_recipient_bcs.
lo_recipient = cl_cam_address_bcs=>create_internet_address( 'recipient@company.com' ).
lo_send_request->add_recipient( lo_recipient ).

" Send
lo_send_request->send( ).
COMMIT WORK.
```

## Column Customization

To rename columns or format dates, add a field catalog:

```abap
DATA lt_catalog TYPE salv_bs_t_column_settings.
APPEND VALUE #(
    columnname = 'EMP_ID'
    heading    = 'Employee ID'
) TO lt_catalog.

cl_salv_bs_lex=>export_from_result_data_table(
    EXPORTING
        iv_field_catalog = lt_catalog
        ...
).
```
