---
title: 'German to English dictionary in the Kindle'
date: 2010-09-27T15:05:00.001-07:00
draft: true
url: 
---

I wanted to read books in German in my Kindle, but because I don't know any German, I need a dictionary. If that's what you want, a dictionary for German that works in the Kindle, then download this:

*   [German to English dictionary converted from StarDict, ready to be used as a](http://www.blogger.com/goog_615831286) _[default dictionary](http://www.blogger.com/goog_615831286)_[ in Kindle 3](http://cetico.org/Dictionary_de-en.mobi.gz) (Available under the terms of the GPL)

Long version:

  

I went through a lot of problem converting the [StarDict dictionary of German to English](http://stardict.sourceforge.net/Dictionaries_dictd-www.freedict.de.php). It really took me days. So let me dump the process here, roughly:

*   download the stardict file. I believe the extension is .xdxf
*   use pyglossary to convert it to a tab-separate text file
*   use [tab2opf from this dude](http://www.klokan.cz/projects/stardict-lingea/) to convert the text file into an OPF set of files (.opf plus a bunch of HTMLs)
*   go to [http://www.mobipocket.com/dev/](http://www.mobipocket.com/dev/) and download the proprietary "kindlegen" tool
*   use kindlegen on the .opf file. If all goes right, you'll get a .mobi file at the end.

After that, put the mobi file in the Kindle (I use Calibre for that) and make it the default dictionary, by going to Home, then Menu, Settings, then Menu, then Change Primary Dictionary.

  

There's a big catch. They clearly tune the Kindle to use as less power as possible. So it makes sense for the English language to save a few CPU cycles by skipping dictionary lookups when the word starts with an uppercase letter in the middle of a phrase. Like for example if in this phrase: "I think Amazon sucks at making internalized products". In English, a dictionary lookup for "Amazon" doesn't make sense.

  

As you, a smart reader, knows, the German language uses capitalized letters for all nouns. So it's effectively impossible to lookup the meaning of for example _Weihnachten_ (christmas). The only workaround I could think it's to edit the content of the book and search all capitalized letters by lower case ones.

  

Or, alternatively, stop wasting time tuning the Kindle and go **study** German so you don't need a dictionary in the first place :-).