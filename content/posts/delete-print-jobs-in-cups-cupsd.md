---
title: 'Delete print jobs in cups - cupsd'
date: 2005-06-15T16:49:00.000-07:00
draft: false
url: /2005/06/delete-print-jobs-in-cups-cupsd.html
---

To delete print jobs in cups in a Linux system you should use this command:  
  
\# lprm  
  
Or, to cancel all jobs:  
  
\# lprm -
# Archived Comments

#### If you have high CPU usage on your mac, and the cu...
[Anonymous]( "noreply@blogger.com") - <time datetime="2009-04-30T23:49:00.000-07:00">May 5, 2009</time>

If you have high CPU usage on your mac, and the culprit is the "socket" process, "lprm -" fixes it!  
  
From comments I read on the web and my own experience, it would appear that some print drivers installed on your windows VM can get a page stuck in the queue. How cupsd or the socket process gets involved is unknown to me.
<hr />
#### According to my experiences helps rather: # cancel...
[Unknown](https://www.blogger.com/profile/05213373920793626109 "noreply@blogger.com") - <time datetime="2010-02-04T23:42:07.670-08:00">Feb 5, 2010</time>

According to my experiences helps rather:  
\# cancel -a  
  
E.
<hr />
#### Which 2 of the following commands list all standin...
[Anonymous]( "noreply@blogger.com") - <time datetime="2010-04-23T02:21:10.065-07:00">Apr 5, 2010</time>

Which 2 of the following commands list all standing print jobs and their status
<hr />
#### lpstat -t
[Anonymous]( "noreply@blogger.com") - <time datetime="2011-11-21T07:39:55.440-08:00">Nov 1, 2011</time>

lpstat -t
<hr />
