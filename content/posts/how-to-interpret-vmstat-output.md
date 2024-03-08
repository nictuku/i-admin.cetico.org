---
title: 'How to interpret vmstat output'
date: 2004-05-17T16:22:00.000-07:00
draft: false
url: /2004/05/how-to-interpret-vmstat-output.html
---

vmstat is a wonderful tool, whose output is a bit cryptic for the faint of the heart.  
  
![Image Hosted by ImageShack.us](http://img88.exs.cx/img88/7249/vmstat1.png)  
  
Proc  
\---  
r: Processes actually running, waiting for some attention from the CPU  
b: Uninterruptble sleeping processes (This I am yet to discover what does it mean)  
  
Memory:  
\---  
swpd: Virtual memory usage (swap areas are listed in /proc/swaps)  
free: Idle memory  
buff: Memory used as buffers, like before/after IO operations, I guess  
cache: Memory used as cache.  
  
Swap:  
\---  
si: Memory swapped in from the disk  
so: Memory swapped to the disk  
  
IO:  
\---  
bi: Blocks received from block device (like a hard disk)  
bo: Blocks sent to a block device  
  
System:  
\---  
in: The number of interrupts per second, including the clock.  
cs: The number of context switches per second.  
  
CPU:  
\---  
us: Time spent running non-kernel code. (user time, including nice time)  
sy: Time spent running kernel code. (system time - network, IO interrupts, etc)  
id: Time spent idle. Prior to Linux 2.5.41, this includes IO-wait time.  
wa: Time spent waiting for IO. Prior to Linux 2.5.41, shown as zero.  
  
  
You should get used to monitor this data, to see how is your system going during peak usage time.