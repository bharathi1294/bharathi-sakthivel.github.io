---
title: "Finding Cloud-Compatible Alternatives in ABAP Cloud"
date: 2026-09-01 08:00:00 +0530
categories: [ABAP]
tags: [abap, cloud, steampunk, btp, released-objects]
description: "Learn how to find cloud-compatible alternatives for classic ABAP objects in ABAP Cloud using ADT, the API Business Hub, and released object queries."
---

If you're working in the ABAP environment on BTP (Steampunk) or using the "ABAP for Cloud Development" language version in the Private Cloud, you may have noticed that you can't directly use some classic objects like tables (`VBAK`, `VBAP`, `MARA`), function modules, or classes. These objects aren't released for use in cloud scenarios, so attempting to use them will throw errors.

So, how do you find the cloud-compliant alternatives? There are several ways to identify released (cloud-compatible) objects:

- **ADT (Eclipse)** — Use the "Where-Used" or "Released Objects" filter when searching for object alternatives
- **SAP API Business Hub** — Browse released APIs and CDS views at [api.sap.com](https://api.sap.com)
- **ABAP Repository** — Filter by release state `RELEASED` in object search
- **SAP Help Portal** — Check the ABAP Cloud documentation for released object lists
- **`I_APIAmdpFunctionDblView`** — Query this CDS view in ADT to find released APIs programmatically

```abap
SELECT api_state, object_name, object_type
  FROM i_apiamdpfunctiondblview
  WHERE api_state = 'RELEASED'
  INTO TABLE @DATA(lt_released).
```

> **Tip:** Always check whether a C1-released alternative exists before falling back to classic objects in cloud scenarios.

**Useful Resources:**

- [SAP API Business Hub](https://api.sap.com/)
- [Released ABAP Objects — SAP Help](https://help.sap.com/docs/ABAP_PLATFORM_NEW/c238d694b825421f940829321ffa326a/3f232ac7cecc4d9891ff512462240223.html)
- [ABAP ATC Compatible API Check Rules](https://sap.github.io/abap-atc-cr-cv-s4hc/)
- [LinkedIn Post — Full List of Methods](https://www.linkedin.com/posts/bharathi-sakthivel_abap-abapcloud-abaptips-activity-7334141134639673345-v1gK)

![Finding Cloud-Compatible Alternatives in ABAP](/assets/images/abap_cloud_released_objects.jpg)
