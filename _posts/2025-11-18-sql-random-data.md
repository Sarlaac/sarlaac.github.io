---
title: Random SQL Data
date: 2025-11-18 20:30 # HH:MM format
categories: howto
tags: sql oracle # always lowercase
---

# Oracle Random Data Generation Guide

This guide explains how to generate random data in Oracle using PL/SQL. It includes:
- A reusable function `generate_random_data` for creating random values.
- A procedure `populate_random_data` for populating a table with random rows.

---

## 1. Function: `generate_random_data`

This function generates random data based on the specified **data type** and **length**.

### Supported Data Types:
- `NUMBER`: Random number with specified length.
- `DATE`: Random date within the last 10 years.
- `VARCHAR2`: Random string of specified length.

### Code:
```sql
CREATE OR REPLACE FUNCTION generate_random_data (
    p_length    IN NUMBER,
    p_datatype  IN VARCHAR2
) RETURN VARCHAR2 IS
    v_result VARCHAR2(4000);
BEGIN
    CASE UPPER(p_datatype)
        WHEN 'NUMBER' THEN
            -- Generate a random number with p_length digits
            v_result := TO_CHAR(TRUNC(DBMS_RANDOM.VALUE(POWER(10, p_length - 1), POWER(10, p_length))));

        WHEN 'DATE' THEN
            -- Generate a random date within the last 10 years
            v_result := TO_CHAR(TRUNC(SYSDATE - DBMS_RANDOM.VALUE(0, 3650)), 'DD-MON-YYYY');

        WHEN 'VARCHAR2' THEN
            -- Generate a random string of length p_length
            v_result := DBMS_RANDOM.STRING('U', p_length);

        ELSE
            RAISE_APPLICATION_ERROR(-20001, 'Unsupported data type: ' || p_datatype);
    END CASE;

    RETURN v_result;
END;
/
```

### Example Usage:
```sql
SELECT generate_random_data(5, 'NUMBER') FROM dual;
SELECT generate_random_data(10, 'VARCHAR2') FROM dual;
SELECT generate_random_data(0, 'DATE') FROM dual;
```

---

## 2. Procedure: `populate_random_data`

This procedure populates a specified table with random data for all its columns.

### Features:
- Dynamically reads column metadata.
- Generates fresh random values for each row.
- Supports `VARCHAR2`, `NUMBER`, and `DATE` columns.

### Code:
```sql
CREATE OR REPLACE PROCEDURE populate_random_data (
    p_table_name   IN VARCHAR2,
    p_row_count    IN NUMBER
) IS
    v_sql       VARCHAR2(32767);
    v_cols      VARCHAR2(32767);
BEGIN
    -- Build column list dynamically
    FOR col IN (
        SELECT column_name, data_type, data_length
        FROM user_tab_columns
        WHERE table_name = UPPER(p_table_name)
        ORDER BY column_id
    ) LOOP
        v_cols := v_cols || col.column_name || ', ';
    END LOOP;

    v_cols := RTRIM(v_cols, ', ');

    -- Insert rows
    FOR i IN 1..p_row_count LOOP
        DECLARE
            v_values VARCHAR2(32767);
        BEGIN
            -- Generate new random values for each row
            FOR col IN (
                SELECT column_name, data_type, data_length
                FROM user_tab_columns
                WHERE table_name = UPPER(p_table_name)
                ORDER BY column_id
            ) LOOP
                IF col.data_type IN ('VARCHAR2', 'CHAR') THEN
                    v_values := v_values || ''' || generate_random_data(col.data_length, 'VARCHAR2') || '', ';
                ELSIF col.data_type = 'NUMBER' THEN
                    v_values := v_values || generate_random_data(5, 'NUMBER') || ', ';
                ELSIF col.data_type = 'DATE' THEN
                    v_values := v_values || 'TO_DATE('' || generate_random_data(0, 'DATE') || '', ''DD-MON-YYYY''), ';
                ELSE
                    v_values := v_values || 'NULL, ';
                END IF;
            END LOOP;

            v_values := RTRIM(v_values, ', ');

            v_sql := 'INSERT INTO ' || p_table_name || ' (' || v_cols || ') VALUES (' || v_values || ')';
            EXECUTE IMMEDIATE v_sql;
        END;
    END LOOP;

    COMMIT;
END;
/
```

### Example Usage:
```sql
BEGIN
    populate_random_data('EMPLOYEES', 10);
END;
/
```

---

## Notes:
- Ensure the table has no NOT NULL constraints without defaults.
- Unsupported column types will be set to NULL.
- You can extend the logic for more data types like CLOB, TIMESTAMP, etc.

---

## Tips:
- Use `DBMS_RANDOM.STRING('A', length)` for mixed case letters.
- Adjust date range by changing `DBMS_RANDOM.VALUE(0, 3650)`.
