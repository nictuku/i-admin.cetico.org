---
title: 'how to liberate O''Reilly books acquired from the Android Market'
date: 2010-09-16T15:22:00.001-07:00
draft: false
url: /2010/09/how-to-liberate-oreilly-books-acquired.html
---

_this post may be useful to people out there trying to read on a Kindle the O'Reilly books they acquired from the Android Market_  
  
O'Reilly is a competent, decent and not-evil publisher of technical books. They know that DRM only causes pain to good costumers, because bad customers will find other ways to access/steal their books. I therefore feel respected as a customer and retribute by buying lots of e-books from them :-).  
  
The cheapest way to get e-books from O'Reilly is, besides the very unfriendly O'Reilly Safari bookshelf, to buy them via Android Market. Books acquired in the Market come bundled with a crippled version of Aldiko. Most of the time, that's good enough. But, there are two problems:  
  

*   I want to have a backup of the books that I legally acquired. If in X years I don't have an android phone anymore, or maybe if I lose access to my gmail account, I won't be able to re-download the books.
*   I believe it's legal and fair that I should be able to read this book in other devices that I own. Recently I bought a Kindle, and it become my main ebook reading device. I don't think it's reasonable to expect that I'm going to buy those books all over again, this time from the Amazon Kindle book shop.

Optionally, I could have bought these books from the Amazon Kindle bookstore and read them using the Amazon Kindle for Android. That's not ideal, though. First, it's more expensive. Second, these books are DRM protected and I would only be able to read them using a Kindle software. I don't want that.  
  
So instead, I found a way to copy the DRM-free O'Reilly books that I bought from the Android Market. It's simple:  

1.  Install ASTRO file manager from the Android Market
2.  Open ASTRO, go to Tools and Application Manager/Backup
3.  Select the applications referring to the books, then hit backup. The ".apk" files will be copied to your sdcard.
4.  Connect the phone via USB to a computer and copy the files from the /backups directory. Alternatively, use ASTRO itself to "Send" the apk from /sdcard/backups to your email, then download it to your computer.
5.  The APK files are really zip files. If you want, you can simply backup these apk files. That's what I do.
6.  If you want to open the book in another device that reads epub books, then unzip the apk file, enter the directory called assets, then zip the contents of this directory into a .zip file. Rename the zip file to .epub, and you're done.

good luck!