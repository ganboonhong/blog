---
author: Ben
title: Why SSH PasswordAuthentication option doesn't work
date: 2020-04-05
tags:
    - auth
    - ssh
description: Why SSH PasswordAuthentication option doesn't work
---

![](../../images/ssh-password-auth.webp)

After reading the SSH manual,  I enabled the PasswordAuthentication in the **/etc/ssh/ssh_config** on my server.

I tried to establish a SSH connection to my server with the command `ssh username@hosturl.com`.

I got this error message: `username@hosturl.com: Permission denied (publickey)`.

Apparently, my ssh client was trying to use public key to establish the connection instead of prompting for password. I couldn't find out why the option doesn't work as expected until I tried to list the **/etc/ssh/** directory again. There's another file named **/etc/ssh/sshd_config**. One question immediately appear in my mind: what's the difference between **ssh_config** and **sshd_config**?

This [blog](https://prasadlinuxblog.wordpress.com/2012/09/13/what-is-the-difference-between-ssh_config-and-sshd_config/) provides a good explanation. In short:

-   ssh_config is configuration for SSH client on that server
-   sshd_config is configuration for incoming connection request

So, I updated the connection on the correct configuration file and it worked as expected. I successfully established a connection via password and username from my laptop.
