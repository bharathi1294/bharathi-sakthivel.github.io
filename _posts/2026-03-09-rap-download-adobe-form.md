---
title: "RAP: Download Adobe Forms as PDF — Without GUI_DOWNLOAD"
date: 2026-03-09 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, adobe-forms, pdf, download, stream, odata-v4]
---

`GUI_DOWNLOAD` doesn't work in RAP / BTP ABAP Environment. The correct approach is to use **stream fields** (`@Semantics.largeObject`) populated via an action or virtual element.

## Architecture

```
Action / Virtual Element
  → generate PDF (Adobe Forms function module)
  → store in stream field (MIME type + filename + content)
  → OData V4 stream endpoint
  → browser downloads the PDF
```

## Step 1: Database Table Fields

```abap
" Add to your DB table:
attachment      : rawstring;   " PDF binary content
mime_type       : mimeotype;   " 'application/pdf'
filename        : string;      " 'CustomerReport.pdf'
```

## Step 2: CDS Root Entity — Stream Annotations

```abap
@Semantics.largeObject: {
  mimeType:   'MimeType',
  fileName:   'FileName',
  contentDispositionPreference: #ATTACHMENT
}
Attachment;

MimeType;
FileName;
```

## Step 3: Behavior Definition — Action

```abap
define behavior for ZR_MyEntity
{
  action downloadForm result [1] $self;
}
```

> Set stream fields as **read-only** — they are populated by your logic, not by the user.

## Step 4: Action Implementation

```abap
METHOD downloadForm.
  LOOP AT keys ASSIGNING FIELD-SYMBOL(<key>).
    " Generate PDF via Adobe Forms
    CALL FUNCTION 'FP_FUNCTION_MODULE_NAME'
      EXPORTING form_name = 'ZMY_ADOBE_FORM'
      IMPORTING fm_name   = DATA(lv_fm_name).

    CALL FUNCTION lv_fm_name
      EXPORTING customer_id = <key>-CustomerID
      IMPORTING fp_docparams = DATA(ls_params)
                fp_outputparams = DATA(ls_output).

    " Store in stream field
    MODIFY ENTITIES OF ZR_MyEntity IN LOCAL MODE
      ENTITY MyEntity
      UPDATE FIELDS ( Attachment MimeType FileName )
      WITH VALUE #( (
          %key      = <key>-%key
          Attachment = ls_output-pdf
          MimeType   = 'application/pdf'
          FileName   = |CustomerReport_{ <key>-CustomerID }.pdf|
      ) ).
  ENDLOOP.
ENDMETHOD.
```

## Alternative: Virtual Element

If you want the download without an explicit button click, use a virtual element with a calculation class implementing `if_sadl_exit_calc_element_read`. Note: this may call the Adobe form generation multiple times (once per GET request), so an action is usually preferred.
