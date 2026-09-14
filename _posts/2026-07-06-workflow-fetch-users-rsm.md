---
title: "SAP: Fetch Users from Responsibility Management (RSM)"
date: 2026-07-06 08:00:00 +0530
categories: [Workflow]
tags: [abap, workflow, responsibility-management, rsm, approval, agents, s4hana]
description: "Learn how to fetch responsible agents dynamically from SAP Responsibility Management using CL_RSM_DETERMINE_RESPONSIBLE."
---

In SAP S/4HANA, **Responsibility Management (RSM)** defines who is responsible for what based on attributes like Sales Org, Bill-to-Party, etc. Use `CL_RSM_DETERMINE_RESPONSIBLE` to fetch agents dynamically.

## Class Method

```abap
CLASS lcl_rsm DEFINITION CREATE PUBLIC.
  PUBLIC SECTION.
    CLASS-METHODS get_users_from_rsm
      IMPORTING
        iv_team_category TYPE rsm_de_team_category
        iv_function      TYPE rsm_de_function
        it_attributes    TYPE atmtg_name_value_pair_tab
      EXPORTING
        et_agents        TYPE rsm_tt_user.
ENDCLASS.

CLASS lcl_rsm IMPLEMENTATION.
  METHOD get_users_from_rsm.
    DATA(lo_resp) = NEW cl_rsm_determine_responsible( ).
    DATA(lt_functions) = VALUE rsm_tt_functions( ( iv_function ) ).

    DATA(lt_parameters) = VALUE rsmtg_name_value_pair_tab(
        ( name = 'RESPYMGMTTEAMCATEGORY'
          value = REF #( iv_team_category ) )
        ( name = 'RESPYMGMTFUNCTION'
          value = REF #( lt_functions ) )
        ( name = 'RESPYMGMTATTRIBUTENAMEVALPAIR'
          value = REF #( it_attributes ) )
    ).

    TRY.
        lo_resp->if_rsm_determine_responsible~determine_responsible(
            EXPORTING
                iv_rule_id                    = 'RESPY_MGMT_TEAMS'
                it_parameters_name_value_pair = lt_parameters
            IMPORTING
                et_agents                     = et_agents
        ).
      CATCH cx_rsm_runtime_error cx_rsm_no_agent_determined.
    ENDTRY.
  ENDMETHOD.
ENDCLASS.
```

## Usage Example

```abap
DATA(lt_bill_to_party) = VALUE string_table(
    ( `0000000001` )
    ( `0000000002` )
).
DATA(lv_sales_org) = `0001`.

lcl_rsm=>get_users_from_rsm(
    EXPORTING
        iv_team_category = 'SALES'
        iv_function      = 'CMR1LVLA'   " Level 1 Approver
        it_attributes    = VALUE #(
            ( name  = 'BILL_TO_PARTY'
              kind  = 'T'               " T = Table (multi-value)
              value = REF #( lt_bill_to_party ) )
            ( name  = 'SALES_ORG'
              kind  = 'E'               " E = Single Value
              value = REF #( lv_sales_org ) )
        )
    IMPORTING
        et_agents = DATA(lt_users)
).
```

## Common Function Codes

| Code | Meaning |
|---|---|
| `CMR1LVLA` | Level 1 Approver |
| `CMR2LVLA` | Level 2 Approver |
| `CMR3LVLA` | Level 3 Approver |

> Technical names of Responsibility Attributes are in table `P_RESPYMGMTATTRIBUTEDETAILS`.
