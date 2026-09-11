---
title: "Multi Input Field in RAP"
date: 2026-09-09 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, multi-input, composition, fiori]
---

Have you ever encountered a scenario in RAP where you need to handle multiple inputs? It's possible to implement a **multi-input field** that allows you to create and delete values in a composition child.

For instance, consider `_EmployeeAddress` as the composition child (CRUD enabled in BDEF). You can directly assign `_EmployeeAddress.Address` to the value parameter of the Line Item, Identification, or Field Group annotation.

```cds
@UI.lineItem: [{
  position: 10,
  value: '_EmployeeAddress.Address'
}]
element;
```

This renders a multi-input field in the Fiori UI where users can add or remove child entries inline.

**Further Reading:**

- [Multi Input Field — Fiori Feature Showcase](https://github.com/SAP-samples/abap-platform-fiori-feature-showcase/blob/main/06_object_page_content.md#multi-input-field)
- [Multi Input Field — UI5 Documentation](https://sapui5.hana.ondemand.com/sdk/#/topic/04ff5b1a81344a8e8169ea99630ff4e5)

![Multi Input Field in RAP](/assets/images/rap_multi_input_field.jpg)
