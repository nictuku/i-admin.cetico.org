+++
title = "From Ground Zero to Production: My Journey with Go at Google"
date = 2024-03-08
author = "Yves Junqueira"
description = "An exploration of Go's adoption at Google from the perspective of an SRE and early adopter, detailing the challenges and milestones of integrating Go into production systems."
categories = ["Programming", "Go", "History"]
tags = ["golang", "google", "programming history"]
draft = false
+++

![The Early Days of Go at Google](/images/go.png)

Recently, [Jeremy
Mason](https://www.linkedin.com/pulse/language-policy-google-lets-go-jeremy-manson-ffmac/?trackingId=8vWpNw4USImSRBV7MXwi%2BQ%3D%3D)
and [Sameer
Ajmani](https://www.linkedin.com/pulse/gos-early-growth-2012-2016-sameer-ajmani-oxtjc/)
wrote about the saga to make Go one of Google's internal languages. [Go
is currently the 8th most popular programming language in the
world](https://www.infoworld.com/article/3712690/go-language-hits-top-10-in-the-tiobe-index.html)
and it is still growing, so people are interested to learn about the
early days and how we got here.

I thought I would write about the perspective of an SRE, framework
developer and early adopter. All information I'm sharing is related to
systems that Google already documented publicly, so I don't think I'm
revealing any secrets. There are some important parts of this story
(e.g: envelope) that I haven't seen mentioned elsewhere so I won't
discuss them.

## Breaking the Ice: My Introduction to Go Programming at Google

I started looking at Go before it was released publicly, and when it
launched, I became an instant fan and an early user inside Google. I
loved its simplicity.

I worked a bit on core libraries and was active in the community,
helping users in the go-nuts mailing list early on and writing
open-source libraries. Later on, I helped lead the [Go Meetup in
Seattle](https://www.meetup.com/golang/) and co-organized a beloved
conference, [Go
Northwest](https://www.youtube.com/channel/UCq9zCm9qiQ6glsz8B3kwsxw).

To the best of my knowledge, I wrote the first production-critical tool
and, later on, the first user-facing service in Go at Google.

The first one was a service used to monitor the health of
[Google+](https://en.wikipedia.org/wiki/Google%2B)'s
[Bigtable](https://en.wikipedia.org/wiki/Bigtable) servers. That was one
of my jobs as a SRE. Bigtable had detailed internal stats about the
performance of each tablet, but sometimes we needed to understand *why*
a certain tablet was so overloaded and what was happening elsewhere in
the system, so we could understand the root causes. We needed to collect
that data over time and analyse it. So I built a crawler that would
inspect many thousands of servers and present detailed stats in a global
dashboard.

In 2011, Andrew Gerrand gave [an interview to The
Register](https://www.theregister.com/2011/05/05/google_go/?page=4)
where he mentioned this work. He confirmed to me at the time that this
referred to my project. I was thrilled! He said this in the interview:

> *"Google has people who administer apps and services, and they need
> to, say, write tools that say scrape a few thousand machines statuses
> and aggregate the data," he says. "Previously, these operations people
> would write these in Python, but they're finding that Go is much
> faster in terms of performance and time to actually write the code."*

*"Google has people who administer apps and services, and they need to,
say, write tools that say scrape a few thousand machines statuses and
aggregate the data," he says. "Previously, these operations people would
write these in Python, but they're finding that Go is much faster in
terms of performance and time to actually write the code."*

Go was indeed faster to run and faster to write. Most importantly, it
felt *fun to use*. It made me more productive, so I got hooked!

## Low-level libraries: LOAS, Stubby

When Go started, it couldn't talk to Google's internal infrastructure.

First, the team had to build a protocol-buffers-based [stubby
RPC](https://cloud.google.com/blog/products/gcp/grpc-a-true-internet-scale-rpc-framework-is-now-1-and-ready-for-production-deployments)
system. This required implementing
[LOAS](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/45728.pdf)
to encrypt and authenticate communication with remote nodes, and
[Chubby](https://static.googleusercontent.com/media/research.google.com/en//archive/chubby-osdi06.pdf)
for name resolution (similar to [etcd](https://etcd.io/docs/v3.3/faq/),
used in kubernetes).

Stubby and Chubby are notoriously complex. Stubby requires a complicated
state machine for managing connections but most of the heavy lifting is
done by Chubby, which needs to provide a consistent view of the world
even when
[Borg](https://research.google/pubs/large-scale-cluster-management-at-google-with-borg/)
nodes run out of CPU, or are temporarily disconnected from the network
because someone is running a MapReduce and eating all your rack's switch
bandwidth. It's easy to end-up with deadlocks or reliability issues.

Due to [Hyrum's law](https://www.hyrumslaw.com/), which states that all
observable behaviors of your system will be depended on by somebody, the
team had to make sure to match the exact behaviour expected by the
existing production network and watch out for corner cases. For example,
healthchecking is notoriously easy to get wrong, and should not be too
strict otherwise they leave the door open for cascading failures when a
part of the network is temporarily overloaded or disconnected from the
other part. Other tricky distributed system features that had to be
implemented, such as backend subsetting and load-balancing. We needed to
diagnose when things went wrong, so logging and metrics libraries were
added early on.

In order to find which host:port to talk to, services used Chubby for
name resolution. It worked as a fully-consistent storage system for
small amounts of data, and its most used feature was to resolve
[BNS](https://sre.google/sre-book/production-environment/) addresses -
similar to what you would see today with etcd in kubernetes.

Systems sent and received data to and from other services using the
Stubby protocol. In Stubby (like in [gRPC](https://grpc.io/)), messages
were encoded using the protocol-buffers wire format. Creating
protocol-buffers payload at runtime using reflection would be too slow
and resource intensive. Engineers would also miss the lack of feedback
from a strong-typed system. For those reasons, Google used generated
code libraries for all languages. Luckily, protocol buffers are
language-agnostic. The team just had to write
[Blaze](https://bazel.build/basics/artifact-based-builds) extensions to
the existing build system logic and, voila, we had high-quality client
library code for all internal RPC services.

Curiously, there is a small incremental build time cost to generate code
for another language, and Google had many, many thousands of RPC
services. Therefore, it was decided that owners of each RPC service had
to opt-in to allow the build system to generate Go code for their
particular service. Although a little bit bureacratic, over time we saw
thousands of CLs (google's equivalent Pull Requests) flying around to
add Go to the set of generated code for each service. This served as a
silly but fun measure of progress for our community since we could count
the number of instances of the "enable Go" flags in the code base.

## Influencing the Global Master Selection and Executing Bigtable Drainage

As an early adopter of those early libraries and an engineer focused in
production systems, I was able to learn how internal systems worked. I
helped debug and fix many weird problems. Over time, I acquired the
confidence to build systems to automate operational SRE work. Noticing
that most of the user-facing outages on our services happened in the
storage layer (Bigtable or Colossus), I had the idea to create a control
system that would monitor the health of Bigtable partitions and
carefully drain them in
[GSLB](https://sre.google/workbook/managing-load/) when it detected
problems. At the time, when an outage occurred, an SRE would get paged
and, after confirming it was a storage issue, they would simply drain
the cluster and go back to sleep.

I wanted to replace this manual whackamole with a proper control system.
Draining traffic could lead to cascading failures so it was a dangerous
operation. At the time, most SREs did not want to take this kind of risk
with an automated system. Luckily, I had a good team. They carefully
reviewed my proposal, provided lots of feedback about potential failure
modes, and we eventually came up with a design that we were confident
enough with. We needed to carefully aggregate information from different
monitor systems (which could fail or provide incorrect data), use the
global load balancer to safely traffic away from a cluster, then finally
open a ticket in
[Buganizer](https://www.freecodecamp.org/news/messing-with-the-google-buganizer-system-for-15-600-in-bounties-58f86cc9f9a5/)
for the oncall SRE to clean-up during work hours.

The system needed multiple replicas to be always on to react to an
outage, but it was critical that a single replica remained live at a
time. To support that, I wrote a global "master election" library for Go
that would ensure a single replica of the system would be active at a
time. It used the global chubby lock service to provide a high-level
library to tell the application to start operating or shut itself down
if it can't prove that we hold the "global lock".

To support this work, I also wrote minor utilities here and there, and
worked with the Go team to fix bugs. I reported issues I'd find, and
they fixed them.

At the time, the Go's team focus was on external users. All their
attention was on releasing Go 1.0. It was a small team with few
resources, but their "secret sauce" is that they were exceptional
engineers and the team was very productive. Somehow, they supported
internal users very well even though their time was so limited. The
internal mailing list was very active with googlers mostly playing
around with Go for side-projects, but the Go team adopted very robust
internal processes to make things run smoothly. They reviewed everyone's
code carefully and helped build a strong internal code quality culture.
Whenever they released a new candidate version of Go, they would rebuild
all internal projects with the new version and re-run our tests to make
sure things were OK. They always did things the right way.

## Initial Insights from JID Proxy Deployment in Production

A few months later I wrote the first user-facing service in Go at
Google. By user-facing I mean that if it stopped working many
user-facing products would stop working. It was a simple RPC service,
but it was used by all Google messaging services.

This service converted data to and from [JID
format](https://en.wikipedia.org/wiki/JID_(Jabber)) based on internal
user ids obtained from another RPC service. The service was simple but
it was massive, doing hundreds of thousands of requests per second at
the time. It was critical to the core of Google's messaging services
that powered Android, Hangouts and other products.

This migration was a very important test bed for Go at Google.
Critically, it gave us an incredible base to compare the performance of
Go vis-a-vis our other production languages - specifically Java. This
service was replacing a Java-based one that was difficult to maintain
(not because of Java but for other reasons) so we ran both of them at
the same time with real production traffic and compared their
performance closely.

We learned important lessons from that first large-scale experiment: Go
used more CPU cores than Java to serve the same traffic, but the garbage
collection (GC) pauses were extremely short. As an SRE that worked hard
to reduce GC pauses to improve tail latency in user-facing services,
that was very promising to see. The Go team was happy with that result
but they weren't surprised: Go was just doing what it was designed to
do!

In fact, years later when the [SRE leadership officially reviewed Go's
readiness for
production](https://www.linkedin.com/pulse/language-policy-google-lets-go-jeremy-manson-ffmac/)
and asked the Go team to ensure Go had good GC performance, I think it
was largely pro-forma. Go had proved early on that Go had exceptional GC
performance, and it kept getting better over the years.

## Encountering Absent In-house Libraries

In those early days, before
[flywheel](https://research.google/pubs/flywheel-googles-data-compression-proxy-for-the-mobile-web/),
before the [dl.google.com](https://dl.google.com/) service, before
[Vitess](https://vitess.io/), Go was ignored by most of the engineers at
Google. If someone wanted to ship a product to users, they would first
have to write the basic building blocks that let them connect to other
services at Google. That was a non-starter for most. 

The lower-level libraries for the lock service  (chubby) and the RPC
system (stubby) popped up relatively quickly (again, the Go team was
**extremely** good), the most important libraries at Google were the
interfaces with our storage systems: Bigtable, Megastore, Spanner,
Colossus. If you wanted to read or write data, you basically couldn't
use Go yet. But, slowly, the Go team, sometimes in partnership with core
infrastructure teams, started to tackle this challenge.

One by one, they eventually created libraries for Bigtable, Colossus and
even Spanner (not Megastore, since it was largely a library that was
replaced by Spanner). That was a major achievement.

Usage at Google was still limited but our community was growing. I gave
the first official **Introduction to Go** **Programming** class at
Google and helped people in Zurich find interesting projects to work in
Go. Around this time I finally got "readability" in Go, and later joined
the Go readability team.

## The need for Site Reliability Engineers to guide application functionality

The other thing missing in Go were production-related features that we
learned over the years were necessary for production teams. That is, if
you want to run large-scale systems without constantly being in ops
mode, fighting fires.

Whenever an outage occurs and we diagnose the root causes, over time we
learn the weaknesses in the system that should be improved. The goal is
to reduce outages and operational overhead. Often times, to make the
system more reliable, we have to make changes to the application
runtime. It's hard appreciate the depth of details we need to observe
and control a system to truly make it reliable.

For example, we need to make sure that, in addition to logging incoming
requests, applications should also log details about outgoing requests
that were involved in that operation. This way, we can pinpoint with
certainty that, say, our "CallBob" service became slow at 11:34am
because of increased latency to the "FindAddress" calls. When we operate
large-scale systems, we can't be satisfied with guess work and weak
correlations. There are too many red herrings and "optimistic" root
cause assignment. We need to higher certainty about the causes: we want
to see that the specific requests that failed did experience
high-latency, and exclude other explanations (i.e: incoming requests
that did not trigger a slow FindAddress call shouldn't be failing).

Similarly, over the years we noticed that a large chunk of SRE time was
spent coordinating between teams to determine the exact number of
connections and requests per second that one service should send to
another, and how those connections should be established exactly. For
example, if several services want to connect to a backend, we want to be
smart about which exact nodes are connecting to which other nodes. This
is called backend subsetting. It needs to be carefully adjusted
considering the overall system health and not just of one node or a node
pair, but of the entire network. Excessively large subsets cause too
much resource usage, and excessively small subsets can lead to load
imbalances. For this reason, over time, SRE teams started to help
maintain the client libraries used to talk to their service, so they
could instrument what was happening and retain some control over how
other nodes talked to their systems.

## Unveiling the Magic: a server toolkit for Go

The model where SRE co-owned the client libraries worked very well in
pratice, and over time we learned that it was a great idea to also add
traffic and load management to these libraries.

-   What do you do to incoming RPCs when your system is starting to
    overload?
-   Should you hold those requests in a queue, or immediately reject
    them?
-   What metrics should you use to determine that your system is
    overloaded?
-   How can you avoid entering a cascading failure when too many parts
    of the system think they are overloaded?

Alejo Forero Cuervo wrote about the lessons learned in the SRE book
chapter [Handling
Overload](https://sre.google/sre-book/handling-overload/), it's worth
reading. One by one, we added careful logic to the libraries to
automatically set these parameters based on experience and internal
sensors.

In [The Evolving SRE Engagement
Model](https://sre.google/sre-book/evolving-sre-engagement-model/), my
former colleague Ashish Bhambhani and my former boss Acacio Cruz
explained that we eventually evolved the SRE engagement model to include
work and adoption of Server Frameworks. This model allowed SREs to
directly influence the behaviour of systems in nuanced areas that
benefitted from our extensive field experience.

My SRE team and I wanted to bring these features to Go, but they were
too exotic and specialized for the Go team to handle. I set up a 20%
project (which later became a full-time project) and recruited a bunch
of experienced engineers that wanted to contribute. I flew to New York
and met with a very awesome Go team member and we worked together to
build a roadmap for a "server framework" in Go.

The Go team was reluctant at first with our approach. The whole
"framework" concept was a little bit of a red flag for them. This could
have become a religous war of sorts, but the Go team took the time to
explain in detail why they were concerned. Sameer specifically has an
uncanny ability to reflect and explain *why* he thinks something works
better one way vs another, in technical terms.

Sameer felt strongly that Go should not have inconsistent developer
experiences, internal vs external, with or without a "framework". It
would be a disservice to the internal Go community if there were
different ways to build Go applications at Google. In agreement with his
concern, our ragtag team of 20%-ers went through great lengths to ensure
our "framework" felt more like just another library, not a framework,
and that it would not introduce a different programming model for Go.
The goal was to introduce our reliability features with a simple library
import. If you wrapped your Go HTTP or Stubby server with our libraries,
everything would look the same in code, but you magically got logging,
instrumentation, load-shedding, traffic management and even per-request
experimentation support, out of the box.

In order to create this magic library that made services better, we had to
make significant changes to Google's internal RPC libraries and even to the
build system - to enable our framework team to create arbitrary "extensions"
to the RPC systems that would operate seamlessly without significant performance
overhead when receiving and sending requests.

The result was worth it. It worked really well. Our project made services significantly
easier to manage, without imposing a programming style that was different from what the
Go team wanted. We called it a server "toolkit" to avoid confusion, and it the 
became the Right Way to build production-ready systems at Google. People often
cite our internal server framework ) in their LinkedIn profile :). It was
called Goa, not to be confused with the unrelated external [Goa
framework](https://goa.design/). Here\'s an example from someone\'s LinkedIn
profile:

![Goa mentioned in people's LinkedIn profile](/images/goa.png)

With its production-readiness features, our Go toolkit removed a major
roadblock for Go's internal growth. Engineers could now be confident that their
Go projects would perform as well as, and be as debuggable as, their older Java
and C++ siblings. That said, growth didn't quite happen yet. Go needed a killer
use-case to become popular at Google.

### Go\'s Adoption Across Several SRE Teams

At the time, the SRE team I was part of was a special place at Google,
the Social SRE team. We had great engineers and exceptional management
in both SWE and SRE. So we were able to do things the right way. Some
SRE teams were chasing their tails fighting fires, but we had the luxury
of engineering things properly. That created a virtuous cycle where we
continuously fixed problems before they became big, which means we had
time to optimize operations further, and so on.

As a result, our SRE team wrote a lot of useful code. Like my fellow
senior engineers, I helped people find things to do, so I helped
kickstart many early production-related tools in Go. One of those tools
would automatically, and safely, remove traffic away from an entire
Bigtable cluster if it noticed something wrong.

There were also other projects, in Java and C++, related to traffic and
load management, led by other senior engineers. This innovative
environment attracted talent and we continued to deliver good results,
so our SRE team grew. 

Our engineering director [Acacio
Cruz](https://www.linkedin.com/in/acacio/) (responsible for many of the
positive things happening with our team, along with his peers in
Mountain View) was very attuned to engineering efficiency: are we using
our engineering time for the most impactful things?  He understood that
there is efficiency in standardization, and he saw that our engineers
were happy and productive. He had the idea of pushing for Go to become
the tool of choice for any automation within our team. The proposal was
to avoid Python and use Go to write production tooling. To my surprise,
none of my teammates objected. That accelerated the usage of Go within
our social SRE team, and soon folks outside of our area took notice.

The core libraries, server framework, the successful production tools
and the social SRE standardization around Go - they all contributed to a
changing perception that Go was becoming a serious language at Google.

At the same time, SREs had seen a couple of generations of tools written
in Python that worked extremely well but became very difficult to
maintain over time. Google SREs enjoyed Python, we wrote a ton of Python
code. Unfortunately, at the time, the lack of types and compile-time
syntax error checks caused many hard-to-fix issues:

1.  When you work on a project that someone else started, that project
    may or may not have good test coverage. It's difficult to add tests
    for code you didn't write. You don't really know what is being used
    and how. So you end up testing too many things or testing too
    little. In production-critical tools we can't take risks when making
    changes.
2.  At the time, people generally wrote code in one moment and ran the
    tests in another moment. If you only realize you have syntax errors
    when you run tests, maybe you already context switched to doing
    something else, so now you have to go back and fix it. That wastes
    time and adds uncertainty.

As more and more SREs started to write automation in Go, it became clear
that those teams were happy and productive and were less likely to get
stuck with hard-to-maintain code. It started to dawn on people that Go
projects are easier to evolve and maintain, and that it was not just an
effect of those projects being newer, cleaner or just better engineered.

SRE leadership noticed this effect and decided to take action and
communicate very broadly within the organization: [SRE teams should
preferably use Go for production-related
projects](https://go.dev/solutions/google/sitereliability), and avoid
Python. I don't know if this is now seen at Google as something
dictatorial, but at the time I think it just felt like good org-wide
communication and decision-making.

## Go Production Platform and explosive growth

Things accelerated quickly after that. We created a production platform
that had strong support for Go since the early days and replaced a lot
of boilerplate configuration and repetitive procedures with high-level
abstractions. This platform saw strong growth and eventually other
platforms surfaced. Go and our server framework became ubiquitous. I
eventually left Google but I remember those days with joy.

While I was just a user of the language, the experience of watching a
project go  from zero to being a top-10 programming language has taught
me a lot. I could see with my own eyes that a strong team, surrounded by
a strong community, can really make **big** things.

## Observing Go\'s Ascend to Prominence

My time working with Go programming at Google has been a game-changer,
giving me a great understanding of the project\'s technical side and how
a world-famous team operates. As the project went on, I could clearly
see how Go can make project and team scaling easier.

Go\'s emphasis on minimalistic design facilitated uniform coding, making
it easy to integrate new programmers into the project, a feature
particularly useful in projects on a tight schedule. As the project
grew, new libraries and toolkits also emerged, increasing its popularity
and facilitating its adoption by several big tech companies including
Apple, Facebook, and Docker.  

Despite Rust having an extensive range of features, the widespread
acceptance of Go across various industries testifies to its exceptional
capabilities. Go demonstrates that powerful software doesn\'t
necessarily need to be complicated.  

From its creation in 2009 by a small team at Google to its position
among the top ten programming languages, Go\'s journey is a significant
chapter in the history of modern programming. This journey inspires
tech-business founders aiming to create faster, more efficient, and
portable applications.  

Looking back, it\'s clear that while our journey was filled with
challenges, each twist and turn, each adjustment and advancement, was
key to shaping today\'s Go. As we move forward, we hold onto the
valuable lessons from the past, the assurance of the present, and the
anticipation for what\'s to come.
