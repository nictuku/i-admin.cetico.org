---
title: 'Setting the default locale (language) in TurboGears'
date: 2007-07-25T21:02:00.000-07:00
draft: false
url: /2007/07/setting-default-locale-language-in.html
tags: 
- turbogears
---

The TurboGears [documentation](http://docs.turbogears.org/1.0/Internationalization) only mentions this en passant. It's easy to change the default locale (language) of your TG application.  
  
In your project/controllers.py, in you can add:  
  

> def locale\_pt():  
> return 'pt'  
>   
> turbogears.config.update({'i18n.get\_locale' : locale\_pt})

  
  
I have this in the top namespace of my controllers.py file, not inside the Root class. I'm not sure if this is the prettiest way to make this work, but hey, it works for me. If you have any better suggestion, please leave a comment.
