---
title: "UI5: Dynamic Grid Table Columns Driven by OData Metadata"
date: 2025-12-15 08:00:00 +0530
categories: [UI5]
tags: [ui5, fiori, odata, metadata, grid-table, javascript, cds]
---

When the standard Fiori floorplan can't meet a requirement, a custom dialog or full UI breakout is the way to go. The challenge: keeping the frontend in sync with the backend without hardcoding every field.

**Solution:** read `@UI.LineItem` annotations from the OData MetaModel at runtime and build the table columns dynamically.

## How it Works

- Add a CDS field with `@UI.lineItem` → column appears automatically
- Set `hidden: true` in the annotation → gone from UI and `$select`
- Rename a label in CDS → reflected in the column header instantly
- No JS or XML change needed for any of the above

## ABAP CDS

```abap
@EndUserText.label: 'Custom Entity'
@ObjectModel.query.implementedBy: 'ABAP:ZCLASS'
define custom entity ZCustomEntity
  with parameters
    P_CustomerID : char40,
    P_Status     : char40
{
    @UI.lineItem: [{ position: 10, label: 'Customer' }]
    key customer : char40,

    @UI.lineItem: [{ position: 20, label: 'Order ID', hidden: true }]
    order_id : char10,

    @UI.Hidden: true
    internal_key : char120
}
```

## XML View

```xml
<t:Table id="idMyTable" rows="{tableModel>/items}" width="100%">
    <t:columns/>
</t:Table>
```

Columns are built programmatically — no static column definitions in XML.

## JavaScript

```javascript
var oMeta       = oView.getModel().getMetaModel();
var sEntityPath = "/ZCustomEntity/Set/";

// Step 1 — resolve columns from annotations
var aLineItems = oMeta.getObject(
    sEntityPath + "@com.sap.vocabularies.UI.v1.LineItem"
) || [];

var aColumns = aLineItems.map(function(oItem) {
    var sField = oItem.Value.$Path;
    return {
        field:   sField,
        label:   oItem.Label
                 || oMeta.getObject(sEntityPath + sField + "@com.sap.vocabularies.Common.v1.Label")
                 || sField,
        visible: !oItem["@com.sap.vocabularies.UI.v1.Hidden"]
                 && !oMeta.getObject(sEntityPath + sField + "@com.sap.vocabularies.UI.v1.Hidden")
    };
});

// Step 2 — build table columns
var oTable = oView.byId("idMyTable");
oTable.destroyColumns();
aColumns.forEach(function(oCol) {
    oTable.addColumn(new Column({
        visible:  oCol.visible,
        label:    new Label({ text: oCol.label }),
        template: new Text({ text: "{tableModel>" + oCol.field + "}" })
    }));
});

// Step 3 — fetch only visible fields via $select
var aSelectFields = aColumns
    .filter(function(c) { return c.visible; })
    .map(function(c) { return c.field; });

var oContexts = await oView.getModel().bindList(
    "/ZCustomEntity(P_CustomerID='" + sCustomerId + "',P_Status='" + sStatus + "')/Set",
    undefined, undefined, undefined,
    { $select: aSelectFields.join(",") }
).requestContexts(0, 99999999);

// Step 4 — set model
oView.setModel(new JSONModel({
    items: oContexts.map(function(c) { return c.getObject(); })
}), "tableModel");
```

The `$select` is built dynamically from visible columns — no hidden fields fetched from the backend.
