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
# Archived Comments

#### Hi Admin,  
It's nice to new learner like me, but...
[Anonymous]( "noreply@blogger.com") - <time datetime="2008-07-26T07:06:00.000-07:00">Jul 6, 2008</time>

Hi Admin,  
It's nice to new learner like me, but still I do not understand few information like below mentioned synonym.  
mf  
pi  
po  
fr  
de  
sr  
m0  
m1  
m2  
m3  
m7  
in  
sy  
cs  
us  
When I did vmstat 5 5 i dot following details  
  
kthr memory page disk faults cpu  
r b w swap free re mf pi po fr de sr m0 m1 m3 m7 in sy cs us sy id  
0 0 0 13948448 1184032 59 178 363 44 47 0 6 2 1 0 1 805 7729 2584 7 3 90
<hr />
#### b is for blocked - process is waiting for in examp...
[Anonymous]( "noreply@blogger.com") - <time datetime="2009-04-24T05:54:00.000-07:00">Apr 5, 2009</time>

b is for blocked - process is waiting for in example write to disk.
<hr />
#### b is also known as "Wait Queue"
[SeanFromIT](https://www.blogger.com/profile/14319943010216197408 "noreply@blogger.com") - <time datetime="2011-01-11T10:42:05.534-08:00">Jan 2, 2011</time>

b is also known as "Wait Queue"
<hr />
#### check this URL for more info. http://perumal.org/a...
[chandranjoy](https://www.blogger.com/profile/00600672498776043775 "noreply@blogger.com") - <time datetime="2011-07-14T09:30:08.716-07:00">Jul 4, 2011</time>

check this URL for more info.  
http://perumal.org/analyzing-database-server-io-bottlenecks-using-iostat/  
  
  
Thanks,  
Jay  
chandranjoy@gmail.com  
http://innovationframes.com/phpbb
<hr />
#### Nice, thanks. Direct copy from the man pages witho...
[Anonymous]( "noreply@blogger.com") - <time datetime="2011-08-27T16:17:20.579-07:00">Aug 0, 2011</time>

Nice, thanks. Direct copy from the man pages without a clear explanation. Your a real gem.
<hr />
#### nice !
[Anonymous]( "noreply@blogger.com") - <time datetime="2013-03-12T23:43:14.769-07:00">Mar 3, 2013</time>

nice !
<hr />
