---
title: 'Fixed my first bug in C++. PROFIT!'
date: 2009-06-09T17:22:00.004-07:00
draft: false
url: /2009/06/fixed-my-first-bug-in-c-profit.html
tags: 
- c++
- gnote
---

I'm still [trying to learn C++ by fixing bugs](http://i-admin.blogspot.com/2009/06/having-fan-while-trying-to-learn-c.html). Well, the bug I wanted to fix in Ekiga had already being solved upstream (should I be happy or sad?), but I had fun trying to debug it anyway.  
  
Luckily I found another one to fix today. After 1h30m playing around with gdb and adding debug messages everywhere, I fixed a [bug in Gnote where preferences were not being set unless you restarted the program](http://bugzilla.gnome.org/show_bug.cgi?id=582789).  
  
The biggest challenge was to find out that all code was written already, it just wasn't working. I initially thought I'd have to write the callback methods myself, but then I saw references to "gconf...notify" in the code, which would normally be enough.  
  
Reading the gconf API docs was sufficient for me to find the culprit. [2-liner patch submitted](http://git.gnome.org/cgit/gnote/commit/?id=22e48c6de1373c73f64196b29f7d1b274d1300b4) ;-).