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

Recently, [Jeremy Mason](https://www.linkedin.com/pulse/language-policy-google-lets-go-jeremy-manson-ffmac/?trackingId=8vWpNw4USImSRBV7MXwi%2BQ%3D%3D) and [Sameer Ajmani](https://www.linkedin.com/pulse/gos-early-growth-2012-2016-sameer-ajmani-oxtjc/) wrote about the saga to make Go one of Google’s internal languages. 

I thought I would write about the perspective of an SRE, framework developer and early adopter.

## Go in 2009

I started looking at Go before it was released publicly, and when it launched, I became an instant fan and an early user inside Google. I worked a bit on core libraries and was active in the community, helping users in the go-nuts mailing list early on and writing open-source libraries. Later on, I helped lead the [Go Meetup in Seattle](https://www.meetup.com/golang/) and co-organized a beloved conference, [Go Northwest](https://www.youtube.com/channel/UCq9zCm9qiQ6glsz8B3kwsxw).

## First production tool

To the best of my knowledge, I wrote the first production-critical tool and, later on, the first user-facing service in Go at Google.

The production tool was a service used to monitor the health of Google+'s Bigtable servers. That was one of my jobs as a SRE. In 2011, Andrew Gerrand gave [an interview to The Register](https://www.theregister.com/2011/05/05/google_go/?page=4) where he mentioned this work. He confirmed to me at the time that this referred to my project. I was thrilled! He said this in the interview:

> _"Google has people who administer apps and services, and they need to, say, write tools that say scrape a few thousand machines statuses and aggregate the data," he says. "Previously, these operations people would write these in Python, but they're finding that Go is much faster in terms of performance and time to actually write the code."_

Go was indeed faster to run and faster to write. Most importantly, it felt _fun to use_. It made me more productive, so I got hooked!

### Go’s secret sauce

At the time, Go had many missing pieces. It couldn't talk to Google’s internal infrastructure properly, so in order to do anything useful, we still had to build libraries for talking to everything. That was a very unique opportunity for me. As an early adopter of those early libraries and an engineered focused in production systems, I learned about how every system worked internally. I wrote a global lock library in Go (just a convenient wrapper for an existing library), some minor utilities here and there, and worked with the Go team to fix many bugs. I submitted occasional patches but, for the most part, I was just a user. I reported issues I'd find, and they fixed them.

At the time, the Go’s team focus was on external users. All their attention was on releasing Go 1.0. It was a small team with few resources, but their "secret sauce" is that they were exceptional engineers and the team was very productive. Somehow, they supported internal users very well even though their time was so limited. The internal mailing list was very active with googlers mostly playing around with Go for side-projects, but the Go team adopted very robust internal processes to make things run smoothly. They reviewed everyone's code carefully and helped build a strong internal code quality culture. Whenever they released a new candidate version of Go, they would rebuild all internal projects with the new version and re-run our tests to make sure things were OK. They always did things the right way.

### Initial Production Lessons

A few months later I wrote the first user-facing service in Go at Google. By user-facing I mean that if it stopped working many user-facing products would stop working. It was a simple RPC service, but it was used by all Google messaging services.

This service converted data to and from [JID format](https://en.wikipedia.org/wiki/JID_(Jabber)) based on internal user ids obtained from another RPC service. The service was simple but it was massive, doing hundreds of thousands of requests per second at the time. It was critical to the core of Google’s messaging services that powered Android, Hangouts and other products.

This migration was a very important test bed for Go at Google. Critically, it gave us an incredible base to compare the performance of Go vis-a-vis our other production languages - specifically Java. This service was replacing a Java-based one that was difficult to maintain (not because of Java but for other reasons) so we ran both of them at the same time with real production traffic and compared their performance closely.

I remember well the lessons we got from that first large-scale experiment: Go used more CPU cores than Java to serve the same traffic, but the garbage collection (GC) pauses were basically non-existent. As an SRE that fought with the JVM for years to reduce the impact of GC in tail latency, that was very promising to see. The Go team was happy with that result but they weren’t surprised: Go was just doing what it was designed to do!

In fact, years later when the [SRE leadership officially reviewed Go’s readiness for production](https://www.linkedin.com/pulse/language-policy-google-lets-go-jeremy-manson-ffmac/?trackingId=8vWpNw4USImSRBV7MXwi%2BQ%3D%3D) and asked the Go team to ensure Go had good GC performance, it was largely pro-forma. Go proved early on that Go had exceptional GC performance, and it keeps getting better.

### Missing internal libraries

In those early days, before [flywheel](https://research.google/pubs/flywheel-googles-data-compression-proxy-for-the-mobile-web/), before the [dl.google.com](http://dl.google.com/) service, before [Vitess](https://vitess.io/), Go was ignored by most of the engineers at Google, for good reason. If you needed to ship a product to users, you would first have to write the basic building blocks that let you connect to other services at Google. That was a non-starter for most. 

The lower-level libraries for the lock service  (chubby) and the RPC system (stubby) popped up relatively quickly (again, the Go team was **extremely** good), the most important libraries at Google were the interfaces with our storage systems: Bigtable, Megastore, Spanner, Colossus. If you wanted to read or write data, you basically couldn’t use Go yet. But, slowly, the Go team, sometimes in partnership with core infrastructure teams, started to tackle this challenge.

We never got Megastore in Go, because Spanner was close to being production-ready, but, one by one, they eventually created libraries for Bigtable, Colossus and even Spanner. That was a major achievement.

Usage at Google was still limited but our community was growing. I gave the first official **Introduction to Go** **Programming** class at Google and helped people in Zurich find interesting projects to work in Go. Around this time I finally got “readability” in Go, and later joined the Go readability team.

### Server Framework

The other thing missing in Go were production-related features that over the years we learned were necessary for production teams to run large-scale systems without constantly being in ops mode, fighting fires. These features looked a little too exotic for the Go team, so I set up a 20% project (which later became a full-time project) and recruited a bunch of engineers to start a “server framework” for Go. I flew to New York, met with a very awesome Go team member and we worked together to build a roadmap.

The Go team was reluctant at first with our approach. The whole “framework” concept was a little bit of a red flag for them. You know when people give you bullshit arguments and don’t really explain why they are rejecting your idea? Well, they were not like this. Sameer has this uncanny ability of reflecting and explaining _why_ something works better one way vs another, while most of us give up and use superficial arguments. I learned a lot from his emails and code reviews.

Sameer felt strongly like Go should not have inconsistent developer experiences, internal vs external, with or without a “framework”. We agreed, so our ragtag team of 20%-ers went through great lengths to ensure our “framework” felt more like just another library, not a framework, and that it would not introduce a different programming model for Go.

This worked well. With Go’s team's spiritual blessing and having been authored by us SREs to make services production-ready out of the box, our “toolkit” became the Right Way to build production-ready systems at Google. I don’t know how popular it is these days.

The production-ready toolkit removed a major roadblock for Go’s internal growth, but growth didn’t quite happen yet. Go needed a killer use-case to become popular at Google.

### Go spreads in a few SRE teams

At the time, the SRE team I was part of was a special place at Google, the Social SRE team. We had great engineers and exceptional management in both SWE and SRE. So we were able to do things the right way. Some SRE teams were chasing their tails fighting fires, but we had the luxury of engineering things properly. That created a virtuous cycle where we continuously fixed problems before they became big, which means we had time to optimize operations further, and so on.

As a result, our SRE team wrote a lot of useful code. Like my fellow senior engineers, I helped people find things to do, so I helped kickstart many early production-related tools in Go. One of those tools would automatically, and safely, remove traffic away from an entire Bigtable cluster if it noticed something wrong.

There were also other projects, in Java and C++, related to traffic and load management, led by other senior engineers. This innovative environment attracted talent and we continued to deliver good results, so our SRE team grew. 

Our engineering director [Acacio Cruz](https://www.linkedin.com/in/acacio/) (responsible for many of the positive things happening with our team, along with his peers in Mountain View) was very attuned to engineering efficiency: are we using our engineering time for the most impactful things?  He understood that there is efficiency in standardization, and he saw that our engineers were happy and productive. He had the idea of pushing for Go to become the tool of choice for any automation within our team. The proposal was to avoid Python and use Go to write production tooling. To my surprise, none of my teammates objected. That accelerated the usage of Go within our social SRE team, and soon folks outside of our area took notice.

### Go as the standard choice for tools in SRE

The core libraries, server framework, the successful production tools and the social SRE standardization around Go - they all contributed to a changing perception that Go was becoming a serious language at Google.

At the same time, SREs had seen a couple of generations of tools written in Python that worked extremely well but became very difficult to maintain over time. Google SREs enjoyed Python, we wrote a ton of Python code. Unfortunately, at the time, the lack of types and compile-time syntax error checks caused many hard-to-fix issues:

1. When you work on a project that someone else started, that project may or may not have good test coverage. It’s difficult to add tests for code you didn’t write. You don’t really know what is being used and how. So you end up testing too many things or testing too little. In production-critical tools we can’t take risks when making changes.
2. At the time, people generally wrote code in one moment and ran the tests in another moment. If you only realize you have syntax errors when you run tests, maybe you already context switched to doing something else, so now you have to go back and fix it. That wastes time and adds uncertainty.

As more and more SREs started to write automation in Go, it became clear that those teams were happy and productive and were less likely to get stuck with hard-to-maintain code. It started to dawn on people that Go projects are easier to evolve and maintain, and that it was not just an effect of those projects being newer, cleaner or just better engineered.

SRE leadership noticed this effect and decided to take action and communicate very broadly within the organization: [SRE teams should preferably use Go for production-related projects](https://go.dev/solutions/google/sitereliability), and avoid Python. I don’t know if this is now seen at Google as something dictatorial, but at the time I think it just felt like good org-wide communication and decision-making.

### Go Production Platform and explosive growth

Things accelerated quickly after that. We created a production platform that had strong support for Go since the early days and replaced a lot of boilerplate configuration and repetitive procedures with high-level abstractions. This platform saw strong growth and eventually other platforms surfaced. Go and our server framework became ubiquitous. I eventually left Google but I remember those days with joy.

While I was just a user of the language, the experience of watching a project go  from zero to being a top-10 programming language has taught me a lot. I could see with my own eyes that a strong team, surrounded by a strong community, can really make **big** things.

Just Go!
