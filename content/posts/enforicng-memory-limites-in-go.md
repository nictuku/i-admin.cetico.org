---
title: 'Enforicng memory limites in Go'
date: 2016-11-17T11:34:00.002-08:00
draft: true
url: 
---

Enforcing memory limits in Go
-----------------------------

RESIST TEMPTATION TO PUBLISH EARLY
----------------------------------

Go currently does not have a convenient way for applications to limit their own memory usage.  

This article argues that we do need a robust method for limiting memory usage that works for most application types, and that such method is within reach.

  
Hopefully by the end of this article you'll agree that a solution is desirable and exists, we just haven’t connected all the pieces of the puzzle yet.  

Why some applications work fine without memory limits?
------------------------------------------------------

Many applications are OK with the current state of things. Users expect that memory usage will probably stay within certain bounds and are unlikely to go beyond a certain threshold.

  

The Go heap size is limited to 2x the peak live allocations. So as long as you don't keep too many things in memory at the same time, the Go runtime won't go too crazy with your heap size.

  

For systems running in production with resource isolation, when an application does go beyond a container limit, it crashes. In production setups servers will be restarted, which sort of fixes the problem. Clients become aware of the failed requests immediately (TCP connections are reset) and retry.

  

The current behavior works fine for many cases so it's understandable when people are skeptic about making this more complicated.

Larger systems need robust memory limits
----------------------------------------

Critical production systems can't just hope for the best.  
  
