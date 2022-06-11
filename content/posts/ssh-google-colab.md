---
author: Robertus Diawan Chris
title: "Access Google Colab Through SSH and SSHFS"
date: 2021-06-22T09:32:28+07:00
tags: [Linux]
ShowToc: true
---

> Have you ever want a terminal emulator in google colab instead of jupyter notebook? Well, here it is bois!

## Skip-able Part

First thing first, i prefer to use vim/neovim instead of jupyter notebook and that's why i prefer to use terminal or terminal emulator to do things as much as possible. I know jupyter notebook has it's own vim emulation, but it's not as good as the OG vim/neovim. Also, i know you can have neovim inside your browser using [firenvim plugin](https://github.com/glacambre/firenvim), but i'm not sure how useful it is and do i really want that? i mean, my IDE is a terminal or terminal emulator. So, unless it's a terminal or terminal emulator then i won't feel comfortable using it.

That's why i'm trying to figure out how to access google colab through terminal emulator, and it turns out i can do that!

> As far as i know, google colab support terminal emulator but only for pro user. I haven't try it yet, so i might be wrong.

## Requirements

To make this work, please make sure that SSH and SSHFS installed on your system. You can install both of them using your distro package manager or something similar (there are a lot of tutorial on how to install SSH or SSHFS).

## A Brief Intro to SSH

According to [wikipedia](https://en.wikipedia.org/wiki/Secure_Shell), Secure Shell (SSH) is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line login and remote command execution, but any network service can be secured with SSH.

To make it simple, SSH let you access another computer/server and you can use command line interface (CLI) to interact with those computer/server.

## A Brief Intro to SSHFS

According to [the project github repo](https://github.com/libfuse/sshfs), SSHFS allows you to mount a remote filesystem using SFTP.

To make it simple, SSHFS let you mount the computer/server storage to your local system and you can treat it like the usual storage on your local system, like using `mv` and `cp` command.

## Access Google Colab Using SSH

First thing first, you need to have ngrok account. If you doesn't have ngrok account, then you can register [here](https://dashboard.ngrok.com/signup).

After that, you need to make a new google colab notebook. And then, paste the code below:
```sh
#CODE

#Generate root password
import random, string
password = ''.join(random.choice(string.ascii_letters + string.digits) for i in range(20))

#Download ngrok
! wget -q -c -nc https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
! unzip -qq -n ngrok-stable-linux-amd64.zip
#Setup sshd
! apt-get install -qq -o=Dpkg::Use-Pty=0 openssh-server pwgen > /dev/null
#Set root password
! echo root:$password | chpasswd
! mkdir -p /var/run/sshd
! echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
! echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config
! echo "LD_LIBRARY_PATH=/usr/lib64-nvidia" >> /root/.bashrc
! echo "export LD_LIBRARY_PATH" >> /root/.bashrc

#Run sshd
get_ipython().system_raw('/usr/sbin/sshd -D &')

#Ask token
print("Copy authtoken from https://dashboard.ngrok.com/auth")
import getpass
authtoken = getpass.getpass()

#Create tunnel
get_ipython().system_raw('./ngrok authtoken $authtoken && ./ngrok tcp 22 &')
#Print root password
print("Root password: {}".format(password))
#Get public address
! curl -s http://localhost:4040/api/tunnels | python3 -c \
	"import sys, json; print(json.load(sys.stdin)['tunnels'][0]['public_url'])"
```

What that code does is basically create a connection from google colab back-end with ngrok so that we can access the google colab back-end using SSH and SSHFS. You can check the [github gist](https://gist.github.com/yashkumaratri/204755a85977586cebbb58dc971496da#file-google-colab-ssh) for more detail.

After that, you can decide whether to mount your google drive to google colab to or not. If you decide to mount your google drive to google colab, then later on you can mount your google drive using SSHFS.

After you setup all of that, run the code. After you run the code, it gonna request you to copy your ngrok authtoken from [ngrok dashboard](https://dashboard.ngrok.com/auth). If there's an error after you input ngrok authtoken, you can just re-run the code.

After that, you can see the Root password and something similar to `tcp://n.tcp.ngrok.io:xxxxx` (which `n` and `x` represent any number from 0-9)

Next, you can access the google colab using SSH with the following command:
```sh
ssh -p xxxxx root@n.tcp.ngrok.io
```

If there's a question "Are you sure you want to continue connecting?", answer yes. That was to save the SSH address so that you can connect to it again in the future. But, if you use google google colab then you need to make new SSH address because each SSH session restart when google colab restart. That is one of the downside for this method.

## Access Google Drive in Google Colab

If you mount google drive before you activate google colab, then you can access google drive too. You can access google drive in google colab using SSHFS.

If you can already access google colab back-end using SSH, then all you need too do is to run this command:
```sh
sshfs -p xxxxx root@n.tcp.ngrok.io:/content/drive/MyDrive /mnt/colab
```

The command is almost the same as SSH command from before, the difference is that you need to specify which directory you want to mount on the google colab (in above example is google drive which has path `/content/drive/MyDrive`) and which directory you want to mount it on the local system (in above example is on `/mnt/colab`).

You can choose to mount google drive on any other directory you want, even on your home directory. I usually mount it on `/mnt` because it's already a habit of mine to mount an external drive to either `/mnt` or `/run/media`. And also, make sure the directory you want to mount your drive into is already exist. I already create `colab` directory beforehand, so i can mount the google drive into `/mnt/colab`.

## Upside and Downside

There's always an upside and downside of things, and this method is also the same. Below is a few upside and downside that i notice when using this method.

### The Upside

- You can use it like the usual terminal or terminal emulator, with familiar command and vim/neovim in it.
- If you use SSHFS, you can update your script directly from your terminal emulator to google colab through google drive. Basically you can update your script in google drive directly from your terminal emulator.

### The Downside

- SSH connection with ngrok only last around 40 minutes, so if you need a long time to run your code then you should use the google colab front-end instead. Use SSH and SSHFS to update script instead of running the script.
- If you have slow internet connection, then it's gonna be a bit of lag when you interact with google colab back-end CLI.
- SSH session restart when the google colab restart, and SSH session only last for around 40 minutes so you might need to start over again.

## Conclusion

When you see that the method has a lot more downside rather than upside, you might be thinking "why should i use this method?". Well, only use this method if you feel more comfortable using terminal emulator rather than jupyter notebook, other than that, it's not really worth your time.

Use this method while it lasts, as far as i know, there was a time when this method doesn't work. At the time of writing this post, this method still works but i'm not sure if google gonna let this happen for a long time. Anyway, updating the script through SSHFS is really convenient for me.

## References

- [Google colab SSH github gist](https://gist.github.com/yashkumaratri/204755a85977586cebbb58dc971496da#file-google-colab-ssh).
- [How to SSH into google colab medium post](https://medium.com/@meet-minimalist/how-to-ssh-into-google-colab-and-run-scripts-from-terminal-instead-of-jupyter-notebook-3931f2674258).
- [Access your google drive using ssh medium post](https://medium.com/@bikcrum/connect-your-google-drive-using-ssh-103faaba4ba7).
- [Distrotube youtube video on SSHFS](https://www.youtube.com/watch?v=-0jyrvMl0Ic).
