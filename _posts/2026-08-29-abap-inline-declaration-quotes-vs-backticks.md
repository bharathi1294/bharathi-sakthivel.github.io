---
title: "ABAP Inline Declaration — Single Quotes vs Backticks"
date: 2026-08-29 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, inline-declaration, string, new-syntax]
---

When using inline declarations with literals in ABAP, there's a key difference in how values behave depending on whether you use **single quotes** (`' '`) or **backticks** (`` ` ` ``).

```abap
DATA(lv_text_1) = 'A'.    " typed as C(1) — fixed-length char
DATA(lv_text_2) = `A`.    " typed as STRING

lv_text_1 = 'ABCD'.       " only 'A' is stored (truncated to length 1)
lv_text_2 = `ABCD`.       " 'ABCD' is stored in full
```

- `lv_text_1` is typed as `C(1)` because the initial value `'A'` is in single quotes. So even when you assign `'ABCD'`, it only keeps the first character. If the initial value length is 2, the variable length also remains 2.
- `lv_text_2` is typed as `STRING` because of the backticks, so it can hold the full value.

> **Tip:** Use backticks when you need to store a full string. Single quotes will create a fixed-length character field based on the length of the initial value.

![Single Quotes vs Backticks in ABAP Inline Declaration](/assets/images/abap_inline_quotes_vs_backticks.jpg)
