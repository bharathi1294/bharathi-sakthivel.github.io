---
title: "step keyword"
date: 2026-01-12 08:00:00 +0530
categories: [ABAP New Syntax]
tags: [abap, step-keyword, loop]
---

```abap
"𝗦𝗧𝗘𝗣 𝗞𝗲𝘆𝘄𝗼𝗿𝗱 𝗶𝗻 𝗔𝗕𝗔𝗣
"The 𝗦𝗧𝗘𝗣 keyword in ABAP simplifies working with loops when you want to skip odd or even indices. 
"Previously, this was achieved using 𝘀𝘆-𝘁𝗮𝗯𝗶𝘅, but with STEP, you can directly control both the step size and the loop's direction, making the process more straightforward and efficient.
"Here is a small example,

TYPES: tt_numbers TYPE TABLE OF int8 WITH EMPTY KEY.
DATA(lt_numbers) = VALUE tt_numbers( FOR i = 1 THEN i + 1 WHILE i < 11 ( CONV #( i ) ) ).

"Step with Positive number -> Forward
LOOP AT lt_numbers INTO DATA(forward_num) STEP 2.
  WRITE:/ forward_num.
ENDLOOP.
DATA(lt_step_forward) = VALUE tt_numbers( FOR num in lt_numbers STEP 2 ( num ) ).
"Output -> 1,3,5,7,9

"Step with Negative Number -> Backword
LOOP at lt_numbers INTO DATA(backword_num) STEP -2.
  WRITE:/ backword_num.
ENDLOOP.
DATA(lt_step_backward) = VALUE tt_numbers( FOR num in lt_numbers STEP -2 ( num ) ).
"Output -> 10,8,6,4,2

"Step with From and To
LOOP AT lt_numbers INTO DATA(from_to_index) FROM 6 STEP -1.
  WRITE:/ from_to_index.
ENDLOOP.
DATA(lt_step_from_to) = VALUE tt_numbers( FOR num in lt_numbers FROM 6 STEP -1 ( num ) ).
"Outpu -> 6,5,4,3,2,1

"Step can be used in
"LINES OF jtab [FROM idx1] [TO idx2] [STEP n] [USING KEY keyname]
```
