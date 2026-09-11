---
title: "CDS: Enable Multi-line Long Text with @UI.multiLineText"
date: 2025-11-03 08:00:00 +0530
categories: [CDS]
tags: [cds, fiori, rap, abap, annotation, long-text, ui]
---

Need a multi-line text area in your Fiori app? One annotation is all it takes.

## Annotation

```abap
define view entity ZC_MyEntity
  as projection on ZR_MyEntity
{
  key id,

  @UI.multiLineText: true
  description
}
```

This renders the field as a **text area** instead of a single-line input in the Fiori Object Page.

## Show Long Text in a Separate Tab

If the text is important enough to deserve its own tab, create a dedicated facet:

```abap
@UI.facet: [
  {
    id:       'GeneralInfo',
    type:     #IDENTIFICATION_REFERENCE,
    label:    'General Information',
    position: 10
  },
  {
    id:       'LongText',
    type:     #IDENTIFICATION_REFERENCE,
    label:    'Notes',
    position: 20,
    targetQualifier: 'LongTextGroup'
  }
]

@UI.identification: [{ position: 10, qualifier: 'LongTextGroup' }]
@UI.multiLineText: true
description;
```

## Tips

- Works with both **Fiori Elements** List Report + Object Page and standalone Object Pages
- Combine with `@EndUserText.label` for a clean field label
- For very long text (>1333 chars), consider the virtual element + `SAVE_TEXT` approach instead
