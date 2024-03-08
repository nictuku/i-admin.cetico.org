---
title: 'Sometimes I miss doing Linux end user support work'
date: 2009-06-14T15:46:00.005-07:00
draft: false
url: /2009/06/sometimes-i-miss-doing-linux-end-user.html
tags: 
- ubuntu
- linux
- sysadmin
---

Last October I set an Ubuntu desktop for [Carla](http://whodsay.blogspot.com/)'s family. We live very far away from them, so it was important that their system was resilient, otherwise it wouldn't last long. They are all beginners in this internet thing, so installing Windows for them would have been a bad idea.  
  
And I was right. Things ran pretty smoothly, we talked via Skype very often, with no signs of virus or crashes. I even found this file in their system:  

> clica-aqui.exe (click-here.exe, in Portuguese)

  
Of course this was harmless - a Windows malware downloaded on Linux.  
  
After many months of usage everything was still working fine, until the computer stopped booting.  
  
Helping them from the phone was a challenge. They had to read the error messages in English for me (and they don't understand this language), while we tried to fix GRUB. In the end I assumed the hard disk was just dead.  
  
I asked my good friend [Marcelo Lemos](http://marcelolemos.blogspot.com/) for help and he burned an ISO image of Ubuntu and sent them by mail. I live in Switzerland and it would take ages for the package to get there. The customs in Brazil are a black hole.  
  
The CD got there after a couple days but, of course, since the worst case scenario always apply, the computer was not set to boot via CD-ROM, so we had to fix that - all by phone, while the girls spell each CMOS setup word for me.  
  
Now the Ubuntu Live CD system was finally up, I logged in via a reverse SSH connection (I gave them commands via Gmail that they would run via terminal) and fixed the filesystem errors. But the kernel still showed too many I/O errors from that hard disk. The only safe thing to do was to backup their remaining personal files somewhere while the system is still up, then reinstall everything with a new hard disk.  
  
I needed a safe place to send all their stuff but my amazing friends and former colleagues at [RimuHosting](http://rimuhosting.com/) do not offer the cheapest disk space plans, unfortunately. So I remembered that a RimuHosting customer once said great things about rsync.net (awesome name, by the way), and I gave them a try.  
  
I ordered a one-year 4Gb quote with them and my account was setup in a few minutes, even before I send them the payment, which is really awesome. I got all login details by mail, and started rsync'ing everything right away. Pretty neat. So it turns out that [rsync.net is a decent secure offsite backup service](http://rsync.net/) that I strongly recommend so far. They even have servers here in Zürich :-).  
  
The rsync is still running inside a screen(1) session as I write this, and it's going to take a while to finish. I just hope the disk is not too damaged and that it copies the most important files. Otherwise how would they live without the clica-aqui.exe?  
  
So much adrenaline! Only end-user support work gives me that. I miss that a little bit.  
  
  
  
  
  
NOT!