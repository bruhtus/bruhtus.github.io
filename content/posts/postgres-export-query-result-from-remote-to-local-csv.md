---
author: Robertus Diawan Chris
title: "Postgresql Export Query Result From Remote to Local in CSV file"
date: 2023-07-25T04:22:04+07:00
tags: [Postgresql]
ShowToc: true
---

## A Brief Intro

Have you ever thought of how to get postgresql query result from remote server
to our local machine?

If so, this short post might be for you.

## How?

Before we start, please keep in mind that there are other methods to get
postgresql query result from remote server to local machine, the method that i
mentioned after this is the easier method for me.

Alright, let's get to it!

Before we start, you need to configure the ssh stuff like private key and so
on. So i will assume you already set it up in your ssh config.

In this post we'll use `psql` to get the query result and `copy()` function
from postgresql.

Now, let's say we have this query:
```sql
SELECT count(*) as user_count
FROM users;
```

and we want to put those query result in `user-count.csv`.

The first thing we need to do is make a `query` file with the sql query above
and `copy()` like this:
```sql
copy(
  SELECT count(*) as user_count
  FROM users;
)
TO stdout WITH csv header
```

For simplicity, we will put those sql query in a file called `query`. After
that, we can use this command:
```sh
ssh server-config 'psql -d postgresql://user:password@localhost:5432/db-name' < query
```

Please keep in mind that `server-config` is our ssh configuration in
`~/.ssh/config`, and `postgresql://user:password@localhost:5432/db-name` is
our postgresql data source name (DSN).

If our query has result, we will see them in our terminal. Now to put those
result in csv file, we can use redirection like this:
```sh
ssh server-config 'psql -d postgresql://user:password@localhost:5432/db-name' < query > user-count.csv
```

And, that's it. Now we have our query result in csv file. Alright, see you
next time!

## References

- [Stackoverflow ssh with psql](https://stackoverflow.com/a/49992062).
- [Stackoverflow psql using stdin](https://stackoverflow.com/a/28803698).
