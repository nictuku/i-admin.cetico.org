---
title: 'bpf_trace_printk as a last-resort method to debug eBPF programs'
date: 2018-10-15T18:12:00.000-07:00
draft: false
url: /2018/10/bpftraceprintk-as-last-resort-method-to.html
---

It's hard to debug problems in eBPF programs. When everything fails, there is a last-resort: use bpf\_trace\_printk.  
  
bpf\_trace\_printk can be used as such:  
  
p.p1 {margin: 0.0px 0.0px 0.0px 0.0px; font: 14.0px Menlo; color: #93c836; background-color: #272822} span.s1 {font-variant-ligatures: no-common-ligatures}  

bpf\_trace\_printk("fname %s\\\\n", valp->fname);

  
  
The double-escaped \\\\n is needed when the C source code is embedded in a Python multi-line string, which is the case for most bcc examples.  
  
You can use formatting directives like %s and %d, but you can only use one per line.  
  
To see the output, first run the bcc program as usual, then do this in a separate terminal:  
  

$ sudo cat /sys/kernel/debug/tracing/trace\_pipe

 foo-29323 \[002\] d... 12090253.569332: : fname /etc/ld.so.cache

foo-29323 \[002\] d... 12090253.569350: : fname /lib/x86\_64-linux-gnu/libpthread.so.0

foo-29323 \[002\] d... 12090253.569384: : fname /lib/x86\_64-linux-gnu/libc.so.6

foo-29323 \[002\] d... 12090253.570230: : fname /proc/sys/net/core/somaxconn

foo\-29323 \[002\] d... 12090253.571336: : fname /dev/null

  
Tail won't work. _cat_ will already take care of _streaming_ the output for you as it appears.  
  
This is a low-level debugging method but it's not meant for general use. Finally, only one reader of the _trace\_pipe_ is allowed at a time.  
p.p1 {margin: 0.0px 0.0px 0.0px 0.0px; font: 14.0px Menlo; color: #ffffff; background-color: #272822} span.s1 {font-variant-ligatures: no-common-ligatures}
