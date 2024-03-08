---
title: 'Caveats about Linux connection tracking and high traffic servers'
date: 2014-02-09T12:23:00.002-08:00
draft: false
url: /2014/02/caveats-about-linux-connection-tracking.html
---

  
Dear Internet, whenever you're setting up a high-performance TCP or specially a UDP server on Linux, don't be stupid like me, and do **remember to pay attention to connection tracking on your server**.

### What is connection tracking?

Connection tracking is normally used by Linux for [certain firewall rules](http://en.wikipedia.org/wiki/Netfilter#Connection_tracking), like those that depend on connection state such as NEW, ESTABLISHED, RELATED, etc. 

  

Even UDP connection can have pseudo-state tracked by Linux.

  

Connection tracking is enabled by default. Even if a system has no netfilter rules configured to use conntrack, Linux keeps a large table of connection states in memory. I assume it tracks connections even if no stateful firewall rules exist because a user would expect that new firewall rules should apply immediately. 

  

In general, the connection tracking is extremely efficient and performs very well. But if you find that it's consuming too much resources, specially in a very constrained system, or if you don't want to think about the limit of connections, you can disable connection tracking or maybe use a dedicated Linux box as your NAT machine for stateful firewall rules. 

  

### How do you tell when connection tracking is hurting you?

#### Can connection tracking increase latency? (TL;DR most likely, no)

I run a high throughput DHT node that handles thousands of packets per second, and I do it in a very old single core machine with an ancient Athlon(tm) 64 Processor 3700+.

  

I use a crappy machine in part because it's cheaper (from one of those brilliant hetzner.de auctions), but also because it makes it easier to spot scalability problems this way. Well, case in point, my DHT supernode needs to handle a large number of of tiny little packets every second. So I wondered, wouldn't the conntrack additional work from the system to handle those packets will slow their processing?

  

Since my system is CPU bound, I believe a decent test is to try running the supernode with and without conntrack, then compare the throughput vs. CPU usage. 

  

I disabled conntracking and inspected my throughput again, there was no significant change. The connection tracking itself didn't appear to cause any measurable performance impact on my system.

  

If the system is dropping connections because the table is full, then that's a different story.

#### Connection Tracking limits the number of concurrent connections on your system

This is the most obvious downside of using connection tracking and it usually manifests as an error message on dmesg, such as:

  

nf\_conntrack: table full, dropping packet

  

You can verify how many entries are in the IP conntrack table by querying the procfs stats for conntrack of IPv4 (or if you live in the future, IPv6):

  

> $ cat /proc/sys/net/ipv4/netfilter/ip\_conntrack\_count  
> 65536

  

To see the limit, see:

> $ cat /proc/sys/net/ipv4/netfilter/ip\_conntrack\_max  
> 65536

  

Uh, oh. If the count of tracked connections is the same as the limit (or close to it), you have a problem. If this is not expected on your system, you can use the "conntrack" utility to inspect the table (conntrack -L conntrack) or even delete the whole table, i.e: flush it (option -F).

  

This would lead to possibly rare and very difficult to debug latency issues (or whatever is the effect on your system from packet loss).

  

If you're near or above the limit, you have two options:

1.  [change the connection tracking table to allow tracking more connections](http://www.cyberciti.biz/faq/ip_conntrack-table-ful-dropping-packet-error/) (uses more memory); 
2.  disable all connection tracking.  This assumes you don't need any stateful firewall rules on this server.

#### Disabling connection tracking

Assuming you don't need stateful firewall rules on your server, you can disable connection tracking. The main benefits of disabling conntrack are:

*   No need to worry about tuning the limits anymore
*   The system needs less RAM

Disabling conntrack is harder then you'd think. Linux will refuse to uninstall a module that is being used, so you can't simply run "rmmod nf\_conntrack" because it will complain:

> \# rmmod nf\_conntrack  
> ERROR: Module nf\_conntrack is in use by: ipt\_MASQUERADE nf\_nat xt\_state nf\_nat\_ipv4 iptable\_nat nf\_conntrack\_ipv4

Even if you flush the conntrack table(s) with "conntrack -F conntrack", it won't suffice - most likely because the table is filled as soon as a new connection comes in.

  

The most reliable way to disable conntrack is to blacklist the conntrack modules and preventing them from being enabled during startup. On my Debian/Ubuntu system, this appears to do the trick:

  

> echo 'blacklist nf\_conntrack' >> /etc/modprobe.d/conntrack.conf

  
Then reboot and run "lsmod|grep conntrack" to confirm that the modules aren't installed anymore.  
  
But do note that just running certain "iptables" commands will immediately install the right modules (sadly, ignoring the blacklisting).

  

Good luck!