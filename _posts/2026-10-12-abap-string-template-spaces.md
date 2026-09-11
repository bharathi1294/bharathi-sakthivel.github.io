---
title: "String Templates — Avoid Spaces Between Vertical Bars"
date: 2026-10-12 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, string-templates, new-syntax]
---

We often use string templates (`| |`) to add or remove leading zeros and perform various string operations. However, it's important to note that you should **not leave any spaces between the two vertical bars**, as this can sometimes produce unexpected results.

For example, the following snippets can produce different results:

```abap
" Without space — correct
DATA(lv_result1) = |{ lv_value ALPHA = IN }|.

" With space — may produce unexpected results
DATA(lv_result2) = | { lv_value ALPHA = IN } |.
```

In the second example, the extra spaces inside the template are treated as literal characters and become part of the resulting string, leading to values that don't match what you intended.

![String Template Spaces Comparison](/assets/images/abap_string_template_spaces.jpg)
