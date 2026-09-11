---
title: "Enhancements in ABAP SQL Functions"
date: 2026-08-17 08:00:00 +0530
categories: [ABAP SQL]
tags: [abap, sql, string-agg, ltrim, rtrim, s4hana]
---

**STRING_AGG:** The separator can now be passed as a host variable instead of a constant. If you're not familiar with this function, check out [this earlier post]({% post_url 2026-04-06-abap-sql-string-agg %}).

**LTRIM / RTRIM:** The trim character can now come from a host variable or SQL expression (not limited to a single character).

Example: You can join values with a dynamic separator or remove leading/trailing characters by passing them as variables.

```abap
DATA lv_separator TYPE string VALUE ', '.
DATA lv_trim_char TYPE string VALUE '0'.

SELECT STRING_AGG( field, @lv_separator )
       LTRIM( another_field, @lv_trim_char )
  FROM my_table
  INTO ...
```

> **Note:** Available from **S/4HANA 2025+** (Private / On-Premise) and latest versions in the **Public Cloud**.

![ABAP SQL Function Enhancements](/assets/images/abap_sql_function_enhancements.jpg)
