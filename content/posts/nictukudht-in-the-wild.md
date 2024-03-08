---
title: 'nictuku/dht in the wild'
date: 2018-08-04T00:19:00.001-07:00
draft: false
url: /2018/08/nictukudht-in-wild.html
---

Open-source is magical. When I wrote my distributed hash table library back in 2012, I just wanted to write something cool that made computers talk to each other efficiently at a very large scale.

  

Since publishing it, the library has been [used by botnets](https://thisissecurity.stormshield.com/2016/08/17/from-website-locker-to-ddos-rex/), has a [source code evolution video](https://www.youtube.com/watch?v=NdtqgLOm-_o) and was used by someone's [Bachelor thesis in Switzerland](http://www.stephan-robert.ch/wp-content/uploads/2015/07/Helke-report-tdb.pdf).

  

And today I found out that computer scientists from University of British Columbia and University of Bamberg have [analyzed the library in a very cool paper to verify that it's correct](https://www.icse2018.org/event/icse-2018-technical-papers-inferring-and-asserting-distributed-system-invariants)! They analyzed logs and checked that the library's behavior follows invariants expected for a correct implementation of Kademlia.

[![](https://1.bp.blogspot.com/-DV_XlCeASRc/W2VRnffRtfI/AAAAAAACN7Q/YwQyVNvnOh8mipNuV4-WcYbTYKDoDfo1ACLcBGAs/s320/nictuku-dht-analysis.png)](https://1.bp.blogspot.com/-DV_XlCeASRc/W2VRnffRtfI/AAAAAAACN7Q/YwQyVNvnOh8mipNuV4-WcYbTYKDoDfo1ACLcBGAs/s1600/nictuku-dht-analysis.png)

In text: _We logged state after the results of a Find\_Value request were added to a peer’s routing table. On each execution we found that ∀ peers i, j, peeri .min\_distance = peerj .min\_distance in all total-order groups. This invariant, in conjunction with O(Loд(n)) message bound, provides strong evidence for the correctness of Nictuku’s implementation of Kademlia._

  

Very cool.