---
title: "Identify the Leading Entity in the Service Binding"
date: 2026-11-23 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, service-binding, service-definition, leading-entity]
---

If you're working on a large RAP-based project, it can sometimes be confusing to identify the root entity in the service binding, especially when multiple entities are exposed. To simplify this, you can use the `@ObjectModel.leadingEntity.name` annotation in the service definition.

By adding this annotation, you clearly define the leading entity of your service. This helps quickly locate the starting node for app preview in the service binding. The designated leading entity will be marked with a small **'L'** icon, making it easy to spot.

```cds
@ObjectModel.leadingEntity.name: '/DMO/FSA_C_RootTP'
define service /DMO/UI_FeatureShowcaseApp {
  ...
}
```

> **Availability:** From S/4HANA 2023 onwards in Private Cloud Edition and latest versions of the Public Cloud.

For more details, refer to the [ABAP Platform Fiori Feature Showcase](https://github.com/SAP-samples/abap-platform-fiori-feature-showcase/blob/main/01_general_features.md#leading-entity).

![Leading Entity marked in Service Binding](/assets/images/rap_service_binding_leading_entity.jpg)
