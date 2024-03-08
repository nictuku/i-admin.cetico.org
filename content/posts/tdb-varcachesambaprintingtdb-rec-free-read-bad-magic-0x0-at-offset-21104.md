---
title: 'tdb(/var/cache/samba/printing.tdb): rec_free_read bad magic 0x0 at offset=21104'
date: 2005-06-15T16:47:00.000-07:00
draft: false
url: /2005/06/tdbvarcachesambaprintingtdb.html
---

\[2005/06/15 20:33:36, 0\] tdb/tdbutil.c:tdb\_log(531) tdb(/var/cache/samba/printing.tdb): rec\_free\_read bad magic 0x0 at offset=21104  
  
If you find something like this in your logs, and you can't print, just remove the file and restart samba.  
  
Visit my sponsors if you find this information and their ads useful hehe.
# Archived Comments

#### Worked for me.
[Anonymous]( "noreply@blogger.com") - <time datetime="2006-08-01T13:29:00.000-07:00">Aug 2, 2006</time>

Worked for me.
<hr />
#### Worked for me to, thanks :)  
(be sure te restart...
[Anonymous]( "noreply@blogger.com") - <time datetime="2008-08-28T07:53:00.000-07:00">Aug 4, 2008</time>

Worked for me to, thanks :)  
(be sure te restart samba after the delete:  
/etc/init.d/smb restart  
)
<hr />

