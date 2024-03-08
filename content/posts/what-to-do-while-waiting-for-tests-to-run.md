---
title: 'What to do while waiting for tests to run'
date: 2021-05-26T10:56:00.000-07:00
draft: true
url: 
---

Whenever I need to wait for tests to finish running, I either read HN or Reddit for far too long, or I plot ways to make the tests go faster.

A few years ago, while waiting for a painfully slow test and thinking about how I could optimize it

  

  

, I wondered: can I use similar optimization techniques to speed up tests for other projects?

What are the technical challenges? Is it even feasible?

Also: will I stop reading HN if I make all my tests run quickly?

\- (...) 

\- discuss the investigations the author had to put in to figure out why tests are slow, how people often optimize it, how can this be generalized and what are the challenges

\- plot twits: tests are slow for different reasons in different cases

\- **moment of revelation**: one can use techniques $A and $B, combined with fast object storage (for shared graphs) to optimize tests for most of those cases, without significant code changes

\-- add a lot of details here, graphs etc. Use my own words.

\- **plan is in motion**: author got together with John to create a service to do it at scale, see this blog post that goes into more details about blablabla.

\- how the story ends: customers in production, contracts signed, coming out of stealth and hoping to help a lot of devs

\- punchline: If you are wondering if I still read a lot of HN/Reddit: I lost count of how many hours I spent there when I was supposed to finish writing this post.