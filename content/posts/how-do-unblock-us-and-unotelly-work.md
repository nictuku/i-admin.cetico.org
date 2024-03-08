---
title: 'How do Unblock-us and UnoTelly work?'
date: 2012-10-06T04:40:00.002-07:00
draft: true
url: 
---

Summary  
  
Unblock-us.com and UnoTelly.com are services that provide workarounds for [geo-blocking](http://en.wikipedia.org/wiki/Geo_targeting), which is used by Netflix and media providers to ensure that they are not distributing media to customers outside geographic regions specified in their license agreements.  
  
Traditional ways for bypassing geoblocking are HTTP and SOCKS proxy, and VPNs.  
  
HTTP or SOCKS proxies  
  
problems:  
  

*   good free proxies are hard to find. These open proxies usually stop working after a few days, so you have to go look for another soon right when you wanted to watch that movie. Besides, there is a good chance that the proxy owner is using it for sniffing and password stealing. They are often installed in hacked machines anyway, unknowingly to the owner - so their usage should be discouraged.
*   Paid proxies cost around $X/month. They are much more reliable than the dodgy open ones

  
  
  
Unblock-us.com and UnoTelly  work by making special configurations of DNS (Domain Name System) and by using a mix of HTTP proxies and raw TCP tunnels. They have to work around a couple interesting challenges, SSL encryption and authorization, but the solutions are simpler than one could imagine.  
  
\==  
  
This article explains how Unblock-us and UnoTelly work. I may have not gotten every details right, but I'm quite sure that I figured out the most important parts because I was able to write my own limited clone of these services. I've been using it successfully since then: [MaskedHost.com](http://maskedhost.com/).
