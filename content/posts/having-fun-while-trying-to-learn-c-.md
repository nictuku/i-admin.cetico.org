---
title: 'Having fun while trying to learn C++'
date: 2009-06-08T17:16:00.004-07:00
draft: false
url: /2009/06/having-fan-while-trying-to-learn-c.html
tags: 
- cpp
- c++
- ubuntu
- ekiga
---

I've been trying to learn C++ lately. I read a few chapters of a few books, but I got tired of just reading so now I am trying to fix bugs in free software out there.  
  
The first step was to install Ubuntu on the Macbook Pro that I use. Second step was to find useful software written in C++ that needs small bugs fixing.  
  
First I tried to fix a [bug in gnote](http://bugzilla.gnome.org/show_bug.cgi?id=581080), but I wasn't persistent enough and the lead developer fixed the bug himself after many days without any update from me. The lesson learned here is **do not just propose a fix a go walkabout. Stick to it until the end.**  
  
Now I'm trying to fix an bug in Ekiga. Actually, I think the bug is in the libopal, but I'm not so sure. The symptom is a segmentation fault in SIPHandler::SendRequest() and it only crashes when I set an outbound SIP proxy. Maybe I'm setting an invalid proxy, but well, it shouldn't be crashing.  
  
Let's see how far I'll go this time.