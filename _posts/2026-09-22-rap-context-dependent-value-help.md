---
title: "Show Value Help Based on Conditions / Context-Dependent Value Help in RAP"
date: 2026-09-22 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, fiori, fiori-elements, value-help, sapui5, s4hana]
description: "Learn how to implement context-dependent value help in ABAP RAP, where the value help of the Partner field changes dynamically based on the Partner Function selected."
---

Imagine a scenario where you have two fields:

**Partner Function and Partner**

You want to control the value help of the **Partner** field based on the value selected in **Partner Function**.

For example, depending on the Partner Function, you may need to provide a different set of valid Partners or a different value help.

This can be implemented in RAP, where the value help can dynamically change based on the context of another field.

In this post, I'll walk through the implementation step by step and show how we can achieve this using RAP.

It is also possible to achieve the same scenario using **XML annotations**, with comparatively less implementation effort.

---

**Step 1 — Add Boolean Fields in the CDS Root View**

Add two computed boolean fields — `ShowValueHelpOne` and `ShowValueHelpTwo` — based on the value of `PartnerFunction`. These fields will act as switches to enable or disable the respective value help.

```abap
define root view entity ZR_RootEntity
  as select from ZRootTable
{
  key entity_id                                                                                   as EntityId,
      partner_function                                                                            as PartnerFunction,
      partner                                                                                     as Partner,

      @Semantics.booleanIndicator: true
      cast( case when $projection.PartnerFunction = 'VN' then 'X' else ' ' end as abap_boolean ) as ShowValueHelpOne,
      @Semantics.booleanIndicator: true
      cast( case when $projection.PartnerFunction = 'SP' then 'X' else ' ' end as abap_boolean ) as ShowValueHelpTwo,
}
```

---

**Step 2 — Expose the Fields in the Projection (C) CDS View**

Expose `ShowValueHelpOne` and `ShowValueHelpTwo` in the Projection CDS View.

```abap
define root view entity ZC_RootEntity
provider contract transactional_query
as projection on ZR_RootEntity
{
    key EntityId,
    PartnerFunction,
    Partner,
    ...
    ShowValueHelpOne,
    ShowValueHelpTwo,
    ...
}
```

---

**Step 3 — Metadata Extension**

Use `@Consumption.valueHelpDefinition` with the `enabled` property pointing to the boolean fields. When `ShowValueHelpOne` is true, the first value help is active; when `ShowValueHelpTwo` is true, the second is active.

Since `ShowValueHelpOne` and `ShowValueHelpTwo` are helper fields used only to control the value help, hide them using `@UI.hidden: true` so they do not appear in the UI.

```abap
annotate entity ZC_RootEntity with
{
  ...
  @Consumption.valueHelpDefinition:
      [{
         qualifier: 'ValueHelpOne',
         entity: { name: 'ZI_PartnerVH_One', element: 'Partner' },
         enabled: 'ShowValueHelpOne'
        },
        {
         qualifier: 'ValueHelpTwo',
         entity: { name: 'ZI_PartnerVH_Two', element: 'Partner' },
         enabled: 'ShowValueHelpTwo'
       }
      ]
  Partner;
  ...
  @UI.hidden: true
  ShowValueHelpOne;
  @UI.hidden: true
  ShowValueHelpTwo;
}
```

---

**Step 4 — Set Default and Update Value Help via Determinations**

Declare two determinations in the base BDEF — one to set a default `PartnerFunction` on create, and one to recalculate the boolean fields whenever `PartnerFunction` changes. Add `side effects` in the base BDEF and use `use side effects;` in the Projection BDEF to enable the side effect propagation.

**Base BDEF:**
```abap
define behavior for ZR_RootEntity alias RootEntity
...
{
  ...
  determination setDefaultPartnerFunction on modify { create; }
  determination setValueHelpForPartner on modify { field PartnerFunction; }
  side effects { field PartnerFunction affects field ShowValueHelpOne, field ShowValueHelpTwo, field Partner; }
}
```

**Projection BDEF:**
```abap
projection;
strict ( 2 );
use draft;
use side effects;

define behavior for ZC_RootEntity
{
  use create;
  use update;
  use delete;
  ...
}
```

