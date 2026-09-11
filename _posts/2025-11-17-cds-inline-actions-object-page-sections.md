---
title: "CDS: Add Inline Actions Inside Object Page Sections"
date: 2025-11-17 08:00:00 +0530
categories: [CDS]
tags: [cds, fiori, rap, actions, object-page, annotations, inline]
---

Normally, actions appear in the **page header toolbar**. But sometimes an action is only relevant to a specific section. With the `inline: true` property, you can place it directly inside a section.

> Available from **SAP S/4HANA 2023** and latest Public Cloud versions.

## Step 1: Define a Field Group with the Action

```abap
@UI.fieldGroup: [
  {
    qualifier:    'GeneralInfoGroup',
    position:     10,
    type:         #FOR_ACTION,
    dataAction:   'resetData',
    label:        'Reset to Default',
    emphasized:   true,
    inline:       true
  }
]
SomeField;
```

## Step 2: Reference the Field Group in a Facet

```abap
@UI.facet: [
  {
    id:             'GeneralInfo',
    type:           #FIELDGROUP_REFERENCE,
    label:          'General Information',
    position:       10,
    targetQualifier: 'GeneralInfoGroup'
  }
]
```

## Result

The **"Reset to Default"** button appears directly inside the "General Information" section — not in the header toolbar. This keeps the UI contextual and avoids toolbar clutter.

## Comparison

| Header Toolbar Action | Inline Section Action |
|---|---|
| Always visible on the page | Only visible in its section |
| Good for page-level operations | Good for section-specific operations |
| `@UI.identification` / `@UI.lineItem` | `@UI.fieldGroup` with `inline: true` |
