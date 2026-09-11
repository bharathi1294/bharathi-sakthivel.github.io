---
title: "Selection Limits in Fiori List Report (RAP)"
date: 2026-08-03 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, fiori, ui5, list-report, table]
---

Recently, I got a question about executing a RAP action on a large set of filtered records (50+). The challenge was that in the List Report, it's not possible to select more than 30 items at once.

By default, the List Report uses a Responsive Table, which initially loads only 30 records. So even if you use Select All, it applies only to those first 30 entries. Trying to override this behavior would require additional UI logic to handle mass actions, which can be time consuming.

As per UI5 guidelines, Responsive Tables are best suited when the total number of items is below 200.

An alternative approach is to switch the table type:

- If you change the table type to a **Grid Table**, it loads up to 100 records initially, allowing you to select up to 200 records at a time by default.

- If you need to handle more than 200 records, you can use the **Table Building Block**, where you can configure and increase the selection limit. You can also enable the **Select All** option, which overrides the selection limit — so you can select all records.

These options are not supported with the Responsive Table.

> **Note:** Before deciding on a table type, always consider the design guidelines, limitations, and your use case.

📄 [View PDF](/bharathi-sakthivel.github.io/assets/pdf/fiori_selection_limit.pdf)

<iframe src="/bharathi-sakthivel.github.io/assets/pdf/fiori_selection_limit.pdf" width="100%" height="600px" style="border:1px solid #ccc;"></iframe>

**Further Reading:**

- [Table / Table Building Block in Fiori Elements](https://ui5.sap.com/#/topic/c0f6592a592e47f9bb6d09900de47412)
- [Tables: Which One Should I Choose?](https://ui5.sap.com/#/topic/148892ff9aea4a18b912829791e38f3e)
- [Table / Table Type Guidelines](https://www.sap.com/design-system/fiori-design-web/v1-71/foundations/best-practices/ui-elements/tables/table-overview)
- [Setup the Fiori Tools in VS Code / BAS](https://developers.sap.com/tutorials/fiori-tools-generate-project..html)