**Implementation:**
```abap
METHOD setValueHelpForPartner.
  CHECK keys IS NOT INITIAL.

  DATA: lt_update TYPE TABLE FOR UPDATE ZR_RootEntity.

  READ ENTITIES OF ZR_RootEntity IN LOCAL MODE
  ENTITY ZR_RootEntity
  ALL FIELDS WITH CORRESPONDING #( keys )
  RESULT DATA(lt_result).

  lt_update = VALUE #( FOR lw IN lt_result (
                        %tky = lw-%tky
                        ShowValueHelpOne = xsdbool( lw-PartnerFunction = 'VN' )
                        ShowValueHelpTwo = xsdbool( lw-PartnerFunction = 'SP' )
                        %control = VALUE #( ShowValueHelpOne = if_abap_behv=>mk-on
                                            ShowValueHelpTwo = if_abap_behv=>mk-on
                                            Partner          = if_abap_behv=>mk-on ) ) ).
  IF lt_update IS INITIAL.
    RETURN.
  ENDIF.

  MODIFY ENTITIES OF ZR_RootEntity IN LOCAL MODE
  ENTITY ZR_RootEntity
  UPDATE FROM lt_update
  REPORTED DATA(ls_reported).

  reported = CORRESPONDING #( DEEP ls_reported ).
ENDMETHOD.

METHOD setDefaultPartnerFunction.
  CHECK keys IS NOT INITIAL.

  DATA: lt_update TYPE TABLE FOR UPDATE ZR_RootEntity.

  lt_update = VALUE #( FOR lw IN keys (
                        %tky = lw-%tky
                        PartnerFunction = 'VN'
                        %control = VALUE #( PartnerFunction = if_abap_behv=>mk-on ) ) ).
  IF lt_update IS INITIAL.
    RETURN.
  ENDIF.

  MODIFY ENTITIES OF ZR_RootEntity IN LOCAL MODE
  ENTITY ZR_RootEntity
  UPDATE FROM lt_update
  REPORTED DATA(ls_reported).

  reported = CORRESPONDING #( DEEP ls_reported ).
ENDMETHOD.
```

---

> **Observation:** If `PartnerFunction` is empty, both `ShowValueHelpOne` and `ShowValueHelpTwo` will be false — meaning no value help is enabled. Clicking the `Partner` field in this state will throw a value help error.

> **Workaround:** Disable the `Partner` field using **feature control** (`instance features`) whenever `PartnerFunction` is empty, so the user cannot interact with the field until a Partner Function is selected.

> **Note:** The `enabled` property in `@Consumption.valueHelpDefinition` is available from **BTP ABAP Environment, Public Cloud** and **S/4HANA Private Cloud** from **2021** onwards.

---

**XML Annotations Approach**

> **Note:** Both approaches work only in OData V4 apps.

So here no determination, no extra fields just

**CDS:**

```abap
@Consumption.valueHelpDefinition:
    [{
       qualifier: 'ValueHelpOne',
       entity: { name: 'ZI_PartnerVH_One', element: 'Partner' }
      },
      {
       qualifier: 'ValueHelpTwo',
       entity: { name: 'ZI_PartnerVH_Two', element: 'Partner' }
     }
    ]
Partner;
```

**XML Annotation** (`project/webapp/annotations`)**:**

```xml
<Schema xmlns="http://docs.oasis-open.org/odata/ns/edm" Namespace="local">
    <Annotations Target="SAP__self.ZC_RootEntityType/Partner">
        <Annotation Term="com.sap.vocabularies.Common.v1.ValueListRelevantQualifiers">
            <Collection>
                <If>
                    <Eq>
                        <Path>PartnerFunction</Path>
                        <String>VN</String>
                    </Eq>
                    <String>ValueHelpOne</String>
                    <If>
                        <Eq>
                            <Path>PartnerFunction</Path>
                            <String>SP</String>
                        </Eq>
                        <String>ValueHelpTwo</String>
                        <String></String>
                    </If>
                </If>
            </Collection>
        </Annotation>
        <Annotation Term="Common.FieldControl">
            <If>
                <Eq>
                    <Path>PartnerFunction</Path>
                    <String></String>
                </Eq>
                <EnumMember>Common.FieldControlType/ReadOnly</EnumMember>
                <EnumMember>Common.FieldControlType/Optional</EnumMember>
            </If>
        </Annotation>
    </Annotations>
</Schema>
```
