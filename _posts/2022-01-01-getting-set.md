---
layout: post
title: "Getting set up"
date: 2022-01-01 11:23:58 -0800
---

# What you need for this course

All you need for this course is a computer and a Unix shell like Bash or ZSH. If you are on Linux or macOS, you don’t have to do anything special. Step by step instructions for installing this on Windows are below.

## How to install a Unix shell on Windows

If you are on Windows, you need to make sure you are not running cmd.exe or PowerShell; you can use Windows Subsystem for Linux or a Linux virtual machine to use Unix-style command-line tools.

For most of you, using Windows Subsystem for Linux is the easiest way to go, so if you're not sure, go ahead and follow the steps below to get that working.

1. Start PowerShell as an administrator: 
* Click on the start menu and search for "powershell"
* Once that pops up, right click on it and choose "Run as administrator"
2. Install WSL
* Type the following text into that command prompt: `wsl --install`
* This command should enable the required components and install a Linux distribution for you. Ubuntu will be installed by default, which is what you want for this course
3. Restart your computer
4. Open Ubuntu
* Click on the start menu and search for "ubuntu"; if it appears, then click on it to open it and move on to the next step
* If it doesn't appear, click on the start menu and search for "microsoft store", click on that and search for "ubuntu" in the microsoft store, and click install
5. Open ubuntu for the first time and set-up a user name and password
* Once ubuntu installs, click on it to open it for the first time
* When it opens, it will prompt you to create a user account and password for your newly installed Linux distribution; store your password in a safe place so you can remember it
6. Enable copy and paste on Ubuntu
* Open Ubuntu, right click anywhere in the Ubuntu window, and select "Properties", then [click the following option](https://devblogs.microsoft.com/commandline/copy-and-paste-arrives-for-linuxwsl-consoles/) to enable copy and paste


## Verifying you have the right thing installed

To make sure you’re running an appropriate shell, you can try the command `echo $SHELL`. If it says something like `/bin/bash` or `/usr/bin/zsh`, that means you’re running the right program.
