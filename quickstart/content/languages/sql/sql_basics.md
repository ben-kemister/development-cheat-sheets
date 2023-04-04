---
title: Basics
tags:
 - database
 - sql
 - oracle
---

This page contains information and simple code examples about some common simple SQL statements.

## Create Table

The syntax for the `CREATE TABLE` statement in Oracle/PLSQL is:

```sql
CREATE TABLE table_name
( 
    column1 datatype [ NULL | NOT NULL ],
    column2 datatype [ NULL | NOT NULL ],
    ...
    column_n datatype [ NULL | NOT NULL ]
);
```

A basic Oracle CREATE TABLE example.

```sql
CREATE TABLE COMPANY_TYPE
( 
    COMPANY_TYPE_ID NUMBER(38,0) NOT NULL,
    CODE varchar2(10) NOT NULL,
    DESCRIPTION varchar2(50),
    CONSTRAINT COMPANY_TYPE_PK PRIMARY KEY (COMPANY_TYPE_ID)
);
```

For more detailed information [TechOnTheNet](https://www.techonthenet.com/oracle/tables/create_table.php) has a great reference page.

## Insert

The *INSERT* statement is used to insert a single record or multiple records into a table.

The syntax for the Oracle *INSERT* statement when inserting a single record using the VALUES keyword is:

```sql
INSERT INTO table
(column1, column2, ... column_n )
VALUES
(expression1, expression2, ... expression_n );
```

```sql
INSERT INTO COMPANY_TYPE
    (COMPANY_TYPE_ID, CODE, DESCRIPTION)
    VALUES
    (COMPANY_TYPE_SEQ.nextval, 'COMPANY', 'A corporation where the ownership is divided into shares');
```

## Alter Table

The `ALTER TABLE` statement to add a column, modify a column, drop a column, rename a column, add a constraint or rename a table.

### Add a Column

The syntax To ADD A COLUMN in a table, the Oracle ALTER TABLE syntax is:

```sql
ALTER TABLE table_name
ADD column_name column_definition;
```

### Drop a column

For one, or more columns use):
```sql
ALTER TABLE table_name
    drop
    (col_name1, col_name2);
```

### Add foreign key constraint

```sql
ALTER TABLE table_name
    ADD CONSTRAINT constraint_name
    FOREIGN KEY (column1, column2, ... column_n)
    REFERENCES parent_table (column1, column2, ... column_n);
```
