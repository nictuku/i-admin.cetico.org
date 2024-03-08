---
title: 'linux group passwords'
date: 2007-06-18T12:45:00.000-07:00
draft: false
url: /2007/06/linux-group-passwords.html
---

Group passwords is a sort of obscure feature most people don't use. It is safer \*not\* to have passwords set for groups.

  

By setting a password to a group, you allow an arbitrary users to be part of that group as long as they know the password. That generally doesn't make any sense unless you need to grant a shell user temporary access to a certain group. I don't even know if it works as advertised.

  

If you see this at webmin for example, you can safely ignore this option. For more information, see:

  

[http://tldp.org/HOWTO/Shadow-Password-HOWTO-7.html](http://tldp.org/HOWTO/Shadow-Password-HOWTO-7.html)
# Archived Comments

#### You can use "gpasswd" for assigning grou...
[Unknown](https://www.blogger.com/profile/12439686807839605824 "noreply@blogger.com") - <time datetime="2012-11-30T01:14:16.586-08:00">Nov 5, 2012</time>

You can use "gpasswd" for assigning group password,more detail use man page "man gpasswd".
<hr />
