---
title: "Display RAP Actions in the Footer Using Determining Actions"
date: 2026-09-10 08:00:00 +0530
categories: [CDS]
tags: [cds, abap, rap, determining-actions, annotations, fiori]
description: "Learn how to display RAP actions in the Fiori Object Page footer using determining actions and the @UI.identification determining: true annotation."
---

Want to display your RAP actions in the footer? Consider using **determining actions**.

Determining actions allow you to trigger specific actions directly from the object page, typically used for process-related tasks like "Approve" or "Reject." These actions appear as buttons in the **footer** of the object page. This only works with the `@UI.identification` annotation with the keyword `determining: true`.

```cds
@UI.identification: [
  {
    type:        #FOR_ACTION,
    dataAction:  'approveRequest',
    label:       'Approve',
    determining: true,
    criticality: 'ApproveButtonCriticality'
  },
  {
    type:        #FOR_ACTION,
    dataAction:  'rejectRequest',
    label:       'Reject',
    determining: true,
    criticality: 'RejectButtonCriticality'
  }
]
element;
```

> **Note:** In this example, two additional elements (`ApproveButtonCriticality` and `RejectButtonCriticality`) are used to store criticality values for coloring the buttons.

![Determining Actions in the Footer](/assets/images/cds_determining_actions_footer.jpg)
