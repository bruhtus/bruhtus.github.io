---
author: Robertus Diawan Chris
title: "Postgresql Update Multiple Rows with One Query"
date: 2023-06-01T15:01:42+07:00
tags: [Postgresql]
ShowToc: true
---

## A Brief Intro

Have you ever wondering how to update multiple rows with one query? Let's say
you want to change value on table A where the name is `anu` on year 2019,
2020, and 2021.

Rather than doing 3 query with different `WHERE` statement, we can do that
with one query. Without further ado, let's go straight to it!

## FROM statement

Postgresql has `FROM` clause in `UPDATE` statement which let us use columns on
other table, it use the same syntax as `FROM` clause in `SELECT` statement.

We can combine `FROM` clause with `VALUES` expression. `VALUES` in postgresql
let us create a constant table which means we can generate a table with
constant values that can be used in a query without having to actually create
and populate the table on disk.

Now here's an example case:<br>
Let's say we need to update the `price` rows on `area` tokyo at table
`meat` with year `2019`, `2020`, and `2021`. We can make the query like this:
```sql
UPDATE meat
SET price = new.price
FROM (values
    (6942, 2019),
    (69420, 2020),
    (69690, 2021),
) AS new(price, year)
WHERE meat.area ILIKE 'tokyo'
AND meat.year = new.year
RETURNING *;
```

You can change the `new` in `new(price, year)` with anything you like.

Another example case:<br>
Let's say we need to update the city's population on specific regencies.
So we need to update data on population column in city table which has
reference to regencies id. We can make the query like this:
```sql
UPDATE city
SET population = new.population
FROM
regencies,
(values
    ('city 1', 69420),
    ('city 2', 6969)
) AS new(regency, population)
WHERE regencies.id = city.regency_id
AND regencies.regency ilike new.regency
RETURNING regencies.regency, city.population;
```

> The `RETURNING` clause is optional, it's a way to check if we really changed the
> right rows or not. The `ILIKE` clause is an expression for insensitive case.

Alright, that's it. See you next time!

## References

- [Stackoverflow example](https://stackoverflow.com/a/18799497).
- [Postgresql update
documentation](https://www.postgresql.org/docs/current/sql-update.html).
- [Postgresql values
documentation](https://www.postgresql.org/docs/current/sql-values.html).
