---
author: Robertus Diawan Chris
title: "Systemd Reset Failed Status"
date: 2023-07-18T03:01:00+07:00
tags: [Notes]
ShowToc: true
---

## A Brief Intro

Have you ever experience a failed systemd service and already trying to
restart using `systemctl restart <some-service>` but the `failed` status still
there?

If so, this short post might be for you! Let's get started.

## Reset Failed Status

Now, if our systemd service has a `failed` status, we can try using command:
```sh
systemctl reset-failed <some-service>
```
to remove the `failed` status and reset it to recent status.

Please keep in mind that `reset-failed` is dependent on `Restart` option, so
if you use option `Restart=always`, there's a chance that the `failed` status
still there because systemd still trying to restart our service which result
in another `failed` status.

There are still a lot more i need to learn about systemd service, so for now
that's all. See you next time!
