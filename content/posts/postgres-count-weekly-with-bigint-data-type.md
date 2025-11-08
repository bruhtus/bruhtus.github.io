---
author: Robertus Diawan Chris
title: "Postgresql Count Weekly With Bigint Data Type"
date: 2023-06-26T10:58:13+07:00
tags: [Postgresql]
ShowToc: true
---

## A Brief Intro

Let's say we have a `created_at` column in our database with data type big
integer that has a value with this equation:
```sql
EXTRACT(EPOCH FROM NOW()) * 1000
```
> Don't ask me why the value of `created_at` like that because i have no idea.

Now, the goals is to count every data per week.

## How?

How to do that? Because we use a unix timestamp which look like this
`1687746597339`, we need to convert those into timestamp using postgresql
`to_timestamp()` function like this:
```sql
to_timestamp(created_at/1000)
```

> We divide by 1000 because we multiple it by 1000 when we insert the value.

And then, we need to truncate into a specific date. We can use `date_trunc()`
function to truncate the date.

According to the documentation, this is the parameter for `date_trunc()`:
```sql
date_trunc(field, source [, time_zone ])
```

Valid values for `field` are:
- microseconds
- milliseconds
- second
- minute
- hour
- day
- week
- month
- quarter
- year
- decade
- century
- millennium

`source` is either timestamp or interval and return timestamp or interval
depending on the source.

`time_zone` is an optional parameter to specify a different timezone. By
default, truncation is done with the current timezone setting.

If we combine `date_trunc()` and `to_timestamp()`, we will get something like
this:
```sql
date_trunc('week', to_timestamp(created_at/1000))
```

If we make the entire query, it would be something like this (let's say the
table name is `participants`):
```sql
SELECT
date_trunc('week', to_timestamp(created_at/1000)) AS weekly,
COUNT(*) AS total_participants
GROUP BY weekly
ORDER BY weekly ASC;
```

And the result would be something like this:
```sql
         weekly         | total_participants
------------------------+--------------------------
 2023-06-19 00:00:00+07 |                    69420
 2023-06-26 00:00:00+07 |                       69
```

> `00:00:00+07` is because the timezone setting on my database is
> `asia/jakarta` which is basically UTC+7.
> If your timezone setting is different, the timestamp might be different.
>
> Still not sure why the `date_trunc()` with field `week` start at monday,
> and, at the time of writing this post, i haven't found a settings to change
> that.

Alright, that's it. See you next time!

## References

- [Postgresql Date/Time Functions and Operator
Documentations](https://www.postgresql.org/docs/current/functions-datetime.html).
- [Postgresql tutorial date_trunc()
function](https://www.postgresqltutorial.com/postgresql-date-functions/postgresql-date_trunc/).
- [Stackoverflow group by week in
postgresql](https://stackoverflow.com/a/36024734).
