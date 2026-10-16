---
title: "RAP - File Upload as an Action Parameter (No UI Extensions)"
date: 2026-10-16 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, fiori-elements, file-upload]
description: "Learn how to upload a file through a RAP action parameter without UI extensions."
---

Hello Everyone! 👋

If you've worked with file uploads in RAP before, you know they were typically handled by adding stream fields on the Object Page. But what about uploading a file via an action? That used to require UI extensions — not anymore!

RAP now supports file uploads directly as action parameters, eliminating the need for any UI extensions.

To enable this, we need to create two abstract entities along with their respective Abstract BDEFs.

---

### 1. Abstract Entity for File Upload Action

The first abstract entity acts as the action parameter. It holds a dummy field and an association to the stream properties entity, which carries the actual file data.

```cds
@EndUserText.label: 'File Upload'
define root abstract entity ZDFileUpload
{
   DummyField: abap_boolean;
   _StreamProperties : association [1] to ZDFileStreamProperty on 1 = 1;
}
```

Its Abstract BDEF exposes the association with hierarchy:

```rap
abstract;
strict ( 2 );
with hierarchy;
define behavior for ZDFileUpload alias FileUpload
{
   association _StreamProperties with hierarchy;
}
```

---

### 2. Abstract Entity for Stream Properties

The second abstract entity defines the stream fields — the file content, MIME type, and filename — annotated with `@Semantics.largeObject` to enable the file upload behavior.

```cds
@EndUserText.label: 'Stream Property'
define root abstract entity ZDFileStreamProperty
{
  @Semantics.largeObject.mimeType: 'mimetype'
  @Semantics.largeObject.fileName: 'filename'
  @Semantics.largeObject.contentDispositionPreference: #INLINE
  @EndUserText.label: 'File'
  streamproperty : abap.rawstring(0);

  @UI.hidden     : true
  mimetype       : abap.char(133);

  @UI.hidden     : true
  filename       : abap.char(133);
}
```

Its Abstract BDEF:

```rap
abstract;
strict ( 2 );
with hierarchy;
define behavior for ZDFileStreamProperty alias StreamProperties
{

}
```

---

### 3. Using the Action in BDEF

Once the abstract entities are set up, the action can be declared in any BDEF as either a bound or unbound (static) action:

```rap
static action fileUpload deep parameter ZDFileUpload; 
action fileUpload deep parameter ZDFileUpload;
```

The `deep parameter` keyword is key here it allows the action parameter to carry the associated stream properties along with it.

Once this setup is done, it can be **reused in any BDEF** simply by referencing the abstract entity name.

> **Note:** It is mandatory to create **both abstract entities** (`ZDFileUpload` and `ZDFileStreamProperty`) along with their respective **Abstract BDEFs**.

---

📌 **Availability:** SAP S/4HANA Cloud Public Edition, SAP BTP ABAP Environment, and SAP S/4HANA Cloud Private Edition from 2025.

![File Upload as an Action Parameter](/assets/images/FileUploadInActionParam.png)

📖 **Reference:** [File Upload as an Action Parameter](https://ui5.sap.com/#/topic/b236d32d48b74304887b3dd5163548c1)
