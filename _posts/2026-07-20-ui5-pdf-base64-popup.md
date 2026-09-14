---
title: "UI5: Display a Base64 PDF in a Popup Using PDFViewer"
date: 2026-07-20 08:00:00 +0530
categories: [UI5]
tags: [ui5, javascript, pdf, base64, popup, pdfviewer, blob]
description: "Learn how to display a Base64 PDF string in a SAP UI5 popup using PDFViewer and the Blob API without requiring server access."
---

Display a PDF received as a Base64 string in a SAP UI5 popup — no server download, no file system access needed.

## The Function

```javascript
showPdfPopup: function(sBase64Pdf) {
    // Decode Base64 to binary string
    var sDecoded = atob(sBase64Pdf);

    // Convert to byte array
    var aBytes = new Uint8Array(sDecoded.length);
    for (var i = 0; i < sDecoded.length; i++) {
        aBytes[i] = sDecoded.charCodeAt(i);
    }

    // Create a Blob from the byte array
    var oBlob = new Blob([aBytes.buffer], { type: 'application/pdf' });

    // Generate an object URL for the blob
    var sBlobUrl = URL.createObjectURL(oBlob);

    // Whitelist the blob URL scheme
    jQuery.sap.addUrlWhitelist('blob');

    // Open in PDFViewer popup
    this._oPDFViewer = new sap.m.PDFViewer({
        title:              'Document Preview',
        source:             sBlobUrl,
        width:              '70%',
        showDownloadButton: false
    });

    this._oPDFViewer.open();
}
```

## How to Call It

```javascript
// From a button press or action handler:
this.showPdfPopup(sYourBase64String);
```

## Typical Integration with OData

```javascript
// Get PDF from OData action response:
var oModel = this.getView().getModel();

oModel.callFunction('/GeneratePDF', {
    method: 'POST',
    urlParameters: { DocumentId: sDocId },
    success: function(oData) {
        this.showPdfPopup(oData.PdfContent);
    }.bind(this)
});
```

## Notes

| Point | Detail |
|---|---|
| `atob()` | Decodes Base64 → binary string |
| `Uint8Array` | Converts binary string → byte array |
| `Blob` | Browser in-memory binary object |
| `URL.createObjectURL()` | Generates a temporary `blob:` URL |
| `addUrlWhitelist('blob')` | Required for UI5 to allow blob URLs |
| `showDownloadButton: false` | Hides the download button (set `true` to allow) |

For UI5 versions 1.108+, use `sap.m.PDFViewer` directly from the `sap.m` library — it's fully released and doesn't need `jQuery.sap`.
