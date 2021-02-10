---
author: Robertus Diawan Chris
title: "Linux Ownership and Permissions"
date: 2021-02-06T13:56:01+07:00
tags: [Linux, English]
ShowToc: true
---

> How to change ownership and permission on linux (and probably other unix-like system? i'm not sure). Plain boring and important.

## A Brief Intro

File/directory ownership is basically who owns the file/directory, for example, if we create a file/directory as admin or root in this case and if we want to edit those file/directory as user then we need to change the file/directory ownership because that file/directory owned by the root and we have no access to change that file/directory.

To check the ownership and permission you can use `ls -l`, to make it simpler, `-l` means long format and the file ownership on column 3 (username) or 4 (group). Below is the example output when you run `ls -l` command:

![ls -l](ls-l.png)

As you can see from example above, most of the files owned by bruhtus user and only one file owned by root (the admin) so if we want to change that file then we need to change the ownership or access root privilege everytime we want to change that file (which is such a pain). Your choice.

There're 10 character on the most left side or the first column of the `ls -l` result, let's take a look on `nganu` file as an example. On the most left side of `nganu` there're this squence `-rw-r--r--`. The `-` means that there's no permission for that file or directory, or if it on the most left then it means it's not directory (or folder). So if `nganu` is directory rather than a file then the squence gonna be like this `drwxr-xr-x`.

`r` means that the file or directory has read permission (we can take a look at it), `w` means that the file or directory has write permission (we can edit the file), and lastly is `x` which means that the file or directory is executable (we can run the file). Usually if we make a folder then it's already have executable permission.

## How To Change Ownership

Change ownership is quite simple, the format is like this

```sh
chown user:group <file>
```

if you want to change ownership from or to root (or admin) then you need to access root privilege. Take the example from the image above, if i want to change ownership of file `nganu` from root to bruhtus user, then what i need to do is

```sh
sudo chown bruhtus:bruhtus nganu
```

Please keep in mind that the user is on column three and the group is on column four on `ls -l` result if you're unsure about the user and the group. `chown` command is not only for file but also for directory (or folder).

If you want to change all the files within the directory (not only the directory itself), then you can do

```sh
chown -R user:group <directory>
```

For example, let's assume that `nganu` is a directory with a file `anu` and owned by root like this

![ownership directory](ownership-directory.png)

If we want to change ownership of the `nganu` directory and `anu` file from root to bruhtus user, then what we need to do is

```sh
sudo chown -R bruhtus:bruhtus nganu
```

Please keep in mind that we need `sudo` because the previous owner is root (or admin), if the previous owner isn't root then we don't need `sudo`.

## How To Change Permission

The permission section has 10 character like i explained above, here's the breakdown of those permission:

Directory | Owner | Group | Others
---|---|---|---
`d` or `-` | `rwx` or `---` | `rwx` or `---` | `rwx` or `---`

- `r` or read is worth 4 points
- `w` or write is worth 2 points
- `x` or executable is worth 1 points

So if you have read, write, and execute permission of the owner, group, and others then it's gonna be `777`. Here's the example:

![permission example](permission-example.png)

file `anu` only has read and write permission for the owner, and only read permission for group and others (or everyone else), in this case it's worth `644`. Why is this important? because we can use that to change file or directory permission precisely.

So if you want to change anyfile to have the same arrangement as file `anu` then you can do

```sh
chmod 644 <file>
```

if we want to change those file to have execute permission on owner, group, and others then we can do this

```sh
chmod 755 <file>
```

or if you want everyone to have the same permission you can use `+`, like this one (have execute permission on owner, group, and others)

```sh
chmod +x <file>
```

or you want to specify one of them (either owner/user, group, or others) you can do one of this

```sh
chmod u+x <file> #execute permission only for user/owner
chmod g+x <file> #execute permission only for group
chmod o+x <file> #execute permission only for others
chmod ug+x <file> #execute permission only for user/owner and group
chmod a+x <file> #similar to +x, give user/owner, group, and others execute permission
```

You can also change permission recursively like change ownership, by doing this

```sh
chmod -R u+x <directory>
```

or

```sh
chmod u+x -R <directory>
```

## The Conclusion

That's all about changing ownership and permission on linux. Hopefully that's useful (probably not, optimistic).

## References

- [DistroTube Video](https://www.youtube.com/watch?v=fguV653wfqY&ab_channel=DistroTube).
