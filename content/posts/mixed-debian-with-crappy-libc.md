---
title: 'Mixed Debian with crappy libc'
date: 2004-06-21T13:52:00.000-07:00
draft: false
url: /2004/06/mixed-debian-with-crappy-libc.html
---

There is something crappy with my libc development files.  
As I was forced to use backported version of Debian packages (something I won't recommend to anyone), I had some terrible problems that urged me to migrate to Fedora.  
  
There have been security updates in the 2 or three last kernel versions, and I am not being able to easily upgrade due to compilation time errors.  
  
After a make:  
  
CC arch/i386/kernel/process.o  
arch/i386/kernel/process.c:505: parse error before \`\*'  
arch/i386/kernel/process.c:506: warning: return-type defaults to \`int'  
arch/i386/kernel/process.c:506: conflicting types for \`\_\_switch\_to'  
include/asm/system.h:13: previous declaration of \`\_\_switch\_to'  
arch/i386/kernel/process.c: In function \`\_\_switch\_to':  
arch/i386/kernel/process.c:576: warning: return from incompatible pointer type  
make\[1\]: \*\*\* \[arch/i386/kernel/process.o\] Error 1  
make: \*\*\* \[arch/i386/kernel\] Error 2  
  
  
This is bad. I couldn't find the cause, although I didn't have time to further investigate. I am using Debian woody's libc6-dev, and have tried with many different gcc versions.  
  
Add to that a bunch of other reasons, and I am migrating to Fedora.
