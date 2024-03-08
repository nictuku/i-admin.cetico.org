---
title: 'How to crack Windows Terminal Services 3 month trial'
date: 2005-06-06T22:18:00.001-07:00
draft: false
url: /2005/06/how-to-crack-windows-terminal-services.html
---

HOW TO RESET WINDOWS TERMINAL SERVICES 3 MONTH TRIAL  
Warning: greyhat content.  
  
Thanks to a Microsoft article, I've found out that it is very easy to extend your Windows Terminal Services 3-month trial or experience time. It's so easy that I'm sure many administrators have done this in their own systems, while waiting for their managers or financial staff to buy the definitive licenses (not being hypocrite here).  
  
Following these instructions, you don't have to crack any program. You won't mess with your system. The magic is to only delete the licenses databases both in the server and the clients, and let windows re-create them for you.  
  
  
\----  
  
Make sure you've installed "Terminal Services Licensing". By the way, this method only makes sense if you need to use Terminal Services in Application Mode, which is the one that requires licensing.  
  
The idea is quite simple.  
  
First, disconnect all users from the terminal. If you need to do this remotely, you can also disconnect yourself, and access the files remotely.  
  
Go to %WINDIR%\\system32\\lserver  
  
Notice the file TSLIC.edb. Rename it to tslic.old  
  
In every client computer, remove the CAL TS registry keys, located at:  
  
HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\MSLicensing  
  
Restart your server.  
  
  
Finally, if you need more information about how to crack terminal services, [go check at Microsoft's web site.](http://support.microsoft.com/?id=839878) See the "Cause 2" steps. :-)  
  
Shouldn't MS improve the security for TS Licensing in the next versions of Windows (2003 with SP1 is also easy to "crack")?  
  
PS> I am definitely against software piracy. I strongly believe that Microsoft should strength their anti-piracy policy. Only then they could theoretically lower their licensing prices. The biggest result, though, would be a huge increase of free software popularity. Only then we, FOSS proponents, would have the opportunity to prove our paradigm is superior (now I'm being hypocrite, I guess).  
  
UPDATE: Since Windows 2003, instead of following the complex process above, you can just switch to "Per User" mode. As long as you have a TS Licensing server up, it should work, even if you don't have license. If that's not the case please leave a comment below.