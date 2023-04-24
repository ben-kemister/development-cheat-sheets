---
title: Select Basics
tags:
 - database
 - sql
 - oracle
---

This page contains information, and simple code examples, about SQL select scripts.
<!--more-->

## Count

The SQL COUNT function is used to count the number of rows returned in a SELECT statement.

The syntax for the COUNT function in SQL is:
```oracle-sql
SELECT COUNT(aggregate_expression)
FROM tables
[WHERE conditions]
```

Simple example:
```oracle-sql
SELECT COUNT(*)
FROM Company;
```
