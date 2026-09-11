---
title: "CDS: Color Code Line Items with Criticality Indicators"
date: 2026-05-04 08:00:00 +0530
categories: [CDS]
tags: [cds, fiori, annotations, criticality, status, line-items, ui]
---

Highlight rows in the Fiori List Report with color-coded status indicators using `@UI.lineItem` criticality annotations — no custom controller needed.

## Step 1: Derive a Criticality Code in CDS

```abap
define view entity ZR_Travel
  as select from ztravel_table
{
  key travel_id,
  overall_status,

  case overall_status
    when 'A' then 3   " 3 = Green  (Positive)
    when 'X' then 1   " 1 = Red    (Critical)
    when 'O' then 2   " 2 = Yellow (Warning)
    else           0  " 0 = Grey   (Neutral)
  end as CriticalityCode
}
```

## Criticality Code Reference

| Code | Color | Meaning |
|---|---|---|
| `0` | Grey | Neutral / No status |
| `1` | Red | Critical / Error |
| `2` | Yellow / Orange | Warning |
| `3` | Green | Positive / OK |
| `5` | Purple | New / Information |

## Step 2: Expose in Projection View

```abap
define view entity ZC_Travel
  as projection on ZR_Travel
{
  key travel_id,
  overall_status,
  CriticalityCode
}
```

## Step 3: Apply to Line Items via Metadata Extension

```abap
@UI.lineItem: [
  {
    position:                   10,
    criticality:                'CriticalityCode',
    criticalityRepresentation:  #WITH_ICON
  }
]
TravelId;
```

## criticalityRepresentation Options

| Value | Effect |
|---|---|
| `#WITH_ICON` | Shows colored icon alongside the value |
| `#WITHOUT_ICON` | Colors only the cell background |
| `#WITH_ICON_AND_TEXT` | Icon + colored text |

## Result

Each row in the List Report is highlighted based on `OverallStatus`:
- Accepted (`A`) → green row / icon
- Rejected (`X`) → red row / icon
- Open (`O`) → yellow row / icon
