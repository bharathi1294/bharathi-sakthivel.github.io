---
title: "Inheriting Access Conditions from a CDS View"
date: 2026-08-19 08:00:00 +0530
categories: [CDS]
tags: [cds, abap, access-control, dcl, authorization]
description: "Learn how to use #INHERITED in CDS access control to carry access conditions from a source view to a consuming view without duplication."
---

In CDS, access controls are used to define authority for accessing entries. When consuming these CDS views in other CDS views, we often bypass these checks by adding `#NOT_REQUIRED` or `#NOT_ALLOWED`. However, this can lead to issues by overriding the access control from the source CDS and potentially returning all entries.

Instead of replicating the original access conditions in the consuming CDS, it's preferable to **inherit** them directly.

```cds
@AccessControl.authorizationCheck: #NOT_REQUIRED

define view entity ZC_ConsumingView
  as select from ZI_SourceView
{
  key field1,
      field2
}
```

With `#NOT_REQUIRED`, the access control from `ZI_SourceView` is bypassed. Instead, use `#INHERITED` to carry the access conditions forward:

```cds
@AccessControl.authorizationCheck: #INHERITED

define view entity ZC_ConsumingView
  as select from ZI_SourceView
{
  key field1,
      field2
}
```

This ensures the consuming view respects the same access conditions defined in the source CDS without duplicating them.

For more details, refer to the [SAP Help documentation](https://help.sap.com/doc/abapdocu_latest_index_htm/latest/en-US/abencds_f1_cond_inherit.htm).

![Inheriting Access Conditions from CDS](/assets/images/cds_inherit_access_conditions.jpg)
