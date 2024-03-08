---
title: 'tail-nagios information and source code'
date: 2005-05-24T14:12:00.000-07:00
draft: false
url: /2005/05/tail-nagios-information-and-source.html
---

Hi.  
  
Some fellows from nagios-users list have requested more information about my solution to integrate nagios 2.0 with mysql, so here it is.  
  
Since nagios 2.0 doesn't have built-in support for mysql, we have to find another way to integrate this software with a custom interface that reads a database.  
  
A way to do that is to use service performance data (service\_perfdata).  
  
You'll need to setup service\_perfdata\_file to a "log" of service perfomance data, and design a service\_perfdata\_file\_template.  
  
In my case, I used:  
  
service\_perfdata\_file=/usr/local/nagios/var/service-perfdata  
service\_perfdata\_file\_template=\[SERVICEPERFDATA\]\\t$TIMET$\\t$HOSTNAME$\\t$SERVICEDESC$\\t$SERVICESTATE$\\t  
$SERVICEEXECUTIONTIME$\\t$SERVICELATENCY$\\t$SERVICEOUTPUT$  
\\t$SERVICEPERFDATA$\\t$SERVICESTATETYPE$  
  
(singe line)  
  
Then, after restarting nagios, and if you don't forget to check permissions for that perdata file, nagios will start to append service check output to this file.  
  
The next step is to read this file and update a database as we want.  
  
In my case, I've designed a simple table that will list every server and service and their latest status combined in a "object" row.  
  
  
\> describe status;  
+--------+--------------------------+------+-----+---------+-------+  
Field Type Null Key Default Extra  
+--------+--------------------------+------+-----+---------+-------+  
objeto varchar(45) PRI  
tipo varchar(45)  
status varchar(45)  
output varchar(255)  
time int(16) unsigned YES NULL  
hard int(1) unsigned zerofill 0  
+--------+--------------------------+------+-----+---------+-------+  
  
  
\> select \* from status limit 1;  
+-----------------------+---------+--------+------------------------------------------------------+------------+------+  
objeto tipo status output time hard  
+-----------------------+---------+--------+------------------------------------------------------+------------+------+  
adsl-router.LINK\_ADSL servico OK HTTP OK HTTP/1.0 200 OK - 1.113 second response time 1116971094 1  
+-----------------------+---------+--------+------------------------------------------------------+------------+------+  
  
  
"objeto" means "object" and "tipo" means "type" (unused, actually).  
  
  
  
  
The script to read the logs and update the database is:  
  
\---------------------------------------------------------------  
#!/usr/bin/perl -w  
  
\# tail-nagios  
\# v. 0.001  
\# Yves Junqueira - yves.junqueira AT gmail.com  
\# License: GPL  
  
use strict;  
  
use DBI;  
use File::Tail;  
  
my $driver = "mysql";  
my $user = "nagios";  
my $password = "mypass";  
my $database = "nagios";  
  
my $oldlogs = 0;  
my $verbose = 0;  
  
my $name = '/usr/local/nagios/var/service-perfdata';  
my $file = File::Tail->new(  
name => $name,  
interval => 3,  
adjustafter => 5,  
tail => -1  
);  
  
print "Tail Nagios\\n";  
my $dsn = "DBI:$driver:dbname=$database";  
  
my $dbh =  
DBI->connect( $dsn, $user, $password, { RaiseError => 1, AutoCommit => 1 } )  
or die "could not connect $!";  
  
my $sth =  
$dbh->prepare(qq\[select time, objeto from status order by time DESC LIMIT 1\]);  
  
$sth->execute();  
  
my ( $lasttimestamp, $lastobjeto ) = $sth->fetchrow;  
  
$lasttimestamp = 0;  
$lastobjeto = 0;  
  
my $count = 0;  
my $nextok = 0;  
my $lastcommit = 0;  
  
my $sth2 = $dbh->prepare(  
qq\[  
replace into status (objeto, tipo, status, output, time, hard) values  
(?,?,?,?,?,?)\]  
);  
  
while ( defined( $\_ = $file->read ) ) {  
  
#\[SERVICEPERFDATA\] 1111058462 roteador-cpd1 PING OK  
\# 4.008 0.135 PING OK - Packet loss = 0%, RTA = 1.32 ms  
s/\[\\n\\r\]//g;  
  
my (  
$i, $time, $maquina, $servico, $status,  
$checkduration, $latency, $output, $etc, $statetype  
)  
\= split /\\t/;  
  
if ( ( $time < $lasttimestamp ) && ( $oldlogs != 1 ) ) { #print "."; } else { if ( ( $time > $lasttimestamp ) ( $oldlogs == 1 ) ) {  
  
my $hard = ( $statetype eq "HARD" ) ? 1 : 0;  
  
if ( $verbose >= 1 ) { print "Insert this!\\n"; }  
$sth2->execute( $maquina . "." . $servico,  
'servico', $status, $output, $time, $hard )  
or die "Argh $!";  
}  
}  
}  
if ( $verbose >= 1 ) { print "Finish. Will commit\\n"; }  
print "1\\n";  
$dbh->commit()  
or print "oh no! $!\\n"  
; # I don't even need this since AutoCommit is on. But someday I can turn it on...  
print "2\\n";  
$sth->finish() or print "sth finish booo $!\\n";  
print "3\\n";  
$sth2->finish() or print "sth2 finish aaaaaaaaaa $!\\n";  
  
print "4\\n";  
$dbh->disconnect();  
print "end\\n";  
  
#1;  
  
  
\---------------------------------------------------------------  
  
This script is not a daemon actually, but I run the following script in the crontab, that checks if it's running, and starts tail-nagios if necessary.  
  
\-------------------- tnagios-monitor.sh -----------------------  
#!/bin/sh  
pidof -x tail-nagios 2>&1 > /dev/null (echo "Starting tail-nagios" ; /usr/local/bin/tail-nagios &)  
  
\---------------------------------------------------------------  
  
A desirable /etc/crontab follows:  
  
\*/5 \* \* \* \* root /usr/local/sbin/tnagios-monitor.sh  
  
  
Well that's almost all you need.  
  
Sorry if it's a mess, but it serves my needs.  
  
If you need more information, or maybe is interested in the perl+apache user interface that shows services status, please let me know.