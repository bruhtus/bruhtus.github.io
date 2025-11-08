---
author: Robertus Diawan Chris
title: "Postgresql's USING Clause on DELETE Statement"
date: 2023-06-01T14:07:25+07:00
tags: [Postgresql]
ShowToc: true
---

## A Brief Intro

The `USING` clause on `DELETE` statement is basically let us join multiple table
and delete only the data from those join.

If you ever use mysql `DELETE JOIN` with `INNER JOIN`, then postgresql `USING`
is *kind of* similar to that except it can only delete from one table by
default.

## How?

How to do that? Here's an example:<br>
Let's say we have table A and table B which is referenced to table C by id. If
we use `SELECT` statement on table C with `INNER JOIN` on table A, it would look
like this:
```sql
SELECT *
FROM C
INNER JOIN A
ON A.C_id = C.id;
```

Now, if we want to **only** delete all records from table C related to table A
without deleting all records from table C related to table B, we can use
`DELETE` with `USING` clause like this:
```sql
DELETE
FROM C
USING A
WHERE A.C_id = C.id;
```

> Please keep in mind that if you haven't set the `ON DELETE CASCADE` for
> table A on reference `C_id`, that query will throw an error.

Alright, that's it. See you next time!

## References

- [Postgresql delete
documentation](https://www.postgresql.org/docs/current/sql-delete.html).
- [Postgresql delete
using tutorial](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-delete-join).
- [MySQL delete join
tutorial](https://www.mysqltutorial.org/mysql-delete-join).
