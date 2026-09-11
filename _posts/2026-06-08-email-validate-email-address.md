---
title: "ABAP: Validate Email Addresses Using CL_BCS_EMAIL_ADDRESS"
date: 2026-06-08 08:00:00 +0530
categories: [Email]
tags: [abap, email, validation, cl-bcs-email-address, rfc-5322]
---

Don't write custom regex for email validation — SAP provides a standard class that validates against RFC 5322.

## Using cl_bcs_email_address=>validate()

```abap
CLASS lcl_email DEFINITION CREATE PUBLIC.
  PUBLIC SECTION.
    CLASS-METHODS validate
      IMPORTING iv_email        TYPE string
      RETURNING VALUE(rv_valid) TYPE abap_boolean.
ENDCLASS.

CLASS lcl_email IMPLEMENTATION.
  METHOD validate.
    TRY.
        cl_bcs_email_address=>validate( iv_email ).
        rv_valid = abap_true.
      CATCH cx_address_exception.
        rv_valid = abap_false.
    ENDTRY.
  ENDMETHOD.
ENDCLASS.
```

## Usage

```abap
IF lcl_email=>validate( 'user@example.com' ) = abap_true.
  " Valid email
ELSE.
  " Invalid email
ENDIF.
```

## Test Cases

| Email | Result |
|---|---|
| `user@example.com` | Valid |
| `user.name+tag@domain.co` | Valid |
| `user@domain` | Invalid (no TLD) |
| `@domain.com` | Invalid (no local part) |
| `user@` | Invalid |
| `plaintext` | Invalid |

## Why Use the Standard Class?

- Implements **RFC 5322** — the actual email standard
- No maintenance — SAP keeps it up to date
- Handles edge cases that custom regex often misses (quoted strings, internationalized domains)
- Works in both on-premise and BTP ABAP Environment

One try-catch, one method call — clean and reliable.
