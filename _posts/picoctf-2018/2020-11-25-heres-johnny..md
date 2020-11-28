---
title: PicoCTF 2018 - HEEEEEEERE'S Johnny!
author: AlphaChaos
date: 2020-11-25 02:15:00 +0000
categories: [PicoCTF_2018]
tags: [basics, picoctf, cracking, beginner, password, shadow file, john, tutorial]     # TAG names should always be lowercase
---

### Challenge Description

> Okay, so we found some important looking files on a linux computer. Maybe they can be used to get a password to the process. Connect with nc 2018shell.picoctf.com 5221. Files can be found here: [passwd](../../assets/challs/picoctf2018/passwd) [shadow](../../assets/challs/picoctf2018/shadow).

### Hint

> If at first you don't succeed, try, try again. And again. And again.  
> If you're not careful these kind of problems can really "rockyou".

### Challenge Analysis

So let's look at what information we are given in the challenge.  

> This challenge is more of a cracking challenge than crypto but it's listed under Crypto for PicoCTF so let's have a look at it.

Firstly, we're given connection information to allow us to connect to the remote server hosting the challenge. These connection strings often take the format `nc server_address port_number` and are very common in most CTF events so it's worth getting familiar with them. The `nc` refers to the netcat tool, which is installed on most linux based systems by default and can be installed on Windows or Mac if needed. We can simply type the connection string on our terminal and we should connect to the remote server.

Let's try that now and see what's happening on the server. The server is prompting us for a username and password, so we need to look at the files that were provided as part of the challenge.

```terminal
$ nc 2018shell.picoctf.com 5221
Username: alphachaos
Password: ?
Failed Login!
```

The challenge mentions that these are important looking linux files and then gives us the two files, `passwd` and `shadow`. If you don't immediately recognise these file names then you'll need to do a bit of searching to see that they are part of how users and protected passwords are stored on linux systems.

The last piece of information for this challenge comes from the hint, that refers to trying again and again and also cryptically mentions the word "rockyou". In this case the reference to try and try again is a hint that we need to brute force the possible passwords, and `rockyou` is the name of one of the most famous password lists, and is included on popular security distros such as Kali or Parrot.

In this instance the actual challenge title is another subtle hint. `John (the ripper)` is the name of a very popular password cracking tool, `HEEEEEEERE'S Johnny!` is likely a reference to the John tool.  

> **Hint:** Good challenge creators often leave subtle hints or clues in the challenge title or description or file names. So every bit of information in the challenge should be examined for clues to help you figure out what you're trying to achieve.

```commmon
Summary
----------------------------------------------
connection = nc 2018shell.picoctf.com 5221
required: username - password
files: passwd, shadow
clues: John, bruteforce
```

### Challenge Solution

Everything in this challenge is pointing towards trying to brute force the protected passwords in the shadow file. Quickly download and open each file so you can see the structure of the files and what details are contained within them.

```terminal
$ cat passwd
root:x:0:0:root:/root:/bin/bash

$ cat shadow
root:$6$IGI9prWh$ZHToiAnzeD1Swp.zQzJ/Gv.iViy39EmjVsg3nsZlfejvrAjhmp5jY.1N6aRbjFJVQX8hHmTh7Oly3NzogaH8c1:17770:0:99999:7:::

```

From the shadow file we can see the hashed password for the user root. So now we just need to find some way of figuring out what the original password was. We've been told to use the rockyou wordlist and we can use the tool john to try bruteforce the passwords for us.

If you are using Kali or parrot linux Os then John is already installed otherwise you'll likely have to install it. For me on Ubuntu it's just `sudo apt  install john`. Once installed we can get john to try and bruteforce our passwords as shown below.

> Note: you can download rockyou.txt.gz from [here](https://downloads.skullsecurity.org/passwords/rockyou.txt.bz2), if you’re not using Kali Linux. (you'll need to extract the file after you download it.)

```terminal
$ unshadow passwd shadow > /tmp/crack.password.db
$ john --wordlist=rockyou.txt /tmp/crack.password.db
$ john --show /tmp/crack.password.db
root:password1:0:0:root:/root:/bin/bash

1 password hash cracked, 0 left
```

> Note:  Your final password or flag might be different to what I've shown as PicoCTf often use dynamic challenges that are slightly different per user.

Once John recovers our password we can then reconnect back to the remote server and enter the `username:root` and `password:password1` to claim our flag.

```terminal
$ nc 2018shell.picoctf.com 5221
Username: root
Password: password1
picoCTF{J0hn_1$_R1pp3d_289677b5}

```

### Flag

`picoCTF{J0hn_1$_R1pp3d_289677b5}`

### Resources

[Beginners Guide for John The Ripper](https://www.hackingarticles.in/beginner-guide-john-the-ripper-part-1/)
