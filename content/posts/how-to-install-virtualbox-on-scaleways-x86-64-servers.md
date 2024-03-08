---
title: 'How to install VirtualBox on Scaleway''s x86_64 servers'
date: 2016-08-13T18:48:00.000-07:00
draft: false
url: /2016/08/how-to-install-virtualbox-on-scaleways.html
---

Scaleway offers reasonably priced dedicated servers that are now even cheaper than Hetzner's robo market.  
  
I wanted to use them for doing Ansible tests using Vagrant. The problem is you can't easily install VirtualBox there, and it's needed for Vagrant to work.  
  
Here's a script that should do most of the work for you:  
  

* * *

```
#!/bin/bash

# Expects Ubuntu 16.06 (xenial) and kernel 4.x.
# Based upon a blog post by Zach at http://zachzimm.com/blog/?p=191

set -eux

# Have the user call sudo early so the credentials is valid later on
sudo whoami 


for x in xenial xenial-security xenial-updates; do 
  egrep -qe "deb-src.\* $x " /etc/apt/sources.list || echo "deb-src http://archive.ubuntu.com/ubuntu ${x} main universe" | sudo tee -a /etc/apt/sources.list
done

echo "deb http://download.virtualbox.org/virtualbox/debian xenial contrib" | sudo tee -a /etc/apt/sources.list.d/virtualbox.list
sudo apt update
sudo apt-get install dkms virtualbox-5.0 -y

KERN\_VERSION=$(uname -r |cut -d'-' -f1)
EXTENDED\_VERSION=$(uname -r |cut -d'-' -f2-)
cd /var/tmp
wget https://www.kernel.org/pub/linux/kernel/v4.x/linux-${KERN\_VERSION}.tar.xz
tar xf linux-${KERN\_VERSION}.tar.xz -C /var/tmp/
KERN\_DIR="/var/tmp/linux-${KERN\_VERSION}"
cd "${KERN\_DIR}"
zcat /proc/config.gz > .config

# Fetch the tools necessary to build the kernel. Using generic because there may not be a package for our $KERN\_VERSION.
sudo apt-get build-dep linux-image-generic -y

NUM\_CORES=$(cat /proc/cpuinfo|grep vendor\_id|wc -l)

make -j${NUM\_CORES} oldconfig include modules

sudo -E /sbin/rcvboxdrv setup

# Check that it's fine.
VBoxManage --version


```

* * *

I hope it works for you. It worked for me. See also the gist [here](https://gist.github.com/nictuku/13afc808571e742d3b1aaa0310ee8a8d). You can download it directly from your box:  
  
  
```
$ wget https://gist.githubusercontent.com/nictuku/13afc808571e742d3b1aaa0310ee8a8d/raw/d41ac5c7e1eb0c030a67b7bdaab69981217f46ed/scaleway-virtualbox-module-build.sh

```Let me know how it goes. Have fun!
# Archived Comments