Large applications like [CockroachDB](https://github.com/cockroachdb/cockroach/issues/7573), [Prometheus](https://github.com/prometheus/prometheus/issues/455) and [SyncThing](https://github.com/syncthing/syncthing/issues/468) can't reliably predict their future memory usage, for several reasons.  
  
**Backend get slower.** When requests to backends take longer to complete, the server ends up holding requests for longer than usual. If the rate of incoming request stays constant, objects stay in memory for longer. The peak live memory usage goes up. In Go this means the amount of memory obtained from the system also goes up.  
  
**Libraries manage their own allocations.** To avoid surprises from libraries allocating more memory than expected, critical systems currently have to be extremely careful with their dependencies. This defeats the basic idea of how modern software is developed. We need to use existing building blocks. It may introduce some extra risk, but using third-party libraries should not preclude careful prediction of memory needs.  
  
**Network clients send requests with different payload types.** We have to allocate memory to read requests from the wire and to process it, often using intermediary data from another server. While it is always desirable to be conservative when dealing with data inputs on a network server, we can't escape the randomness of request costs. If the average request needs 20MB of memory allocated on the heap today, tomorrow that might be 30MB because a backend is now returning more data. Robust systems need to be resilient to such changes and degrade gracefully even when the limits are reached.

  
**It's impossible for applications to account for all memory usage**. At best service owners can identify and add protection around sensitive areas. But it's a herculean effort. The more complex the system the harder it is to make sure that memory allocations stay within limits.  
  
As we'll see when we review below how we "manage" memory in large C++ applications, manual memory accounting is hardly practical.  
  
**Crashing is not safe**: many systems cannot afford to crash in order to workaround increase in memory loads. Crashing stateful systems can be unsafe and/or costly. 

  

Systems that buffer data and write them to disk every several seconds or so, like Prometheus, crashing could mean data might be lost if there is no time to checkpoint recent data before the process exits. 

  

Distributed storage systems like CockroachDB may lose majority and become unable to coordinate work. When the server comes up again it still has to replay Raft logs, being unavailable in the meantime.  
  
After servers crash the service capacity is reduced for some time. Since the crashes are not coordinated, they may easily hit more than one server at a time. If the overall system capacity goes under _N_, that triggers a nightmare cascading failure scenario.

  

So we need a robust way to set memory limits. But what would that even mean in Go? Before we answer that, let's first review what other languages do.

How other languages set limits on memory usage?
-----------------------------------------------

It seems useful to review how other languages attempt to set limits on memory usage. I'll discuss the merits and challenges with their approaches.  
  

### Language-neutral methods to limit memory usage

#### Let memory grow as needed; use swap space and kernel OOMs

It is a common practice to let processes use as much heap as they want, and rely on swap space and the kernel OOM subsystem to take care of the exceptional cases.

  

We can expand the system physical RAM by using a disk-based swap space where unused memory pages are temporarily parked until they are needed again. This ancient technique works well, but disks are extremely slow so swapping in can be very slow. Applications work best with predictable latency. We don't want to make users wait for several MBs of pages to be swapped back into the main system memory whenever those pages need to be used again. To get stable and predictable latency leads, most operators disable swapping for their systems. Swapping is not a good option for lots of production systems.

  

Another blunt instrument is to rely on the kernel OOM killer. When Linux runs out of available memory it tries to find a new and unimportant process to kill. The OOM killer can be a good last case resort for dealing with processes using unexpected amount of RAM if the operator didn't set limits on the container. It can also be useful if all containers have limits but are (often intentionally) oversubscribed so having a safety net is useful.

  

But the OOM killer won't restart processes by itself. And in most cases it will use a SIGKILL and not give task any time for a graceful shutdown. It has all the problems mentioned above with crashes: it's not safe.

#### Set a memory limit for the process or container or job

An alternative to limit memory usage for a process on **Linux**  is to use setrlimit or ulimit. Unfortunately they only control the virtual memory usage, which is rarely useful.  
  
The most robust approach on Linux is to use cgroup memory limits. cgroups are the building blocks of containers, which provide real process isolations for applications running on the same kernel. The cgroups can enforce a limit on how much _main system memory_ the processes within a container can use. If there's only one process, that's typically the process' RSS. This method is used by lxc, docker, kubernetes, borg and pretty much everybody else.  
  
For anybody that wants to isolate processes' memory usage without running separate kernels, cgroups is the way to go.  
  
**Windows** applications have an API to set limits for the virtual memory usage, but that's rarely useful. To really ensure isolation and block applications for using too much memory, the SetProcessWorkingSetSizeEx function is the one to use. It can set hard limits for the resident memory usage for a process.  
  
The cgroups on Linux and SetProcessWorkingSetSizeEx on Windows work very well for enforcing memory isolation between C++ applications running on the same kernel. In fact, the same methods are available in other languages, including Go.  
  
Unfortunately this approach has downsides. Like discussed above, _crashing is not safe_. Once a process reaches the container limit, it has very little time for cleanup and needs to exit abruptly. This often leads to scary events: _query of death_ (when a certain input causes repeated crashes) and _cascading failures_ (when too many servers crash, those still standing may be overloaded with the excess traffic).  

### Memory limits for C++ applications

#### Use memory accounting and manage allocations carefully

Another option for C++ projects is to replace new/malloc with versions that keep track of what has been allocated. It can refuse to allocate memory if a certain limit is crossed and handle errors gracefully.  
  
This method works if there are clear places where most allocations are done and if it's safe for those calls to fail. But it does not work reliably for libraries.  
  
Another problem is that it is work intensive and subject to the law of diminishing returns. In practice even systems with carefully managed memory and operated by experienced engineers can occasionally [fail horribly](https://blog.cloudflare.com/todays-outage-post-mortem-82515/).  
  
Manual memory management is extremely laborious and often not good enough.  
  
A radical version of this method has been proposed for Go, whereby all calls that allocate memory (_make_, _new_, composite literals) would also return an error. The error value could be used to gracefully handle out-of-memory events. The proposal was rejected because it would be too much of a departure from the current behavior.  
  
Besides, we want something done for Go 1.x, not later.  

#### Best practices for C++ in production

The most robust approach seems to be a combination of those methods: manage allocations carefully whenever possible, then use container limits to enforce isolation as a last resort.

### Memory Limits for Java (OpenJDK)

Java is pretty OK at enforcing memory limits, more than what people give it credit. Surely it's a nightmare to operate. The JVM has a million knobs for setting various limits. Every memory space can have limits.   
  
Clearly Go should stay away from having so many knobs, but keep in mind that the ultimate purpose of knobs in JVM is to give the server some _predictability_. This is an important property that we should continue to provide in Go, without the knobs.  
  
But the HotSpot does have a few desirable features for enforcing memory limits, discussed below. First a little bit of background.  
  
The JVM has several mostly independent memory regions. Certain regions are not managed by the JVM directly, such as non-heap memory used by things like C++ JNI libraries. Tracking down the memory usage from such libraries can be quite problematic. That's something we need to keep in mind because Go also has to deal with SWIG and cgo libraries where the heap.  
  
Most people set a maximum heap size to achieve predictability. This works reasonably well. The JVM triggers GCs whenever the heap starts to become full.   
  
If the heap usage goes up, the GC rate also goes up significantly - which helps keep some heap free for new allocations. **Temporarily increasing the GC rate is an effective way to trade-off memory for CPU time**. But if the GC threshold is hit frequently, the JVM may trigger GCs too often and reduce the processing capacity of the application, so this often becomes a challenge. _GC thrashing_ is a common problem.  

#### Best practices for Java in production

A common approach for large scale production systems is to set a heap size limit and throttle requests if the server becomes overloaded.   
  
When a maximum heap size is set, the JVM will deal with memory usage spikes by increasing the GC frequency.  
  
More frequent GC reduces the server processing capacity, which leads to queueing. A throttling library monitors the average queue length of the server and reject new requests if this _load average_ is too high.  
  
This approach creates a **feedback loop that prevents the server from being overloaded** and ensures a minimum processing capacity is maintained.  
  
It is recommended to also limit the container's memory of Java servers to prevent the native non-managed heap space from growing too much and hurting other applications.  

Requirements for Memory Limits in Go
------------------------------------

We need a robust memory limit system that works well for different scenarios.\\  
  

1.  Stateless servers where allocations are dominated by short-term request-scoped objects (most RPC and HTTP microservices)
2.  Stateful servers that keep a lot of important data in memory, or any servers where restarts cause significant cost or inconvenience  (Prometheus, CockroachDB)
3.  Desktop applications that need to keep memory usage in check (SyncThing, Stardew.Farm)

  
For servers, it is critical that the runtime preserve a minimum processing capacity at all times, to avoid cascading failures in case a few servers are overloaded.  

### What should be the limiting metric? STOPPED HERE

Limits should be enforced based on the process or container **peak memory footprint**. That's what most Go users should care about. That is the metric used for bin-packing and scheduling applications on systems with limited physical RAM. It is also the metric used when limits are enforced by systems such as docker, kubernetes, borg and so forth.  
  
Note that the _memory footprint_ can refer to different underlying metrics. A programmatic solution that targets at limiting this unit should be able to understand these different limits and know which one to use for a given application. AFAIK such platform and process/container neutral metric is currently not available in Go, but it is within reach.  
  

  

  

Platform

Memory Limit Entity

Go API

System API

Linux

Process

runtime.MemStats Sys

/proc//rss

Linux

Container

?

/sys/fs/cgroup/memory/.../$CONTAINER\_ID/memory.usage\_in\_bytes

Windows

Process

runtime.MemStats Sys

GetProcessWorkingSetSize

  
  
Go doesn't have an API that abstracts the operation of determining the memory usage for the entity that has limits (process or container).   
  
The good news is the underlying data is available on Windows and Linux through convenient system APIs that allow efficient lookups and also support notifications for when the memory footprint goes beyond a threshold.  
  
So how do we make Go programs stay within a certain memory footprint limit most of the time?  

### How to limit memory limits for Go servers?

Application owners want to limit their program to only use a certain amount of RAM based on the container or process memory footprint. If you say your Go program should use only 10GB of RAM, we should respect that.  
  
I'll discuss possible strategies for how Go could help with that, then present a proposal for a general purpose approach.  

#### Reject new requests if the limit is crossed; use the runtime.MemStats

We could limit memory usage by rejecting new requests once the server uses too much memory as reported by the runtime MemStats (e.g: Sys or HeapSys). This has several problems.  
  
Inspecting MemStats currently causes a stop-the-world pause. We could look into optimizing it for this use case, but at the end of the day we don't need all the stats in the MemStats struct anyway. The next section will discuss a better alternative.  
  
Another problem is that simply rejecting all new requests when we're above the threshold would not work. Rejecting all requests drops the rate of GCs to zero. Without GC, the system can't reclaim free up heap space, so it won't ever leave that state. We could randomize or use a dynamic rejection probability function, but throttling of any rate will reduces the GC frequency, which means it'll take longer to fix the problem.  
  
The primary goal of memory back pressure system should be free up Go heap space for future requests. Merely rejecting requests is not enough.  

  

#### Reject new requests if the limit is crossed; use the process or container memory usage numbers from the kernel

We could reject requests if the process or container RSS usage is too high. Part of the problems mentioned above still apply, but it has a few advantages.  
  
It doesn't require a stop-the-world operation for collecting stats.  
  
It doesn't rely on polling. The cgroup notification API can be used in Linux to receive a notification whenever memory usage crosses a threshold or increases, so the system can react faster to memory usage spikes.  
  
But like in previous option, rejecting new requests is not sufficient. It may help reduce memory usage growth, but it won’t help dealing with live or recently dead objects - on the contrary, it slows the rate of GC and we want the opposite.  
  
Another problem is that reducing the rate of new allocations by itself has only an indirect and delayed effect on reducing the RSS.  
  
REWRITE THIS AND BELOW. Need to clarify that we \_can\_ influence RSS by GC'ing and stopping allocations. Just need to make the limits clear.  
  
In Go, the RSS can be seen as a high watermark for the process memory usage. If the Go memory usage increases temporarily because the application had a burst of allocations, the process RSS will stay like that for a while because Go avoids churning the system memory and growing up and down too much. Only after a while Go starts the scavenging process to return system memory, or if the application calls debug.FreeOSMemory.  
  
Rejecting requests slowdowns of the growth of Go’s InUse space, but the RSS is not immediately reduced.  

#### Increase the rate of GC if the limit is crossed

We need to free up Go heap space for future requests when things are under pressure. In Go the GC is responsible for that.  
  
You might be asking why doing a garbage collection would help controlling the physical memory. The answer is it reduces pressure. If we free up space in the Go heap, then the runtime doesn't have to grab more memory from the system to fit new objects.  
  
  
  
How would we do that? We could observe the current RSS and then either trigger GCs manually or lower the GOGC value whenever the RSS goes above a certain limit.  
  
**PROBLEMS** to be addressed: avoid polling; freeing up heap is not enough (we also need to slow down things).  
  
Explain formula for when freeing up heap is not enough. (formula graph?)  
  
how do we observe the current RSS? Polling would be expensive and hurt performance  
  
A better alternative is to add a check in the runtime itself. It should account for allocations and trigger a GC whenever it allocates memory that makes it approach the limit. #remember Avoids polling  
No matter what, it's hard to prevent crashes due to a single large allocation #remember  
The effect of increasing the rate of GCing is to free up Go heap space for future requests. It is not a hard guarantee that the limit won't be reached.  
As an isolated strategy, GCing more often would eventually lead to thrashing and reduce the processing capacity

#### Pushback Threshold (my proposal)

Assume the following new configuration values (environment variables + runtime options)

  

// Attempt to limit memory usage to this value

MEMLIMIT=8GB

  

// Enter pushback mode if the process RSS is at this percentage of the MEMLIMIT

// (This doesn't have to be an option, it could be an internal value)  
PUSHBACK\_THRESHOLD=80

  

Then apply this logic:

1.  Monitor for the pushback threshold. Don't use polling. Alternatives:

1.  Add checks around Go's tcmalloc. New allocations would return an estimate of the current process memory usage.

1.  problem: this will only consider the heap of the current process

3.  Use the system's memory usage notification API (e.g: Linux cgroup notification API)

3.  Enter pushback mode if the threshold is crossed
4.  If in pushback mode:

1.  Maybe: loop using a time ticker to avoid triggering GC too often in extreme cases.

1.  trigger GC
2.  signal to the application that it should probably reject new requests (whatever we do, do not require polling)
3.  Applications can either _check_ the current status before handling requests; or

1.  They could use a semaphore or notification API (e.g: callback, channel read) to be notified they should temporarily slow down request handling.

5.  Give the application an urgency ratio based upon how close we are from the upper limit. This allows applications to randomize the rejections and enables **smoother throttling**. It also allows users to optionally to **only reject low-priority requests** if the load isn't too high.
6.  check memory usage after GC is finished and leave the pushback mode if possible

  

The exact semantics of the pushback mode should be set to work well with real use cases. The important aspects to take away are:

*   Go needs to know what is the relevant memory limit and memory usage
*   GC should work as usual most of the time
*   When a threshold is reached, we need to both throttle new memory allocations application \*and\* free up heap space by doing GC
*   This system is not watertight and OOMs are still possible, but the proposed algorithm should lower the OOM rate considerably