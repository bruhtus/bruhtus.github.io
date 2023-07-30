---
author: Robertus Diawan Chris
title: "Postgres Exist Clause"
date: 2023-07-20T07:12:44+07:00
tags: [Notes, Postgresql]
ShowToc: true
---

## A Brief Intro

Have you ever wondering, rather than show the data, can we show the
`true` or `false` if the data exist or not?

If so, then this short post might be for you!

## How?

In this post we will take a look at `exists` postgresql function. Before we
start, please keep in mind that `exists` use subquery, so there is a penalty
of subquery here.

The `exists` will evaluate if the subquery return any rows. If it returns at
least one row, the result of `exists` is `true`, if not, then the result of
`exists` is `false`.

We can use that in `SELECT` statement like this:
```sql
SELECT
exists (
  SELECT users.email
  FROM users
  WHERE users.email = participants.email
)
FROM participants;
```

Or we can use it in `WHERE` statement like this:
```sql
SELECT participants.email
FROM participants
WHERE exists (
  SELECT users.email
  FROM users
  WHERE (users.role = 'sponsor' AND users.email = participants.email)
)
```

This can be useful if we don't want to join the table. Alright, that's it for
this post. See you next time!

## References

- [Postgresql subquery expressions
documentation](https://www.postgresql.org/docs/current/functions-subquery.html).
- [Stackoverflow return false if no rows](https://stackoverflow.com/a/68279916).
