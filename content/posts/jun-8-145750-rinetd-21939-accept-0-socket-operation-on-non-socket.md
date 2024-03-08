---
title: 'Jun  8 14:57:50  rinetd[21939]: accept(0): Socket operation on non-socket'
date: 2005-06-08T11:03:00.000-07:00
draft: false
url: /2005/06/jun-8-145750-rinetd21939-accept0.html
---

For the record.  
  

> Jun 8 14:57:50 rinetd\[21939\]: accept(0): Socket operation on non-socket

  
  
I had this syslog message filling my hard disk due to a configuration problem of rinetd.  
  
I was setting it to list in a IP address which was not set to the network interfaces. I usually setup many IP's for external interfaces in a host. I had disabled a certain IP for some time (ip addr del ..) and rinetd got crazy.  
  
Fix: check what IP it is trying to listen in.  
\# /etc/init.d/rinetd stop  
  
\# tail -f /var/log/syslog |less  
  
in another screen:  
  
\# /etc/init.d/rinetd start  
  
You will see:  
  

> Jun 8 14:57:48 rinetd\[21939\]: couldn't bind to address port (Cannot assign requested address)  
> Jun 8 14:57:48 rinetd\[21939\]: Starting redirections...

  
  
Then just go and remove that reference in rinetd.conf and you're done.
# Archived Comments

#### ?
[Anonymous]( "noreply@blogger.com") - <time datetime="2009-10-30T11:41:47.981-07:00">Oct 5, 2009</time>

?
<hr />
#### Also saw this when the destination IP was an inval...
[the programmer](https://www.blogger.com/profile/05809305593192973601 "noreply@blogger.com") - <time datetime="2010-07-27T11:32:35.792-07:00">Jul 2, 2010</time>

Also saw this when the destination IP was an invalid IP address.. For example, I meant to use .254, but had this line instead:  
  
0.0.0.0 38254 10.10.10.255 80
<hr />
#### if already open port in the some port you put it i...
[SIFE](http://0xsife.wordpress.com "noreply@blogger.com") - <time datetime="2010-09-19T14:39:57.696-07:00">Sep 0, 2010</time>

if already open port in the some port you put it in your configuration ,a message like above will be appear in your logs .
<hr />
