---
title: "RAP Instance Features — Using requested_features for Performance"
date: 2026-09-07 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, instance-features, performance, get-instance-features]
---

When working with RAP applications, you often need to enable or disable actions dynamically based on specific business logic. This logic is typically implemented in the `get_instance_features` method.

By default, this method is triggered multiple times:
- Once when the List Report is loaded
- Again when navigating to the Object Page

Even if an action is defined only for the List Report or only for the Object Page, the instance features logic still gets executed twice when any other action is enabled using instance features. This results in unnecessary processing and redundant calls.

**Recommended Solution:**

Use the importing parameter `requested_features`. This parameter indicates which actions are actually requested in the current UI context.

**How it helps:**
- If an action is defined only on the Object Page, it will not be requested during the List Report call
- By checking `requested_features`, you can execute the enable/disable logic only when the action is actually requested

**Best Practice:**

Before running your instance feature logic, always verify whether the action is present in `requested_features`. If it's not requested, skip the logic — this avoids redundant calls and improves application performance.

```abap
METHOD get_instance_features.
  LOOP AT keys ASSIGNING FIELD-SYMBOL(<key>).

    IF requested_features-%action-MyAction = if_abap_behv=>mk-on.
      " execute logic only when MyAction is requested
      result = VALUE #( BASE result
        ( %key           = <key>
          %action-MyAction = COND #( WHEN <condition>
                                     THEN if_abap_behv=>fc-o-enabled
                                     ELSE if_abap_behv=>fc-o-disabled ) ) ).
    ENDIF.

  ENDLOOP.
ENDMETHOD.
```

![RAP get_instance_features with requested_features](/assets/images/rap_instance_features_requested.jpg)
