---
author: Robertus Diawan Chris
title: "Psql Copy Functionality"
date: 2025-07-16T13:06:58+07:00
tags: [Postgresql]
ShowToc: true
---

## A Brief Intro

I need to replace data on one database server with data on another database
server. Let's call it server 1 for the source server and server 2 for the
target server. But there are 2 limitations:
- We only need to replace data on specific table.
- We only able to access the database through psql with VPN (at least that's
how i understand it).

With that in mind, i'm trying to solve those problem with `psql \copy`. Why
did i use `\copy` instead of sql `copy`? From postgres documentation:<br>
> file accessibility and access rights depends on the client rather than the
> server when `\copy` is used.

If i want to retrieve the file on my local machine, using `\copy` is more
convenient as i use `psql` on my local machine.

## \copy to

Assuming we already inside `psql`, to copy the data from database table to csv
file, we can do some like this:
```
\copy (SELECT * FROM meat) to '/home/user/meat.csv' with csv header
```

Here's the breakdown of the previous command means:
- `SELECT * FROM meat` is the sql query to get the data we want.
- `to '/home/user/meat.csv'` is the target where we want the data go into. In
this case, the data will be saved in `/home/user/meat.csv`.
- `with csv header` is to include the column in the specified table from the
select query as a csv header. This can be useful in case we want to debug the
query result from csv file.

## \copy from

Assuming we already inside `psql`, we already got the data source we want, and
we already delete all the existing data (keep in mind that in this scenario,
we need to replace the data). After the preparation is done, we can use this
command:
```
\copy meat from '/home/user/meat.csv' delimiter ',' csv header
```

Here's the breakdown of the previous command means:
- `meat` is the target table we need to fill in.
- `from '/home/user/meat.csv'` is the target path that have the data we want.
- `delimiter ','` is to indicate a delimiter to split the data.
- `csv header` is to indicate that the first row contains the header.

## Conclusions

Keep in mind that this is only one of the solution to solve the problem of
replacing data from one database server to another database server and this
might not be the best solution. So rather than focusing on the scenario, just
treat it as something to help us understand the mechanism of `psql \copy`. The
point of this post is to let you know about the existence of `psql \copy` and
you can tweak it according to your need.

All right, that's it. See you next time!

## References

- [Stackexchange psql copy answer](https://dba.stackexchange.com/a/167076).
- [Postgresql copy sql
documentation](https://www.postgresql.org/docs/current/sql-copy.html).
