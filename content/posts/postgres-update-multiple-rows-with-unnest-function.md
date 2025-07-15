---
author: Robertus Diawan Chris
title: "Postgres Update Multiple Rows With Unnest Function"
date: 2025-07-15T12:56:22+07:00
tags: [Notes, Postgresql]
ShowToc: true
---

## A Brief Intro

At work, i need to build an API that enable user to update specific info in
multiple rows. Previously, i made a blog post about [postgres update multiple
data in one query](../postgres-update-multiple-rows-in-one-query), but those
method is more convenient for running raw sql in the postgresql database
directly rather than for API. I mean, sure, we can use
`FROM (values (...), (...), ...)` but personally, i don't really want to
compose multiple values with positional parameters in postgres.
And since we use `pgx` in golang as our postgres driver, i thought
"can we put all the info we need in an array when trying to update multiple
values?", and looks like we can!

## Unnest Function

I learn about the existence of `unnest()` from this website:<br>
https://www.w3resource.com/PostgreSQL/postgresql_unnest-function.php

So, what i do is that i put all the data in a golang array. Let's say we have
`pgx` named arguments `@ids` to hold all ids that we want to update, which in
this case let's assume we use UUID as our id, and then `@price` to hold the data
that we want to update. And the table we want to update is called `meat`.

After validation and all those stuff, we get the necessary data and this is
the query for updating multiple data with `unnest()` for `pgx` golang:
```sql
UPDATE meat as m
SET price = c.price
FROM unnest(@ids::uuid[], @price::int[]) as c(id, price)
WHERE m.id = c.id
RETURNING m.*
```

Please keep in mind that we need to specify the array type in postgres like
`uuid[]` and `int[]` because apparently `pgx` using array in string format
like `unnest('{1, 2, 3}')` so postgres get confused and throw an error
`unnest(unknown) ... unique` (?). This is still an assumption and might be
incorrect but the query is working fine after casting the type.

Also, i'm still not sure about the performance of using postgres `unnest()` so
it's something to keep in mind.

All right, that's it. See you next time!

## References

- [w3resource about postgresql unnest() function](https://www.w3resource.com/PostgreSQL/postgresql_unnest-function.php)
- [Stackoverflow answer about unnest(unknown)
error](https://stackoverflow.com/a/52682829)
