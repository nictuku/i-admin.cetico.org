---
title: 'CockroachDB: can it be efficient?'
date: 2018-04-30T17:39:00.000-07:00
draft: true
url: 
---

The designers of CockroachDB have clear priorities in mind: high reliability, ease of use and administration, plus strong consistency guarantees.  
  
But I'm curious about its potential efficiency: if I build a very large scale system on top of CockroachDB, do I have to spend a lot of money running it?  
  
The project is still relatively new, and the team is busy focusing on the first set of goals. Performance will probably be the focus for future efforts. But what can we expect?  
  
In this post, I'll propose a simple model for CockroachDB's expected resource efficiency, then see how it compares with the current implementation. The contrast can show potential areas for improvement which we can see as the project matures - but it can also show limitations in the model.  
  
\== iops and CPU rate  
  
Let's assume we care about two simple resource metrics: IO operations and CPU usage rate.  
  
  
  
\== A simple model for the cost of running a CockroachDB cluster  
  
Write operation: needs X, Y, Z actions  
  
SI Read operation: needs A, B, C actions  
  
SSI Read Operation: needs A, B, C, D actions  
  
Background work: foo, bar. NOTE: high cost here. Raft stuff. Is it O(servers)? Would this cost become noise once the system is loaded?  
  
  
  
\== How does our simple CockroachDB model compare with other databases?  
  
Is it fair to compare CockroachDB with Redis or even PostgreSQL?  
  
  
Redis is primarily memory based. Replication is asynchronous (batching makes things easier).  

  

  
Compare to google spanner.  
  
\== What do we see in practice?  
  
  
  
NOTE: remember to fix the post schedule. It's set only to prevent accidental publishing.