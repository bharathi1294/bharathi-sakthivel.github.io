---
title: "Accessing Structure Components Dynamically in ABAP"
date: 2027-01-04 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, dynamic-programming, field-symbols, structure]
---

Have you ever encountered a scenario where you needed to retrieve a specific component value from a dynamic structure? Traditionally, this is achieved using:

```abap
ASSIGN COMPONENT 'COMP_NAME' OF STRUCTURE <fs> TO FIELD-SYMBOL(<fs_value>).
```

However, the same can be achieved with the newer syntax:

```abap
ASSIGN <fs>-(comp_name) TO FIELD-SYMBOL(<fs_value>).

" Or with a work area
ASSIGN wa-(comp_name) TO FIELD-SYMBOL(<fs_value>).
```

Where `comp_name` is a variable containing the component name as a string — resolved dynamically at runtime.

For more details on dynamic programming in ABAP, refer to the [ABAP Dynamic Programming Cheat Sheet](https://github.com/SAP-samples/abap-cheat-sheets/blob/main/06_Dynamic_Programming.md#dynamic-programming).

![Accessing Structure Components Dynamically](/assets/images/abap_dynamic_structure_component.jpg)
