---
title: 'Fedora Core with Mysql 4'
date: 2004-09-10T21:23:00.000-07:00
draft: false
url: /2004/09/fedora-core-with-mysql-4.html
---

David Martínez was kind enough to put up a repository of MySQL 4 and further dependencies compiled for FC2.  
  
[http://dmnet.bitacoras.com/index.php?tb=2680](http://dmnet.bitacoras.com/index.php?tb=2680)  
  
You may get the files directly from:  
  
[http://yum.garsan.ws/fedora/2/RPMS/RPMS.dmnet/](http://yum.garsan.ws/fedora/2/RPMS/RPMS.dmnet/)  
  
These packages fixed a very ugly behaviour I was getting with my mixed mysql lib's.  
Every time I ran a perl with a MySQL DBI, it ended with Segmentaion Fault.  
Now it's fine. Thanks David!