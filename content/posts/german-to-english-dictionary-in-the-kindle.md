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
# Archived Comments

#### By the way, I need to say that this dictionary is ...
[Yves Junqueira](https://www.blogger.com/profile/00104361785049371212 "noreply@blogger.com") - <time datetime="2010-09-27T15:46:36.723-07:00">Sep 1, 2010</time>

By the way, I need to say that this dictionary is very limited so dont get too excited.
<hr />
#### Thanks...at least its a step in the right direction!
[Anonymous]( "noreply@blogger.com") - <time datetime="2010-10-03T16:01:40.894-07:00">Oct 1, 2010</time>

Thanks...at least its a step in the right direction!
<hr />
#### This post was super helpful to me (i managed to cr...
[J. Beaman](https://www.blogger.com/profile/12328151042803228884 "noreply@blogger.com") - <time datetime="2010-10-04T14:23:27.914-07:00">Oct 1, 2010</time>

This post was super helpful to me (i managed to create a working Portuguese-English dictionary). I have one small problem: all the entries run together. But that is really a small problem. Thanks for the help!
<hr />
#### I read about your adventure in MobileRead. In pyg...
[Jordi Balcells](https://www.blogger.com/profile/00200189720838442406 "noreply@blogger.com") - <time datetime="2010-10-20T09:16:58.470-07:00">Oct 3, 2010</time>

I read about your adventure in MobileRead.  
  
In pyglossary, in the "Convert" tab, I choose "Read from format XDXF" and then "Write to format Tabfile (txt, dic)". Is this what you did?  
  
The resulting tab-delimited file has corrupted the entries by mixing them up.
<hr />
#### Never mind. The dictionary I was trying to convert...
[Jordi Balcells](https://www.blogger.com/profile/00200189720838442406 "noreply@blogger.com") - <time datetime="2010-10-20T10:38:52.193-07:00">Oct 3, 2010</time>

Never mind. The dictionary I was trying to convert was malformed, both in XDXF and DCT.  
  
It was the Brazilian Portuguese > English, which I guess J. Beaman was also trying to convert. The Portuguese > English works beautifully, though.  
  
Thanks for your how-to!
<hr />
#### I'm glad it helped. Maybe you could share it t...
[Yves Junqueira](https://www.blogger.com/profile/00104361785049371212 "noreply@blogger.com") - <time datetime="2010-10-22T04:57:09.820-07:00">Oct 5, 2010</time>

I'm glad it helped. Maybe you could share it the generated file with other people? ;-)
<hr />
#### Sure! I created a detailed how-to in Spanish right...
[Jordi Balcells](https://www.blogger.com/profile/00200189720838442406 "noreply@blogger.com") - <time datetime="2010-10-22T12:40:58.116-07:00">Oct 5, 2010</time>

Sure! I created a detailed how-to in Spanish right here: http://www.lectoreselectronicos.com/showthread.php?6615-%5BTuto%5D-Convertir-diccionarios-Stardict-a-MobiPocket&p=71723&viewfull=1#post71723  
  
The resulting Portuguese > English MOBI dictionary is attached to that message.
<hr />
#### Hi can you write how I can do .xdxf dictionary fro...
[Anonymous]( "noreply@blogger.com") - <time datetime="2010-11-12T00:26:57.593-08:00">Nov 5, 2010</time>

Hi can you write how I can do .xdxf dictionary from startdict format?  
  
I cant find dictionary English-Czech  
  
THX!
<hr />
#### They keep changing the URL of the XDXF dictionarie...
[Yves Junqueira](https://www.blogger.com/profile/00104361785049371212 "noreply@blogger.com") - <time datetime="2010-11-12T06:12:06.442-08:00">Nov 5, 2010</time>

They keep changing the URL of the XDXF dictionaries. Currently it's here:  
  
http://xdxf.revdanica.com/down/  
  
If they change again, search for \[startdict xdxf download\] at Google.
<hr />
#### I cannot be grateful enough!! :D I was getting tir...
[radikian](https://www.blogger.com/profile/00686422728032205448 "noreply@blogger.com") - <time datetime="2010-11-18T11:13:38.177-08:00">Nov 4, 2010</time>

I cannot be grateful enough!! :D I was getting tired of stopping to read to look up a word in a dictionary.. xD
<hr />
#### hi! y have a problem, When I type: tab2opf.py file...
[Anonymous]( "noreply@blogger.com") - <time datetime="2010-12-08T15:03:34.630-08:00">Dec 3, 2010</time>

hi! y have a problem, When I type: tab2opf.py filename.txt, y get this message: print "tab2opf (Stardict->MobiPocket)"  
SyntaxError: invalid syntax  
  
please help!! thanks
<hr />
#### What about the Pocket Oxford Duden? Will this proc...
[Steve](https://www.blogger.com/profile/18200414328225312698 "noreply@blogger.com") - <time datetime="2011-01-06T17:18:51.199-08:00">Jan 5, 2011</time>

What about the Pocket Oxford Duden? Will this process also work for that? http://www.mobipocket.com/en/eBooks/eBookDetails.asp?BookID=4382
<hr />
#### Just answered my own question. Even though the sam...
[Steve](https://www.blogger.com/profile/18200414328225312698 "noreply@blogger.com") - <time datetime="2011-01-06T18:26:18.788-08:00">Jan 5, 2011</time>

Just answered my own question. Even though the sample has German to English, that dictionary (Pocket Oxford Duden) is definitely only English to German. Waste of $9. :(
<hr />
#### Hi Yve Junquera, I just bought a german-spanish d...
[JMiguel](https://www.blogger.com/profile/05684387726021503276 "noreply@blogger.com") - <time datetime="2011-01-19T04:21:41.719-08:00">Jan 3, 2011</time>

Hi Yve Junquera,  
  
I just bought a german-spanish dictionary thinking that I could use it in the Kindle. But no no. DRM is in the way.  
  
Could you please tell the dummies how to get rid of the DRM so it can used on the Kindle.  
  
Thanks a lot.  
  
JM
<hr />
#### Thanks for the dictionary. Is there something bett...
[Nuvola](https://www.blogger.com/profile/08546430836758926152 "noreply@blogger.com") - <time datetime="2011-01-31T08:39:21.456-08:00">Jan 1, 2011</time>

Thanks for the dictionary. Is there something better now. Perhaps that recognises capitalised words?  
REgards
<hr />
#### Is there a possible way to make an english-greek d...
[Γιάννης Τζενεβράκης](https://www.blogger.com/profile/08456123824623109317 "noreply@blogger.com") - <time datetime="2011-02-05T20:44:15.730-08:00">Feb 0, 2011</time>

Is there a possible way to make an english-greek dict. for kindle with babylon free bgl dictionaries??  
  
plz respond to this.  
thanks.
<hr />
#### Thanks to everyone there is a torrent going around...
[Anonymous]( "noreply@blogger.com") - <time datetime="2011-04-05T13:29:24.572-07:00">Apr 2, 2011</time>

Thanks to everyone there is a torrent going around search "69 retail" which should give a German to English dictionary. But it doesn't the original seeder has also commented this. It seems like a GOOD! Mobi format German to English dictionary is proving very difficult to get hold of!!!!!!
<hr />
#### Out of all dicts...only ger-eng is missing in the ...
[Anonymous]( "noreply@blogger.com") - <time datetime="2011-06-06T22:48:46.493-07:00">Jun 2, 2011</time>

Out of all dicts...only ger-eng is missing in the torrent....
<hr />

