---
title: SQL Pagination
date: 2025-11-20 19:30 # HH:MM format
categories: howto
tags: sql oracle # always lowercase
---

# SQL Pagination with Materialized View and Pipelined Table Function in Oracle

## Overview
This guide explains how to implement snapshot-based pagination in Oracle using:
- A **Materialized View (MV)** to store a consistent snapshot of the `employees` table.
- A **pipelined table function** to return paginated results as a table type, enabling easy querying with `SELECT * FROM TABLE(...)`.

This approach ensures that all pages are based on the same snapshot, even if the underlying `employees` table changes during pagination.

---

## Step 1: Create the Materialized View
The MV will hold a static snapshot of the `employees` table.

```sql
CREATE MATERIALIZED VIEW mv_employees_snapshot
BUILD IMMEDIATE
REFRESH ON DEMAND
AS
SELECT employee_id, first_name, last_name, email, hire_date
FROM employees
ORDER BY employee_id;
```

- **REFRESH ON DEMAND**: You control when the snapshot updates.
- **ORDER BY employee_id**: Ensures stable ordering for pagination.

---

## Step 2: Refresh the Materialized View
Before starting pagination, refresh the MV to capture the latest data.

```sql
EXEC DBMS_MVIEW.REFRESH('MV_EMPLOYEES_SNAPSHOT', 'COMPLETE');
```

This ensures the MV contains a consistent snapshot of the `employees` table.

---

## Step 3: Create Object and Table Types
Define an object type for a single row and a table type for multiple rows.

```sql
CREATE OR REPLACE TYPE employee_obj AS OBJECT (
    employee_id NUMBER,
    first_name  VARCHAR2(50),
    last_name   VARCHAR2(50),
    email       VARCHAR2(100),
    hire_date   DATE
);

CREATE OR REPLACE TYPE employee_tab AS TABLE OF employee_obj;
```

---

## Step 4: Create the Pipelined Table Function
This function reads from the MV and returns paginated rows using `PIPE ROW`.

```sql
CREATE OR REPLACE FUNCTION get_paginated_mv_pipe (
    p_page_number IN NUMBER,
    p_page_size   IN NUMBER
) RETURN employee_tab PIPELINED
AS
BEGIN
    FOR rec IN (
        SELECT employee_id, first_name, last_name, email, hire_date
        FROM mv_employees_snapshot
        ORDER BY employee_id
        OFFSET ((p_page_number - 1) * p_page_size) ROWS
        FETCH NEXT p_page_size ROWS ONLY
    )
    LOOP
        PIPE ROW(employee_obj(rec.employee_id, rec.first_name, rec.last_name, rec.email, rec.hire_date));
    END LOOP;

    RETURN;
END;
/
```

---

## Example Usage
Query the pipelined function like a table:

```sql
-- Refresh the MV first
EXEC DBMS_MVIEW.REFRESH('MV_EMPLOYEES_SNAPSHOT', 'COMPLETE');

-- Fetch page 2 with 10 rows per page
SELECT *
FROM TABLE(get_paginated_mv_pipe(2, 10));
```

---

## Notes
- Always refresh the MV before starting pagination to ensure consistency.
- This approach guarantees a stable snapshot across all pages.
- For very large datasets, consider indexing the MV for better performance.
