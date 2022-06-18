---
author: Robertus Diawan Chris
title: "Awk Print Row Instead of Column"
date: 2022-06-18T08:53:01+07:00
tags:
ShowToc: true
---

## Main Course

Let's say we have an output from `xrandr --listactivemonitor` like this:
```sh
Monitors: 2
 0: +*eDP-1 1920/344x1080/194+1920+0  eDP-1
 1: +HDMI-2 1920/480x1080/270+0+0  HDMI-2
```

Now, we want to display only the second row which is this line:
```sh
 0: +*eDP-1 1920/344x1080/194+1920+0  eDP-1
```

We can do that using `awk` with this command:
```sh
xrandr --listactivemonitor | awk 'NR==2'
```
or using process substitution command, like this:
```sh
awk 'NR==2' <(xrandr --listactivemonitor)
```

If we also want to limit the row and the column, let's say the second row and
the third column which is result in this:
```sh
1920/344x1080/194+1920+0
```

We can do that with this command:
```sh
xrandr --listactivemonitor | awk 'NR==2 {print $3}'
```
or using process substitution command, like this:
```sh
awk 'NR==2 {print $3}' <(xrandr --listactivemonitor)
```

Now, if we want to display the second to last row, which is this line:
```sh
 0: +*eDP-1 1920/344x1080/194+1920+0  eDP-1
 1: +HDMI-2 1920/480x1080/270+0+0  HDMI-2
```

We can do that with this command:
```sh
xrandr --listactivemonitor | awk 'NR>=2'
```
or using process substitution command, like this:
```sh
awk 'NR>=2' <(xrandr --listactivemonitor)
```

Alright, that's all. Thanks for reading and happy shell scripting!

## Reference

- [Stackoverflow answer about awk for specific row](https://stackoverflow.com/a/31165844).
