---
title: "CDS: Dynamically Hide Fields, Facets, and Actions Based on Data"
date: 2026-04-20 08:00:00 +0530
categories: [CDS]
tags: [cds, fiori, rap, hidden, dynamic-ui, annotations, field-control]
---

Hide fields, sections, or CRUD buttons dynamically based on a boolean field derived from your data — no custom controller needed.

## Step 1: Derive a Boolean in CDS

```abap
define view entity ZR_Employee
  as select from zemp_table
{
  key emp_id,
  employee_status,
  course_list,

  case employee_status
    when 'RETIRED'
    then cast( 'X' as abap_boolean preserving type )
    else cast( '' as abap_boolean preserving type )
  end as HideCourseList
}
```

## Step 2: Hide an Entire Facet/Section

```abap
@UI.facet: [
  {
    id:      'CourseFacet',
    type:    #FIELDGROUP_REFERENCE,
    label:   'Courses',
    hidden:  #( HideCourseList )
  }
]
```

## Step 3: Hide Individual Fields

```abap
@UI.hidden: #( HideCourseList )
CourseList;
```

## Step 4: Hide Standard CRUD Operations (S/4HANA 2023+)

```abap
@UI.createHidden: #( HideCourseList )
@UI.updateHidden: #( HideCourseList )
@UI.deleteHidden: #( HideCourseList )
```

## Step 5: Hide Inside Field Groups

```abap
@UI.identification: [{
  position: 10,
  hidden:   #( HideCourseList )
}]
CourseList;

@UI.fieldGroup: [{
  qualifier: 'DetailsGroup',
  position:  20,
  hidden:    #( HideCourseList )
}]
CourseList;
```

## Summary

| Target | Annotation |
|---|---|
| Facet/Section | `@UI.facet: [{ hidden: #( BoolField ) }]` |
| Individual field | `@UI.hidden: #( BoolField )` |
| Create button | `@UI.createHidden: #( BoolField )` |
| Update button | `@UI.updateHidden: #( BoolField )` |
| Delete button | `@UI.deleteHidden: #( BoolField )` |

All driven by a single boolean field — no annotations to change when business rules change, just update the CASE expression in your CDS view.
