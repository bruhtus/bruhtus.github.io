---
author: Robertus Diawan Chris
title: "Postgresql Get Current Database Size"
date: 2023-07-18T07:07:04+07:00
tags: [Notes, Postgresql]
ShowToc: true
---

## A Brief Intro

Have you ever wondering the size of our current postgresql database? If so,
this short post might be for you.

Let's get started!

## How?

In postgresql, there's a function called `pg_database_size()` which computes
the total disk space used by the database with specified database name.

We can use `pg_database_size()` like this:
```sql
SELECT pg_database_size('db-name');
```

The output of `pg_database_size()` is in bytes, so if we want to display
the output in a human-readable format (kb, mb, and so on), we can use function
`pg_size_pretty()` like this:
```sql
SELECT pg_size_pretty(pg_database_size('db-name'));
```

If we want to check all available database, we can use `pg_database` system
table like this:
```sql
SELECT
pg_database.datname AS db_name,
pg_size_pretty(pg_database_size(pg_database.datname)) AS db_size
FROM pg_database
ORDER BY pg_database_size(pg_database.datname) DESC;
```

Alright, that's all for now. See you next time!

## References

- [Postgresql System Administration Functions
Documentation](https://www.postgresql.org/docs/current/functions-admin.html).
- [Stackoverflow how to get db names and
size](https://stackoverflow.com/a/18907188).
