---
title: "SAP Workflow: Get Decision Note (Approver Comments) from a Work Item"
date: 2026-06-29 08:00:00 +0530
categories: [Workflow]
tags: [abap, workflow, work-item, decision-note, comments, approval]
---

Need to retrieve the comments an approver entered when approving or rejecting a workflow task? Two options depending on your system version.

## Option 1: CDS View (Recommended for BTP / Cloud)

```abap
SELECT FROM i_workflowtaskresultcomment
  FIELDS work_item_id,
         result_text,
         comment_text,
         created_by,
         created_at
  WHERE work_item_id = @lv_work_item_id
  INTO TABLE @DATA(lt_comments).
```

`I_WorkflowTaskResultComment` is a released CDS view available in BTP ABAP Environment and S/4HANA Public Cloud.

## Option 2: Class (On-Premise)

```abap
DATA(lo_query) = NEW cl_wapi_decision_comment_query( ).
lo_query->set_workitem_id( lv_work_item_id ).

DATA(lt_comments) = lo_query->execute( ).

LOOP AT lt_comments INTO DATA(ls_comment).
  WRITE: / ls_comment-comment_text,
           ls_comment-decision_key,
           ls_comment-created_by.
ENDLOOP.
```

## Retrieve Old / Completed Work Item Notes

If the comments aren't showing for completed/old work items, run this report once to backfill:

```
Transaction SE38 → Program: RSWW_SET_WORKITEM_COMMENTS → Execute
```

This populates the comment store for historical work items.

## Practical Use Cases

| Scenario | Use |
|---|---|
| Show approver reason in Fiori Object Page | Read via CDS, display in annotation |
| Audit trail report | Loop through `I_WorkflowTaskResultComment` |
| Rejection email with reason | Read comment → add to email body |
| MDG review comments | Available via same CDS/class |
