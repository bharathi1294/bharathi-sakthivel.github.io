---
title: "RAP Side Effects — Triggering Field Permissions"
date: 2026-08-10 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, side-effects, feature-control, permissions]
description: "Learn how to use RAP side effects with the permissions syntax to trigger field permission updates (readonly/mandatory) when another field changes."
---

Imagine a scenario where the behavior of a field (like readonly, mandatory, etc.) needs to change based on another field's value. For example, if the `OverallStatus` is Rejected, the `Description` field may need to be set to readonly (using feature control).

So usually, we define side effects like this:

```abap
field ( features : instance ) Description;
side effects { field OverallStatus affects field Description; }
```

But the catch is — this does **not** trigger the feature instance. It only refreshes the field value, not its behavior.

To trigger the feature instance, define it like this:

```abap
side effects { field OverallStatus affects permissions ( field Description ); }
```

Here, it refreshes the field permissions, which triggers the feature instance and updates the behavior (readonly, mandatory, etc.).

📄 [View PDF](/bharathi-sakthivel.github.io/assets/pdf/rap_side_effects.pdf)

<iframe src="/bharathi-sakthivel.github.io/assets/pdf/rap_side_effects.pdf" width="100%" height="600px" style="border:1px solid #ccc;"></iframe>
