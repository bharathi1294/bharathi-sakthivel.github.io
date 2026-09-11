---
title: "RAP: Display a Message Strip by Default on the Object Page"
date: 2026-03-23 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, fiori, message-strip, instance-features, object-page]
---

Show contextual message strips automatically when a user opens an Object Page — no button click required. Done via `get_instance_features`.

## Scenario

Show different messages based on travel status:
- **Information** — travel is completed (end date passed)
- **Success** — travel is accepted
- **Error** — travel is rejected

## Step 1: Metadata Extension — Define Informational Actions

```abap
@UI.identification: [
  { type: #FOR_ACTION, dataAction: 'AcceptTravel', label: 'Accept', position: 10 },
  { type: #FOR_ACTION, dataAction: 'RejectTravel', label: 'Reject', position: 20 }
]
TravelId;
```

## Step 2: Behavior Definition

```abap
define behavior for ZR_Travel
{
  instance features;

  action AcceptTravel result [1] $self;
  action RejectTravel result [1] $self;
}
```

## Step 3: get_instance_features Implementation

```abap
METHOD get_instance_features.
  READ ENTITIES OF ZR_Travel IN LOCAL MODE
    ENTITY Travel
    FIELDS ( OverallStatus EndDate )
    WITH CORRESPONDING #( keys )
    RESULT DATA(lt_travel).

  result = VALUE #(
    FOR travel IN lt_travel
    LET is_completed = xsdbool( travel-EndDate < sy-datum )
        is_accepted  = xsdbool( travel-OverallStatus = 'A' )
        is_rejected  = xsdbool( travel-OverallStatus = 'X' )
    IN
    ( %key                          = travel-%key
      %features-%action-AcceptTravel = COND #(
          WHEN is_accepted = abap_true THEN if_abap_behv=>fc-o-disabled
          ELSE if_abap_behv=>fc-o-enabled )
      %features-%action-RejectTravel = COND #(
          WHEN is_rejected = abap_true THEN if_abap_behv=>fc-o-disabled
          ELSE if_abap_behv=>fc-o-enabled )
      %op-%dummy = COND #(
          WHEN is_completed = abap_true
          THEN VALUE #(
              %msg = new_message(
                  id       = 'ZTRAVEL_MSGS'
                  number   = '001'
                  severity = if_abap_behv_message=>severity-information
                  v1       = travel-TravelId ) )
          WHEN is_accepted = abap_true
          THEN VALUE #(
              %msg = new_message(
                  id       = 'ZTRAVEL_MSGS'
                  number   = '002'
                  severity = if_abap_behv_message=>severity-success ) )
          WHEN is_rejected = abap_true
          THEN VALUE #(
              %msg = new_message(
                  id       = 'ZTRAVEL_MSGS'
                  number   = '003'
                  severity = if_abap_behv_message=>severity-error ) ) )
    )
  ).
ENDMETHOD.
```

## Result

When the user opens a travel record:
- Completed → blue information strip
- Accepted → green success strip
- Rejected → red error strip

No button click needed — the strip appears automatically on page load.
