---
author: Robertus Diawan Chris
title: "Docker Compose Down Without Yaml File"
date: 2023-03-22T09:59:40+07:00
tags: [Notes, Linux]
ShowToc: true
---

## A Brief Intro

Have you ever feel that you have too much docker container from spin up a
bunch of `docker-compose.yaml` file? And when you want to take down all those
docker container from the `docker-compose.yml`, you already delete the file?

If that's the case, then this post might be for you.

Before we start, we will be using docker compose **version 2.16.0** in this post.
If you are using the older version of docker compose, then this method might
not work. Please keep that in mind.

## Check Docker Compose Project Name

The first thing we need to do is to check the project name. We can do that
with command:
```sh
docker-compose ls
```

or if we use docker compose v2, we can use this command instead:
```sh
docker compose ls
```

If the container already stopped, we can use flags `-a` or `--all` like this:
```sh
docker-compose ls -a
```

or this:
```sh
docker compose ls -a
```

By default, docker compose will use the `docker-compose.yml` directory name as
project name. We can specify the project name with flag `-p` or
`--project-name` like this:
```sh
docker-compose -p anu up -d
```

or this:
```sh
docker compose -p anu up -d
```

**Please keep in mind that we need to use the flags `-p` or `--project-name`
before the command such as `up` or `down`**.

## Take Down Docker Compose Component

After we know the project name, we can take down all docker component when we
spin up the docker container from `docker-compose.yml`. The component in here
means volume, network, or anything that we define inside `docker-compose.yml`.

To take down component from docker compose without the `docker-compose.yml`,
we can use this command:
```sh
docker-compose -p project-name down <docker-compose-down-flags>
```

or this:
```sh
docker compose -p project-name down <docker-compose-down-flags>
```

For example, let's say we want to take down docker component from project `anu` and also remove the volume from those project. We can do that with this command:
```sh
docker-compose -p anu down -v
```

or this:
```sh
docker compose -p anu down -v
```

Alright, that's it!

I am not sure since when the `ls` command appear in docker compose, so just
to be safe i recommend using at least docker compose version 2.16.0.

## Side Note

I've tried this method with docker compose version 1.18.0, but failed. After i
downloaded the docker compose binary version 2.16.0 from github, i can use
this method.

Still not sure what was wrong. When i use command `docker-compose --help`, the
flag `-p` in there. But when i actually spin up a new docker instance with
command:
```sh
docker-compose -p project-name up -d service-name
```

and then tried to take it down with command:
```sh
docker-compose -p project-name down -v
```

i got an error `docker-compose.yml not found` or along those lines.

If someone know what's going on, please let me know!
