---
author: Ben
title: Speed Up SSH Connection
date: 2021-08-02
tags:
  - config
  - ssh
description: Speed Up SSH Connection
summary: When you need to work on remote environment, chances are high that you need to establish an SSH connection. Slow connection speed can be annoying especially you need to do it very often.
thumbnail: "/blog/images/ssh/speed-up-ssh-connection.png"
---

**Problem**

When you need to work on remote environment, chances are high that you need to establish an SSH connection. Slow connection speed can be annoying especially you need to do it very often.

**Solution**

There are 2 options in ssh_config manual that solve the above problem:

[ssh-config format is still not being supported by hugo yet.]: #
[nested dependencies for markdown syntax highlit in hugo: hugo > chroma > pygments (https://github.com/pygments/pygments/pull/1812)]: #

<!---

```ssh-config
Host your_hostname_alias

	HostName your_hostname
	User Ben
	# add the following options
	ControlMaster auto
	ControlPath `/tmp/%r@%h:%p
```
-->

{{< gist ganboonhong 4c3f4d546964457761033ddb9d388bb1 >}}

With the above configurations, the subsequent SSH connections have less overhead. <br>
(Your first connection must _stay connected_ for others to reuse the connection)

Here is the explaination of above options from ssh_config manual:

<blockquote>
<b>ControlMaster</b>

Enables the sharing of multiple sessions over a single network connection.  When set to yes, ssh(1) will listen for connections on a control socket specified using the ControlPath argument.  Additional sessions can connect to socket using the same ControlPath with ControlMaster set to no (the default).  These sessions will try to reuse the master instance's network connection rather than initiating new ones, but will fall back to connecting normally  the control socket does not exist, or is not listening.

Setting this to ask will cause ssh(1) to listen for control connections, but require confirmation using ssh-askpass(1).  If the ControlPath cannot be opened, ssh(1) will continue without connecting to a master instance.

X11 and ssh-agent(1) forwarding is supported over these multiplexed connections, however the display and agent forwarded will be the one belonging to the master connection i.e. it is not possible to forward multiple displays or agents.

Two additional options allow for opportunistic multiplexing: try to use a master connection but fall back to creating a new one if one does not already exist.  These options are: auto and autoask.  The latter requires confirmation like the ask option.

</blockquote>

<blockquote>
<b>ControlPath</b>

Specify the path to the control socket used for connection sharing as described in the ControlMaster section above or the string none to disable connection sharing.  Arguments to ControlPath may use the tilde syntax to refer to a user's home directory or the tokens described in the TOKENS section.  It is recommended that any ControlPath used for opportunistic connection sharing include at least %h, %p, and %r (or alternatively %C) and be placed in a directory that is not writable by other users.  This ensures that shared connections are uniquely identified.

</blockquote>
