---
title: 'Solution for Web admin interface for cups in Ubuntu not working'
date: 2004-12-17T09:08:00.000-08:00
draft: false
url: /2004/12/solution-for-web-admin-interface-for.html
---

I had troubles making the web interface in ubuntu 4.10 work.  
  
Whatever I set in cupsd.conf, I had an access denied message:  
  

> UnauthorizedAdministrative commands are disabled in the web interface for  
> security reasons. Please use the GNOME CUPS manager (Computer > System  
> configuration > Printing).

  
  
In /var/log/auth.log the following message appeared:  
  

> Dez 17 13:05:34 localhost cupsd: (pam\_unix) authentication failure; logname=  
> uid=0 euid=0 tty= ruser= rhost= user=nictuku

  
  
Also, in /var/log/cups/error\_log I had:  
  

> E \[17/Dec/2004:13:05:50 -0200\] IsAuthorized: pam\_authenticate() returned 7  
> (Authentication failure)!  

  
The solution is quite simple. Just change the /etc/cups/cupsd.conf and set the daemon user to root: Put "User root" in the file. Then restart cupsys.  
  
Obviously this is a security no-no, but one should have the freedom to do whatever he wants, right? :)  
  
Seriously, Ubuntu disabled that for a reason. It's bad to log in as root using plain http authentication. In some cases, though, the web interface is required.  
  
In my home server I don't run X, so I can't use the gnome cups control panel. Also, in general network servers, you could use an ssh tunnel to login to the cups interface. In that case, I suggest you to keep 127.0.0.1 as the only listening interface, for better security.