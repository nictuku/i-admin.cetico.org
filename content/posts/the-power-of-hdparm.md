---
title: 'The power of hdparm'
date: 2004-05-17T16:18:00.000-07:00
draft: false
url: /2004/05/power-of-hdparm.html
---

This shows the power of a well set hard disk controller:  
  
BEFORE:  
johnny:~# hdparm -tT /dev/hda  
  
/dev/hda:  
Timing buffer-cache reads: 128 MB in 0.58 seconds =220.69 MB/sec  
Timing buffered disk reads: 64 MB in 8.89 seconds = 7.20 MB/sec  
  
AFTER:  
/dev/hda:  
Timing buffer-cache reads: 128 MB in 0.60 seconds =212.66 MB/sec  
Timing buffered disk reads: 64 MB in 1.57 seconds = 40.72 MB/sec  
  
PS: The second test was made when the machine was under production. So the benchmark is underrated.