---
title: "The Usage of cl_abap_behv_aux in RAP"
date: 2025-10-13 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, behavior, projection, cl-abap-behv-aux]
---

I had a BDEF named `R_SomeName`, and on top of that, I created two projections: one for the UI (`C_SomeName`) and another for the API (`A_SomeName`).

`R_SomeName` contains some validations and determinations, and I only wanted these to execute when the call is coming from the UI, not from the API.

So, how can we distinguish whether the call originates from the UI or the API? This is where the class `cl_abap_behv_aux` comes in handy:

```abap
cl_abap_behv_aux=>get_current_context(
  IMPORTING from_projection = DATA(lv_proj) ).
```

The `lv_proj` variable will hold the name of the calling projection — `C_SomeName` if the call is from the UI, or `A_SomeName` if it's from the API. Based on this, you can conditionally execute logic in the handler class of `R_SomeName`.

> **Note:** It's also possible to create separate handler classes for each projection view. The `cl_abap_behv_aux` class provides several useful methods that can be extremely helpful in specific scenarios. For more details, refer to the [ABAP Released Classes Cheat Sheet](https://github.com/SAP-samples/abap-cheat-sheets/blob/main/22_Released_ABAP_Classes.md#rap).

![cl_abap_behv_aux](/assets/images/cl_abap_behv_aux.png)
