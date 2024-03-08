---
title: 'I''ve built something: stardew.farm'
date: 2016-03-30T00:32:00.000-07:00
draft: false
url: /2016/03/ive-built-something-stardewfarm.html
---

I've built something called [stardew.farm](http://stardew.farm/). It helps [Stardew Valley](http://stardewvalley.net/) players to share screenshots of their pretty farms.  
  
It's a bunch of [open-source software written in Go](https://github.com/nictuku/stardew-rocks).  
  
A Windows client watches the player's save files and uploads them to a RabbitMQ server whenever the game saves state (once a day in the game).  
  
Then there's a Go program that parses every new save game and renders a screenshot using the _[image/draw](https://golang.org/pkg/image/draw/)_ and _[github.com/disintegration/imaging](http://github.com/disintegration/imaging)_ packages. We put that screenshot in a nice little website for everyone to see.  
  
The screenshot emulates the game's appearance, except that it plots the entire farm in one image. People love that.  
  

[![](https://4.bp.blogspot.com/-STNuI7EVY6g/Vvtr5c2aqLI/AAAAAAABhF8/3bL3Rnbyp0kjeapAqYDtr-f6_qAJAYa9A/s640/stardewfarm.png)](http://stardew.farm/)

  
There's a bunch of people helping me on the project. The javascript frontend was written entirely by another guy, also on his free time. Another person is helping fix rendering problems. And a bunch of people have spent a lot of time helping test it. The SDV modding community has been very supportive so this has been a great hobby project for me.  
  

[![](https://2.bp.blogspot.com/-UqSbjky_e0E/Vvtsv4-BKQI/AAAAAAABhGE/oSxbeizrMVUNa9T5d6cioRaOHsK8DtzKg/s640/JackyBreak.png)](http://stardew.farm/56f362f61700d41c1e594af6)

  
People are building some really impressive farms, which is making the rendering work quite annoying. See for example the farm house on the top right with a tree sticking out of the roof - that's not supposed to be there :-).  
  
The website uses React on top of a Go-based API server. We're trying to develop things quickly (even more so on weekends), which has led to several breakages and regressions in "production". So I decided to write proper deployment scripts and configure staging environments so we could test stuff before deploying them. It's boring work but it's the kind of investment that pays off, just like at work.  
  
The project is giving me exposure to technologies I don't normally use at work, such as RabbitMQ, MongoDB ([mgo](https://labix.org/mgo) is fantastic, holy cow), Javascript app frameworks and frontend testing infrastructure. I could potentially write posts explaining how everything works, but it's hard enough to find time for the main hobby, so it's probably not going to happen.  
  

[![](https://2.bp.blogspot.com/-a2Y8hsbxk5Y/Vvt_j-Yv1GI/AAAAAAABhGU/JeYcorUoChYVaCSTPzQyZAZrXK8SYj7Gw/s640/rey.png)](http://stardew.farm/56f364621700d420d15f6e7d)

  
I'm also learning a bit more about how people write games. I had this fantasy that games were something _different_. At the end of the day it seems like a game is just a program like the kind I'm used to write - perhaps even a simple one. Everything is readily available in memory and the goal is to display things in a 2D or 3D space. It's interesting, but not as hard as I thought. Perhaps programming those high FPS multiplayer games is still a big challenge?  
  
I also put to use some knowledge of systems I haven't used in 10 years, like apache2 reverse proxies. Apache worked better in this case than nginx and haproxy, because it has a better _[mod\_pagespeed](https://developers.google.com/speed/pagespeed/module/)_ module. mod\_pagespeed is a cool piece of middleware that accelerates almost everything about a website. It optimizes and caches images, minifies CSS and Javascript, it can bundle different assets into one file, or inline code into HTML and do many other performance optimizations. It has both an apache and an nginx version, but the nginx once needs a recompilation of nginx. (It's kinda crazy that Google would ship software that makes people use custom-compiled binaries. People typically don't update them for security patches).  
  
I work with large scale systems on my day job, but it's funny how the same lessons apply to the small scale. I'm working on a tight budget for _stardew.farm _and I'm not gonna pay through these nose for some fancy expensive elastic cloud hosting. I'm using small servers and I'm gonna squeeze as much usage out of them as I can. Luckily there are very cheap CDNs these days that can soak up almost all traffic so you don't have to. Even better, they serve assets closer to users which means the site will load up faster for them.  
  
It's easy to setup the CDN bits, but I had to relearn some lessons about static asset caching and invalidation. My solution was to create a shell script :-P. It that makes a hermetic deployment of the website and changes all assets' URL at once to match what's in the HTML. So the user doesn't see a partial/broken site deployment and everything is cached consistently by browsers and CDNs.  
  
I always wanted to write a game. I'm probably not going to do that ever. I don't have [ConcernedApe](https://twitter.com/ConcernedApe)'s talent. In the meantime, I'm happy enough with just sharing screenshots of random people's fictitious farms for his game.