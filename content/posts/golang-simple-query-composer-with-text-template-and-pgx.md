---
author: Robertus Diawan Chris
title: "Golang Simple Query Composer With Text Template and Pgx"
date: 2025-06-22T04:10:37+07:00
tags: [Golang]
ShowToc: false
---

For backend development at work, we just transitioned from `Node.js` to `golang`
recently.  We use `pgx` as our PostgreSQL driver. I'm not quite sure why we
choose `pgx` as our PostgreSQL driver and not using ORM like `gorm`, so we
won't talk about that here. My principal is to adapt to the tech stack that
the project use rather than forcing what i'm familiar with, so i don't really
mind.

Just to be clear, as my workplace tech stack move to golang, that is also the
first time i learn golang. So i have no prior experience with golang. In fact,
by the time i write this post, i have less than one year experience with
golang. You could say that i'm an inexperience golang developer that are
trying things out and see if it works or not.

With that in mind, let's moving into the topic. The problem i'm trying to
solve here is the mess when we're trying to compose an SQL query for `pgx`.
Currently at work, when we compose the SQL query is that we use `+=` to append
new string into existing SQL query string.

Here's an example, let's say we have a table `articles` with this columns:
```
id
author
title
content
created_at
updated_at
```

And then we want to see how many articles each author has. With raw SQL query,
we can do something like this:
```sql
select
articles.author,
count(*) as total
from articles
group by articles.author;
```

If that's all we need, then we can just write those SQL query as is in one
string. Now the problem is, when we want to show only specific author. The
raw SQL query would be something like this:
```sql
select
articles.author,
count(*) as total
from articles
where articles.author ilike '%robertus%'
group by articles.author;
```

Now we have two state, the one with all the authors and the one with specific
author (searching feature). Currently at work, we compose those SQL query like
this:
```go
var args []interface{}

query := `
select
articles.author,
count(*) as total
from articles
`

// Assume `Author` type is `string`.
if filter.Author != "" {
        query += `where articles.author ilike $1`
        args = append(args, filter.Author)
}

query += fmt.Sprintf(`\n%s`, "group by articles.author")

// Assume `db` type is `*pgxpool.Pool`.
rows, err := db.Query(ctx, query, args...)
defer rows.Close()
...
```

I see at least 2 problems with the current query composer:
1. What if we want to add another `where` filter to the query?
2. What if we want to add `where` filter in a sub-query?

Now, what i proposed is that we use golang `text/template` package. I just
recently found out about `text/template` package and i think we can use that
as a simple SQL query composer.

The concept of using `text/template` package as SQL query composer is to use a
placeholder inside the base query string.

With the previous scenario, we can use `text/template` package like this:
```go
// Use named arguments instead of positional arguments.
var (
        composedFilter []string

        queryBuilder    = new(strings.Builder) // Using `strings` package.
        conditionalTmpl = make(map[string]string)
        filterArgs      = make(pgx.StrictNamedArgs)
)

// The `-` at `{{end -}}` is to remove whitespace after the line.
baseQuery := `
select
articles.author,
count(*) as total
from articles
{{if .Where}}{{.Where}}{{end -}}
group by articles.author
`

// Assume `Author` type is `string`.
if filter.Author != "" {
        composedFilter = append(composedFilter, `articles.author ilike @author`)
        // Key `author` is the named argument `@author`, so make sure the name is
        // the same.
        filterArgs["author"] = fmt.Sprintf("%%%s%%", filter.Author)
}

if len(composedFilter) > 0 {
        // This is a placeholder in `{{if .Where}}...` so make sure the name
        // is the same.
        conditionalTmpl["Where"] = fmt.Sprintf(
                "where %s\n",
                strings.Join(composedFilter, ` AND `),
        )
}

queryTmpl := template.Must(template.New("baseQuery").Parse(baseQuery))

if err != queryTmpl.Execute(queryBuilder, conditionalTmpl); err != nil {
        ...
}

query := queryBuilder.String()

// Assume `db` type is `*pgxpool.Pool`.
rows, err := db.Query(ctx, query, filterArgs)
defer rows.Close()
...
```

With the approach above, we can add some placeholder for SQL query `where`
clause, or `order by` clause, or other things that is optional to the feature.

My concern with the `text/template` approach is that i don't know the failure
condition for `template.Must()`, so that might become a consideration when
deciding to use this approach or not. It's similar to when `google/uuid`
package panic when using `uuid.New()`, you can check it out on [this
issue about the context](https://github.com/google/uuid/issues/97).

I already proposed this mechanism at work but i'm not sure if this mechanism
will be used. So, i'm posting this in case someone need an inspiration for
simple SQL query composer.

Alright, that's it. See you next time!

## References
- [Stackoverflow format go string](https://stackoverflow.com/a/31742265).
