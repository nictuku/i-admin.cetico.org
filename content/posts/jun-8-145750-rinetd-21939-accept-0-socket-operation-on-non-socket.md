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