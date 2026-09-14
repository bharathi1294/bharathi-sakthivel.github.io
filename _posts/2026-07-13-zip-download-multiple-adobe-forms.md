---
title: "ABAP: Generate Multiple Adobe Forms and Download as a ZIP File"
date: 2026-07-13 08:00:00 +0530
categories: [ZIP]
tags: [abap, zip, adobe-forms, pdf, download, email, bcs, cl-abap-zip]
description: "Learn how to generate multiple Adobe Form PDFs in ABAP and bundle them into a ZIP file for user download or email attachment."
---

Generate a PDF for each record, bundle them into a ZIP, and let the user download it — or send it as an email attachment.

## Architecture

```
Customer IDs → get_form() per customer → build_zip() → download_zip() or send_email()
```

## Step 1: Generate a Single PDF

```abap
METHOD get_form.
  " Fetch data
  SELECT SINGLE * FROM scustom INTO @DATA(ls_customer)
    WHERE id = @iv_customer_id.

  " Get Adobe form function module name
  CALL FUNCTION 'FP_FUNCTION_MODULE_NAME'
    EXPORTING  i_name     = 'ZMY_ADOBE_FORM'
    IMPORTING  e_funcname = DATA(lv_fm_name).

  " Generate PDF
  CALL FUNCTION lv_fm_name
    EXPORTING fp_docparams    = VALUE sfpdocparams( device = 'PDF' )
              customer        = ls_customer
    IMPORTING fp_outputparams = DATA(ls_output).

  rv_pdf = ls_output-pdf.
ENDMETHOD.
```

## Step 2: Build the ZIP

```abap
METHOD build_zip.
  DATA(lo_zip) = NEW cl_abap_zip( ).

  LOOP AT it_customer_ids ASSIGNING FIELD-SYMBOL(<id>).
    DATA(lv_pdf) = get_form( <id> ).
    lo_zip->add(
        name    = |{ <id> }.pdf|
        content = lv_pdf
    ).
  ENDLOOP.

  rv_zip = lo_zip->save( ).
ENDMETHOD.
```

## Step 3: Download ZIP to Frontend

```abap
METHOD download_zip.
  DATA lt_binary TYPE solix_tab.
  lt_binary = cl_bcs_convert=>xstring_to_solix( iv_zip ).

  cl_gui_frontend_services=>file_save_dialog(
    IMPORTING filename = DATA(lv_path)
  ).

  cl_gui_frontend_services=>gui_download(
      EXPORTING
          filename     = lv_path
          filetype     = 'BIN'
          bin_filesize = xstrlen( iv_zip )
      CHANGING
          data_tab     = lt_binary
  ).
ENDMETHOD.
```

## Step 4: Send ZIP via Email

```abap
METHOD send_email.
  DATA(lo_request)   = cl_bcs=>create_persistent( ).
  DATA(lo_document)  = cl_document_bcs=>create_document(
      i_type    = 'RAW'
      i_text    = VALUE bcsy_text( ( line = 'Please find the forms attached.' ) )
      i_subject = 'Customer Forms'
  ).

  lo_document->add_attachment(
      i_attachment_type    = 'ZIP'
      i_attachment_subject = 'CustomerForms.zip'
      i_att_content_hex    = cl_bcs_convert=>xstring_to_solix( iv_zip )
  ).

  lo_request->set_document( lo_document ).
  lo_request->add_recipient(
      cl_cam_address_bcs=>create_internet_address( 'recipient@company.com' )
  ).
  lo_request->send( ).
  COMMIT WORK.
ENDMETHOD.
```

## Putting It Together

```abap
START-OF-SELECTION.
  DATA(lo_handler) = NEW lcl_sb_zip( ).
  DATA(lv_zip) = lo_handler->build_zip(
      it_customer_ids = VALUE #( ( `C001` ) ( `C002` ) )
  ).
  lo_handler->download_zip( lv_zip ).
  lo_handler->send_email( lv_zip ).
```
