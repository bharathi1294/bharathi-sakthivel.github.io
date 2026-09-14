---
title: "Why Do We Need Projection in ABAP RAP?"
date: 2026-09-14 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, projection, bdef, fiori, odata]
description: "Learn why ABAP RAP projection BDEFs are essential for controlling field behavior, actions, and capabilities for different consumers like UI and API."
---

Recently, I was talking with a fresher who had just started working on an ABAP RAP object. When I looked at his project structure, I noticed this flow:

Root Entity (With some annotations) → BDEF → Service Definition → Service Binding

It works, but there was no Projection View or Projection BDEF. This is something I see quite often. Many developers are not sure why projections matter, and some assume they are only needed for RAP app development. But projections are useful for much more than that.

**Example 1 — UI and API from the same business object:**

Imagine the same RAP business object needs to serve both a UI application and an API. With projections, you can control what each consumer can access:

1. An action available only in the UI
2. An action available only through the API
3. Field behavior per consumer — mandatory, read-only, hidden, etc.
4. Draft enabled for the UI, but not for the API
5. Different fields or capabilities exposed for each consumer

**Example 2 — Two apps, one business object:**

Say you need to build two apps:
- Employee — to create a leave request
- Manager — to approve or reject a leave request

Instead of building two separate business objects, keep the common logic in one BDEF and create separate Projection BDEFs for each app. Each projection exposes only what that application needs.

There are many more use cases like this. Projection is not just another RAP artifact. It helps you control how the same business object is exposed and consumed by different applications and APIs.

🔗 [Business Object Projection — SAP Help](https://help.sap.com/docs/abap-cloud/abap-rap/business-object-projection-6e7a10d30b74412a9482a80b0b88e005-194)

![Why Do We Need Projection in ABAP RAP?](/assets/images/proj_bdef.png)
