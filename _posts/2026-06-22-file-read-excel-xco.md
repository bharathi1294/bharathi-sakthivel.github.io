---
title: "ABAP: Read Excel Files with the XCO Library"
date: 2026-06-22 08:00:00 +0530
categories: [File Handling]
tags: [abap, excel, xco, xlsx, file-handling, btp, steampunk]
---

The XCO Library (`xco_cp_xlsx`) is the modern way to read `.xlsx` files in SAP BTP ABAP Environment and S/4HANA. No function modules, no legacy classes.

## Step 1: File Selection and Upload

```abap
" Open file dialog (frontend)
DATA lt_file_path TYPE filetable.
DATA lv_rc        TYPE i.

cl_gui_frontend_services=>file_open_dialog(
    EXPORTING file_filter = '*.xlsx'
    CHANGING  file_table  = lt_file_path
              rc           = lv_rc
).

" Upload binary content
DATA lt_binary TYPE solix_tab.
DATA lv_size   TYPE i.

cl_gui_frontend_services=>gui_upload(
    EXPORTING filename   = lt_file_path[ 1 ]-filename
              filetype   = 'BIN'
    IMPORTING filelength = lv_size
    CHANGING  data_tab   = lt_binary
).

" Convert to xstring
DATA(lv_xlsx) = cl_bcs_convert=>solix_to_xstring( lt_binary ).
```

## Step 2: Read with XCO

```abap
TYPES: BEGIN OF ty_employee,
         emp_id   TYPE string,
         emp_name TYPE string,
         mobile   TYPE string,
         dob      TYPE string,
       END OF ty_employee.

DATA lt_employees TYPE TABLE OF ty_employee.

" Access the workbook
DATA(lo_xlsx)      = xco_cp_xlsx=>document->for_xstring( lv_xlsx ).
DATA(lo_worksheet) = lo_xlsx->get_workbook( )->get_worksheet_by_index( 1 ).

" Read all data (entire sheet)
lo_worksheet->get_cell_cursor( )->select_all( )->get_rows( )->loop_at_elements( 
    REFERENCE INTO DATA(lo_row)
).
```

## Step 3: Flexible Selection Patterns

```abap
DATA(lo_cursor) = lo_worksheet->get_cell_cursor( ).

" Entire sheet
lo_cursor->select_all( ).

" Specific columns (A to C)
lo_cursor->select_column_range(
    io_from = xco_cp_xlsx_selection=>column->for_alphabetic_value( 'A' )
    io_to   = xco_cp_xlsx_selection=>column->for_alphabetic_value( 'C' )
).

" Specific rows (2 to 5 — skip header row 1)
lo_cursor->select_row_range(
    io_from = xco_cp_xlsx_selection=>row->for_index( 2 )
    io_to   = xco_cp_xlsx_selection=>row->for_index( 5 )
).

" Column A-C + Row 2-5 combined
lo_cursor->select_column_range( ... )->select_row_range( ... ).
```

## Step 4: Map Rows to Internal Table

```abap
lo_cursor->get_rows( )->loop_at_elements( 
    NEW lcl_xlsx_row_handler( CHANGING ct_employees = lt_employees )
).
```

Where `lcl_xlsx_row_handler` implements `if_xco_xlsx_sel_row_handler`:

```abap
METHOD if_xco_xlsx_sel_row_handler~process_row.
  DATA ls_emp TYPE ty_employee.
  io_row->get_cell( xco_cp_xlsx_selection=>column->for_alphabetic_value( 'A' ) 
      )->get_value( )->set_to( CHANGING cv_value = ls_emp-emp_id ).
  io_row->get_cell( xco_cp_xlsx_selection=>column->for_alphabetic_value( 'B' ) 
      )->get_value( )->set_to( CHANGING cv_value = ls_emp-emp_name ).
  APPEND ls_emp TO ct_employees.
ENDMETHOD.
```

> XCO is available in **BTP ABAP Environment** and **S/4HANA Public Cloud**. For on-premise, use `CL_FDT_XL_SPREADSHEET` or the XLSX Workbench.
