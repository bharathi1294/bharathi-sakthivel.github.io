---
title: "Real-Time UI Updates in RAP Using Event-Driven Side Effects"
date: 2026-09-21 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, side-effects, events, real-time, eml]
---

Have you ever had a scenario where something changes in the backend and you want those updates to automatically reflect in the UI — without manually refreshing the page?

This is possible using **Event-Driven Side Effects** in RAP. Useful when:
- A background job updates data and users should see the change in real time
- Multiple users are working on the same data simultaneously

## Step 1 — BDEF Configuration

Define an event and specify which fields it affects as side effects:

```abap
define behavior for ZI_Travel alias Travel
...
{
  event StatusChanged;

  side effects
  {
    event StatusChanged affects field OverallStatus, field Description;
  }
  ...
}
```

## Step 2 — Projection BDEF

Expose the event in the projection layer:

```abap
define behavior for ZC_Travel alias Travel
...
{
  use event StatusChanged;
  ...
}
```

## Step 3 — Raise the Event in save_modified

Raise the event in the behavior saver class, triggered only when the relevant field changes:

```abap
METHOD save_modified.
  LOOP AT update-travel ASSIGNING FIELD-SYMBOL(<travel>).
    IF <travel>-%control-OverallStatus = if_abap_behv=>mk-on.
      RAISE ENTITY EVENT ZI_Travel~StatusChanged
        FROM VALUE #( ( %key = <travel>-%key ) ).
    ENDIF.
  ENDLOOP.
ENDMETHOD.
```

When the status is updated via EML from a background job, the event is raised automatically and the UI refreshes the affected fields without a manual page reload.

> **Availability:** SAP BTP ABAP Environment and SAP S/4HANA Public Cloud, and from release 2025 also in on-premise and private cloud systems.
