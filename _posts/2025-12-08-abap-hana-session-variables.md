---
title: "Session Variables in ABAP, CDS, and AMDP"
date: 2025-12-08 08:00:00 +0530
categories: [ABAP on HANA]
tags: [abap, hana, amdp, cds, session-variables]
---

Session variables are frequently used in ABAP (e.g. `sy-uname`, `sy-datum`). In AMDP and CDS, equivalent built-in functions exist. Here's a cross-reference.

## Common Session Variables

| ABAP | CDS | AMDP (SQLScript) |
|---|---|---|
| `sy-uname` | `$session.user` | `SESSION_USER` |
| `sy-datum` | `$session.system_date` | `CURRENT_DATE` |
| `sy-uzeit` | `$session.system_time` | `CURRENT_TIME` |
| `sy-mandt` | `$session.client` | `SESSION_CONTEXT('CLIENT')` |
| `sy-langu` | `$session.user_logon_language` | `SESSION_CONTEXT('LOCALE_SAP_LANGUAGE')` |

## CDS Usage

```abap
define view entity ZV_AuditFields
  as select from zmy_table
{
  key id,
  $session.user        as CreatedBy,
  $session.system_date as CreatedOn,
  $session.client      as Client
}
```

## AMDP Usage

```abap
METHOD get_current_user
    BY DATABASE FUNCTION FOR HDB
    LANGUAGE SQLSCRIPT
    OPTIONS READ-ONLY.

  RETURN SELECT
    SESSION_USER            AS user_name,
    CURRENT_DATE            AS today,
    CURRENT_TIME            AS now,
    SESSION_CONTEXT('CLIENT') AS mandt
  FROM DUMMY;
ENDMETHOD.
```

## ABAP Comparison

```abap
DATA(lv_user)  = sy-uname.
DATA(lv_date)  = sy-datum.
DATA(lv_mandt) = sy-mandt.
```

The CDS `$session.*` variables are particularly useful in **audit fields** on CDS views — stamping `CreatedBy` and `ChangedBy` without passing them explicitly from ABAP.
