---
title: "RAP - with full data | Get Complete Instance Data in save_modified"
date: 2026-10-08 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, save_modified]
description: "Learn how to use with full data in ABAP RAP to receive complete instance data in save_modified, avoiding extra READ operations."
---

Hello Everyone! 👋

If you have worked with **Managed with Additional Save** or **Managed with Unmanaged Save** in RAP, you might have noticed that in the `save_modified` method, we normally receive only the **key fields and the fields that were changed**.

But what if we need the **complete data of the modified instance** for further processing?

This is where **`with full data`** comes into the picture.

By default, only the key fields and changed fields are passed to the `save_modified` method. By using the addition **`with full data`**, RAP can pass the complete instance data to the saver class, avoiding the need for an additional `READ` operation.

For example:

```abap
managed with additional save with full data
```

or

```abap
managed with unmanaged save with full data
```

### Managed with Additional Save

`with additional save` can be used to perform additional operations after the standard save sequence of a managed RAP BO.

It requires the implementation of the `save_modified` method in the RAP saver class.

### Managed with Unmanaged Save

Similarly, `with unmanaged save` allows you to replace the default save sequence and implement your own save logic through the saver class.

### Why `with full data`?

In scenarios where all fields, not only changed fields, are required for further processing, the addition `with full data` can be used. This spares the RAP BO consumer an additional `READ` operation.

So, depending on your scenario, you can use:

`managed with additional save with full data`

or

`managed with unmanaged save with full data`

A small addition, but quite useful when implementing custom save logic in RAP.

![Consider a scenario where only the Employee Name is updated.](/assets/images/with_Fulldata.png)

> **Note:** The fields of the component group `%control` are not affected by this. Still, only the changed fields of `%control` are flagged.
