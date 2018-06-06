---
date: "2018-06-06T21:40:00+02:00"
title: "How to install ArchLinux on Windows 10 (1803) with WSL"
authors: []
categories:
  - Linux
tags:
  - Linux
  - Windows
  - Archlinux
  - WSL
draft: false
---

Some days ago, I decided to install ArchLinx on [WSL](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux), for get a well-known Linux when I am on Windows and for the fun. It is a guide and my feedback about ArchLinux on Windows Subsystem for Linux (WSL). I inspired by the post of David Wood on his [website](https://davidtw.co/writings/2017/archlinux-on-the-windows-subsystem-for-linux) and I adapted for take account new version of Windows 10 and tools I used.

_Warning : ArchLinux is not officialy supported by WSL, some futures changes in WSL or LxRunOffline can make this guide out-of-date, and I cannot responsible for bad actions you can do._

# LxRunOffline installation

First step is to install [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline). This tool can create and manipulate WSL instance without used the Microsoft store and can used unsupported Linux distribution.

For do install, I prefer use [Chocolatey](https://chocolatey.org/) :

```batch
choco install -y lxrunoffline
```
With Chocolatey, LxRunOffline will add WSL if not already install on your Windows and do a little modification in Windows Registry. You have to reboot Windows after this step.

# Creation of ArchLinux's instance
After that, you have to download the ArchLinux bootstrap, that available on this [site](https://lxrunoffline.apphb.com/download/ArchLinux/) or in any ArchLinux mirror in iso/latest folder with the namearchlinux-bootstrap-{yyyy.mm.dd}-x86_64.tar.gz (with {yyyy.mm.dd} the creation date of the archive). In PowerShell (or cmd), you have to execute :

```batch
lxrunoffline install -n ArchLinux -f X:/where/is/archlinux-bootstrap-{yyyy.mm.dd}-x86_64.tar.gz -d D:/WSL/ArchLinux -r root.x86_64
```

* -n ArchLinux : define the name of WSL instance.
* -f X:/where/is/archlinux-bootstrap-{yyyy.mm.dd}-x86_64.tar.gz : define the archive used to generate the root file system.
* -d D:/WSL/ArchLinux : define the folder where the files instance will be saved.
* -r root.x86_64 : define the folder in archive with the root system files.

_Note : For the instance creation, I create a new archive based on ArchLinux bootstrap like said on David Wood's post. I discoved the -r option in the [LxRunOffline wiki](https://github.com/DDoSolitary/LxRunOffline/wiki) too late._

If, you got an error like this :

```batch
[ERROR] Couldn't open the file or directory "D:\WSL\ArchLinux\rootfs\usr\share\perl5\core_perl\pod\".
```

It is you have forgotten to do a restart after LxRunOffline installation. You have to remove the instance folder (D:\WSL\ArchLinux for example), reboot and retry.

# ArchLinux configuration

Since Windows 10 last update (1803), you have to do a first run of your instance in administration PowerShell. So, to run your ArchLinux's instance, you have to execute (first time as administrator and as normal user the other time) :

```batch
lxrunoffline run -n ArchLinux -w
```

* -n ArchLinux : define the instance name that we want to run
* -w : say, we want to use the home folder in WSL instance instead the current folder.

If you try to run the first time as a normal user, you will get this error :
```batch
Error : 0x80070005
```

You started in bash as root user.

Before to do the base installation, you have to choose an ArchLinux mirror (I get the first french mirror, in my case, but you can get any mirror).
```bash
echo 'Server = http://archlinux.de-labrusse.fr/$repo/os/$arch' >> /etc/pacman.d/mirrorlist 
```
Now, you have to initialize the ArchLinux keyring for package validation. You can get the keyring package directely from the [ArchLinux's website](https://www.archlinux.org/packages/core/any/archlinux-keyring/)) and do a click on _Download From Mirror_
```bash
pacman-key --init
pacman -U /mnt/X/where/is/archlinux-keyring-20180404-1-any.pkg.tar.xz
```
Now, you can install the base packages :
```bash
pacman -Syu base base-devel
```
And do the manipulations that you with your ArchLinux's instance (like change language, install packages, ...)

# Post installation specific details

## Fix fakeroot
The standard fakeroot install by ArchLinux does not work well in WSL, you have to install a special version if you want build ArchLinux package (from [AUR](https://aur.archlinux.org) for example).
On a real ArchLinux installation, you have to build [fakeroot-tcp](https://aur.archlinux.org/packages/fakeroot-tcp) package : 

```bash
git clone https://aur.archlinux.org/fakeroot-tcp.git
cd fakeroot-tcp
makepkg -s
```
And pick up the generated package (fakeroot-tcp-1.22-2-x86_64.pkg.tar.xz), then in your ArchLinux's instance, you have to install this package :

```bash
pacman -U /mnt/X/where/is/fakeroot-tcp-1.22-2-x86_64.pkg.tar.xz
```

## Change default user
When you have created a user, you can modify your ArchLinux's instance to start on this user instead root :
```batch
lxrunoffline config-uid -n ArchLinux -v {uid}
```
* -n ArchLinux : The name of your WSL instance to modify.
* -v {uid} : the default user id to set, you can get the {uid} with the `id` command.

_Note : For me, this modification is not awareness immediately and I have to close my Windows session._

## Creation of shortcut

I can read this [page](https://github.com/DDoSolitary/LxRunOffline/wiki/Creating-shortcuts-to-installations) of LxRunOffline's wiki about shortcut creation. David Wood provides an [icon](https://www.dropbox.com/sh/w3x7ajxwxig3up1/AAAnhLUctzTeAhshV7TJlqcZa?dl=0) for have a good-looking shortcut.

# TODO

The next step, for me, will be to see if we can remove some base packages and especially the linux and mkinitcpio packages. We do not need a Linux kernel in WSL and mkinitcpio generate errors on installation/update packages.
