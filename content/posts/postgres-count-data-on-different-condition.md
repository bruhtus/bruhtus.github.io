---
author: Robertus Diawan Chris
title: "Postgresql Count Data on Different Condition in The Same Query"
date: 2023-07-18T07:07:47+07:00
tags: [Notes, Postgresql]
ShowToc: true
---

## A Brief Intro

Have you ever wondering how to count all the data and specific data with a
certain condition on one query? If so, then this short post might be for you.

Let's get started!

## How?

Let's say we organize an event and we store the participants data on our
postgresql database.

Now we want to know all the participants that registered for the event and all
the participants that actually attended the event. That means, even though
someone register for the event, they might not attend the event.

With that in mind, we can use `FILTER` clause in our query like this:
```sql
SELECT
count(*) AS registered_participants,
count(*) FILTER (WHERE participants.status = 'attended') AS
attended_participants
FROM participants;
```

Alright, that's it for now. See you next time!

## References

- [Postgresql sql expressions
documentation](https://www.postgresql.org/docs/current/sql-expressions.html).
- [Stackexchange count with different condition on the same
query](https://dba.stackexchange.com/a/112797).
