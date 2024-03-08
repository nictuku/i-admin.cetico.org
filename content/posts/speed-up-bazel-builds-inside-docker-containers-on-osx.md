---
title: 'Speed up Bazel builds inside docker containers on OSX'
date: 2017-11-27T22:35:00.004-08:00
draft: false
url: /2017/11/speed-up-bazel-builds-inside-docker.html
---

I've been using Bazel to developing on a Mac.  
  
Occasionally I need to test docker images that are built with the amazing [rules\_docker](https://github.com/bazelbuild/rules_docker). But running anything inside docker on OSX is really painfully slow. And Bazel builds need to read a ton of files, so things can take forever.  
  
I don't think there's a definitive answer for that other than avoiding the combination of OSX+docker, but I found that helped:  

Crank up Docker's own resource limits
-------------------------------------

Click on the little whale sitting on your OSX status bar, and go to _Preferences_, _Advanced_ and crank that up as much as you want. I've given it the maximum but I understand my browser navigation can be affected. YMMV.  
  

[![](https://4.bp.blogspot.com/-U97bKS8Nk9M/Wh0A4sISPtI/AAAAAAAB38M/bJQnrYnTq3YBlB6q_cixWGA9i5Xbbth5gCLcBGAs/s320/docker-resources.png)](https://4.bp.blogspot.com/-U97bKS8Nk9M/Wh0A4sISPtI/AAAAAAAB38M/bJQnrYnTq3YBlB6q_cixWGA9i5Xbbth5gCLcBGAs/s1600/docker-resources.png)

Use a cache directory for Bazel builds inside docker 
-----------------------------------------------------

Deep down, what's really slow is docker going through OSX's Linux virtualization to do volume I/O. We can't currently speed up the IO, but we can reduce the amount of work that Bazel has to do every time it builds. We can cache things.  
  
Normally, every container will have its own ephemeral filesystem namespace. You lose everything Bazel worked hard for as soon as the container exits. It can take quite a while for Bazel to load and compile all those external packages again next time you bring it up. So use caching.  
  
The best part is: Bazel's caching **really works**. It's actually _hard_ to invalidate the build cache (unlike Dockerfile layers, in my experience).  
  
Mount or bind a volume from the host into _/cache_ and use --output\_user\_root=_/cache_ to persist everything. You can add that flag to your container user's $HOME/.bazelrc (probably /root/.bazelrc) or to the container's _/etc/bazel.bazelrc_.  
  
If your entrypoint to the container is a bash script you can do something like:  

> cache\_dir="/cache"  
> if \[\[ ! -f "$HOME/.bazelrc" \]\]; then  
>   cat < "$HOME/.bazelrc"  
> startup --output\_user\_root=${cache\_dir}  
> startup --batch  
> query --show\_timestamps  
> build --show\_timestamps  
> EOF  
> fi

Just remember to mount the /cache directory first.  

Inspect what Bazel is doing to your filesystem 
-----------------------------------------------

.. and scream in horror when you see it's trying to pull the whole internet when it builds your system for the first time. :-)  
  
I use _[fswatch](https://stackoverflow.com/questions/1515730/is-there-a-command-like-watch-or-inotifywait-on-the-mac)_ to monitor the cache directory from outside the container:  
  
$ fswatch -v -r ~/cache/  
/Users/yves/cache/b602195e65b171abc776e092e3980149/bazel-sandbox/4465485924138705607/execroot/\_\_main\_\_/tmp/go-build178822492/runtime/internal/atomic/\_obj/asm\_amd64.o  
/Users/yves/cache/b602195e65b171abc776e092e3980149/bazel-sandbox/4465485924138705607/execroot/\_\_main\_\_/tmp/go-build178822492/runtime/internal/atomic/\_obj  
/Users/yves/cache/b602195e65b171abc776e092e3980149/bazel-sandbox/4465485924138705607/execroot/\_\_main\_\_/tmp/go-build178822492/runtime/\_obj  

Trust the Bazel
---------------

Bazel is engineered for speed. It has a bit of a learning curve but once you go through with it, Bazel is fast.  
  
First time builds do take a while. But if your cache is setup properly, even very large targets should build quickly in between small code changes. That's the magic of Bazel. It makes small changes very quick to build. And it makes me happy to code again.  
  

[![](https://4.bp.blogspot.com/-4bjFwJQ72EM/Wh0CkmtU5-I/AAAAAAAB38Y/S02m5yWDxzQu4N0K2MJR2sx8HT0Fe28XgCLcBGAs/s1600/bazel-icon.png)](https://4.bp.blogspot.com/-4bjFwJQ72EM/Wh0CkmtU5-I/AAAAAAAB38Y/S02m5yWDxzQu4N0K2MJR2sx8HT0Fe28XgCLcBGAs/s1600/bazel-icon.png)

Bazel is Love

  
\- Yves [@cetico](https://twitter.com/cetico)
