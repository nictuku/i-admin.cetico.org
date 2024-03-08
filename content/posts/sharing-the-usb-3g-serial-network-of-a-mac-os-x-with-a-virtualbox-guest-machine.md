---
title: 'Sharing the USB 3G serial network of a Mac OS X with a VirtualBox guest machine'
date: 2008-11-30T07:22:00.001-08:00
draft: false
url: /2008/11/sharing-usb-3g-serial-network-of-mac-os.html
---

When configuring networking on VirtualBox for Mac OS X, I got stuck because it didn't give me the option to use the "Host Interface" option with my ppp0 connection. The ppp0 connection is my "dial-up" Internet connection using a 3G USB modem.  
  
The solution is so obvious, that I'm surprised I couldn't think of it before finding [this post](http://forums.virtualbox.org/viewtopic.php?p=37658&sid=485edee6410389582ef8d79e0419e280) on the VirtualBox forum: just use the (amazing) Internet sharing option of the Mac.  
  
\- First, configure VirtualBox to use "Host Interface", and pick the "Ethernet" device down below.  
\- Then, Boot your guest OS (for example, [Debian Live](http://debian-live.alioth.debian.org/), or [HaikuOS](http://haiku-os.org/)) and use DHCP to configure the network.  
\- Finally, configure Internet sharing on the Mac OS X. Just share the "connection from: " .. "To computers using: Ethernet". Wait a few seconds, then force a reload of the DHCP configuration on the guest OS, and it's done!  
  
Now, the trick is how to make the network work on HaikuOS :-).