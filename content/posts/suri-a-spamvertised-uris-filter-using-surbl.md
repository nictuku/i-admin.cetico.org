---
title: 'Suri, a Spamvertised URIs filter using SURBL'
date: 2004-09-22T12:28:00.000-07:00
draft: false
url: /2004/09/suri-spamvertised-uris-filter-using.html
---

![Image Hosted by ImageShack.us](http://img75.exs.cx/img75/1496/camelo.png)I am making a pair of postfix (and related) tools in perl that could be useful for some people, in some cases.  
  
One is a "[SURBL](http://www.surbl.org)" technique filter to be plugged into amavis, that will check for spamvertised URI's against a "SURBL" server. It acts as an antivirus, checking for the content of the message. If it's configuerd for denial, it could lead to false positives, if the SURBL list in used is not very precise.

  
  
It was written based on a [qpsmtpd plugin](http://devin.com/qpsmtpd/uribl) developed by Devin Carraway.  
  
The other, which is in early stages of development, has the same objective, but is supposed to be used as a transparent (or not) SMTP proxy for postfix. Messages will be filtered and content will be DENIED in real time, so the sender will know the message was not delivered. Spammers don't care about smtp error codes, and real senders will be notified of the error.  
  
I am aware that using transparent proxy is a bad idea in very loaded servers, so I am making different tools for different needs.  
  
These tools aren't of much use to most people, they're mostly a lab for learning perl. Thus, I am not willing to open a project at sourceforge just for them. So, I'll just paste the code them here. If this tools proves itself useful, please leave a feedback comment. Thanks.  

* * *

  
```
#!/usr/bin/perl -w
  
\# Suri, a Spamvertised URIs filter. 
  
\# Suri Copyright 2004, Yves Junqueira <yves.junqueira at gmail.com>
  
\# uribl Copyright 2004, Devin Carrway <qpsmtpd@devin.com>
  
\# Distributed under the GNU Public License
  
\# or the Perl Artistic License
  
#
  
\# Suri is a SpamVertisedURIs check script that should be called
  
\# from amavisd-new or any other antivirus frontend that can
  
\# use tcp socks to connect to the daemon and trigger the checks.
  
#
  
\# When a client (in this case, amavis) connects to it and issue
  
\# a "SCAN /dir" command, it will scan that dir's files screening
  
\# for messages containing SpamVertisedURIs.
  
#
  
\# When it finds a "(http|ftp|etc)://sub.domain.tld/ it will check
  
\# the SURBL server if that domain or subdomain is blacklisted.
  
#
  
\# Suri runs as a pre-forked daemon, so it's supposed to perform
  
\# well and not use many resources.
  
#
  
\# As in any other RBL checks, you shouldn't rely on remote
  
\# RBL servers if you do many checks. Grab a SURBL zone if you
  
\# can, and check against a local rbldnsd.
  
#
  
\# Beware that mail classified using Suri are considered VIRUS,
  
\# so you should be careful with false positives. I suggest
  
\# using only trustful SURBL databases and that you quarantine
  
\# viruses. Or you can set a non-"deny" value to $action to just
  
\# see WARNNING logs at syslog. 
  
\# 
  
\# New versions probably in http://i-admin.blogspot.com
  
#
  
\# Usage:
  
  
\# 1) Insert this after your main antiviruses 
  
\# in @av\_scanners, at amavisd.conf
  
#
  
\#    \['Suri',  \\&ask\_daemon,
  
\#    \["SCAN {}/../email.txt\\n", '127.0.0.1:20098'\],
  
\#     qr/^OK/, qr/^DENY/, qr/^SPAMMEDURL:.\*\[(\](.+)\[)\]/
  
\#     \],
  
#
  
\# 2) Run suri. 
  
\# 3) Restart amavisd
  
  
###################################################
  
\# Options
  
my $version           \= '0.8';
  
my $min\_servers       \= 2;
  
my $max\_spare\_servers \= 2;
  
my $user              \= 'clamav';
  
my $group             \= 'clamav';
  
my $port              \= 20098;
  
my @uribl\_zones \= ('surblgoiano.com.br');
  
my $action      \= "deny";
  
  
###################################################
  
  
use Net::DNS::Resolver;
  
my $dir;
  
my %sockets;
  
my $res \= new Net::DNS::Resolver or return DECLINED;
  
  
  
$res\->udp\_timeout(5);
  
  
package Suri;
  
  
use Unix::Syslog qw(:macros);
  
use Unix::Syslog qw(:subs);
  
use strict;
  
use vars qw(@ISA);
  
use Net::Server::PreFork;
  
  
@ISA \= qw(Net::Server::PreFork);
  
my $self \= bless(
  
    {
  
        'server' \=> {
  
            'user'              \=> $user,
  
            'group'             \=> $group,
  
            'min\_servers'       \=> $min\_servers,
  
            'max\_spare\_servers' \=> $max\_spare\_servers,
  
            'background'        \=> 1,
  
        }
  
    },
  
    'Suri'
  
);
  
  
openlog "suri", LOG\_PID | LOG\_PERROR, LOG\_INFO;
  
syslog LOG\_INFO, "Suri v$version starting.";
  
closelog;
  
  
$self\->run( port \=> $port );
  
exit;
  
  
sub process\_request {
  
    my $self \= shift;
  
  
    #import Unix::Syslog;
  
  
    eval {
  
    openlog "suri", LOG\_PID | LOG\_PERROR, LOG\_INFO;
  
  
        local $SIG{ALRM} \= sub { die "Timed Out!\\n" };
  
        my $timeout \= 30;
  
  
        my $previous\_alarm \= alarm($timeout);
  
        while (<STDIN\>) {
  
            $\_ \=~ s/\\r//g;
  
            if ( $\_ \=~ /SCAN (\\/.\*)/ ) {
  
                $dir \= $1;
  
                print "Scanning $dir\\n";
  
  
                syslog LOG\_INFO, "Scanning %s", $dir;
  
  
                my $result \= &check;
  
                \#                my $result = '';
  
                if ( $result \=~ /DENY/ ) { print "DENY\\n"; last; }
  
                else { print "OK. $result\\n"; last; }
  
            }
  
            else { print "Oops\\n"; }
  
            alarm($timeout);
  
        }
  
        alarm($previous\_alarm);
  
  
    };
  
  
    if ( $@ \=~ /timed out/i ) {
  
        print STDOUT "Timed Out.\\r\\n";
  
        return;
  
    }
  
    closelog;
  
}
  
  
sub check {
  
    openlog "suri", LOG\_PID | LOG\_PERROR, LOG\_INFO;
  
  
    while ( defined( my $arquivo \= glob($dir."\*") ) ) {
  
        next unless \-r $arquivo && \-r $arquivo;
  
        print "Checking $arquivo\\n";
  
        open( ARQUIVO, "<$arquivo" )        
  
          or print "Couldn't open file: $arquivo: $!";
  
        while (<ARQUIVO\>) {
  
  
            #print $\_; }
  
  
            chomp;
  
  
            \# Undo URI escape munging
  
            $\_ \=~ s/%(\[0-9A-Fa-f\]{2,2})/chr(hex($1))/ge;
  
  
            \# Undo HTML entity munging (e.g. in parameterized redirects)
  
            $\_ \=~ s/&#(\\d{2,3});?/chr($1)/ge;
  
  
            while (
  
                $\_ \=~ m{
  
 \\w{3,16}:/+                     # protocol
  
 (?:\\S+@)?                       # user/pass
  
 (\\d{7,})                        # raw-numeric IP
  
 (?::\\d+)?(\[/?\\s\]|$)             # port, slash
  
 #  or EOL
  
  }gx
  
              )
  
            {
  
                my @octets \= (
  
                    ( ( $1 \>> 24 ) & 0xff ),
  
                    ( ( $1 \>> 16 ) & 0xff ),
  
                    ( ( $1 \>> 8 ) & 0xff ),
  
                    ( $1 & 0xff )
  
                );
  
                my $fwd \= join( '.', @octets );
  
                my $rev \= join( '.', reverse @octets );
  
  
                \#      print("uribl: matched pure-integer ipaddr $1 ($fwd)" );
  
  
                $sockets{"$rev\\t$\_"} ||= $res\->bgsend( "$rev.$\_.", 'txt' )
  
                  for @uribl\_zones;
  
            }
  
            while (
  
                $\_ \=~ m{
  
 \\w{3,16}:/+                     # protocol
  
 (?:\\S+@)?                       # user/pass
  
 (\\d+|0\[xX\]\[0-9A-Fa-f\]+)\\.       # IP address
  
 (\\d+|0\[xX\]\[0-9A-Fa-f\]+)\\.
  
 (\\d+|0\[xX\]\[0-9A-Fa-f\]+)\\.
  
 (\\d+|0\[xX\]\[0-9A-Fa-f\]+)
  
  }gx
  
              )
  
            {
  
                my @octets \= ( $1, $2, $3, $4 );
  
  
                \# return any octal/hex octets in the IP addr back
  
                \# to decimal form (e.g. http://0x7f.0.0.00001)
  
                for ( 0 .. $#octets ) {
  
                    $octets\[$\_\] \=~ s/^0(\[0-7\]+)$/oct($1)/e;
  
                    $octets\[$\_\] \=~ s/^0x(\[0-9a-fA-F\]+)$/hex($1)/e;
  
                }
  
                my $fwd \= join( '.', @octets );
  
                my $rev \= join( '.', reverse @octets );
  
  
                #print( 8, "uribl: matched URI ipaddr $fwd" );
  
                $sockets{"$rev\\t$\_"} ||= $res\->bgsend( "$rev.$\_.", 'txt' )
  
                  for @uribl\_zones;
  
            }
  
            while (
  
                $\_ \=~ m{
  
 \\w{3,16}:/+                     # protocol
  
 (?:\\S+@)?                       # user/pass
  
 (\[\\w\\-.\]+\\.\[a-zA-Z\]{2,8})       # hostname
  
  }gx
  
              )
  
            {
  
                my $host         \= $1;
  
                my @host\_domains \= split /\\./, $host;
  
  
                #print( 8, "uribl: matched URI hostname $host" );
  
  
                while ( @host\_domains \>= 2 ) {
  
                    my $subhost \= join( '.', @host\_domains );
  
  
                    #print("URIBL: checking sub-host $subhost\\n" );
  
                    $sockets{"$subhost\\t$\_"} ||=
  
                      $res\->bgsend( "$subhost.$\_.", 'txt' )
  
                      for @uribl\_zones;
  
                    shift @host\_domains;
  
                }
  
            }
  
        
  
        }
  
        my %matches;
  
        while ( keys %sockets ) {
  
            my $c \= 0;
  
            for my $s ( keys %sockets ) {
  
                unless ( $sockets{$s} ) {
  
                    delete $sockets{$s};
  
                    next;
  
                }
  
                next unless $res\->bgisready( $sockets{$s} );
  
                my $packet \= $res\->bgread( $sockets{$s} );
  
                unless ($packet) {
  
                    delete $sockets{$s};
  
                    next;
  
                }
  
                for my $rr ( $packet\->answer ) {
  
                    $matches{$s} \= $rr\->txtdata
  
                      if $rr\->type eq 'TXT';
  
                }
  
                delete $sockets{$s};
  
                $c++;
  
            }
  
            sleep 0.1 if keys %sockets and !$c;
  
        }
  
        for ( keys %matches ) {
  
            my ( $host, $uribl ) \= split /\\t/, $\_;
  
            my $note \= "SPAMMEDURL: $host in $uribl ($matches{$\_})";
  
            print "\\n$note\\nAction: $action\\n";
  
           
  
            if ( $action eq 'deny' ) {
  
      syslog LOG\_INFO, "SPAMMEDURL: $host in $uribl ($matches{$\_}) Action: deny";
  
            return ("DENY $note");
  
                last;
  
            }
  
            else {
  
      syslog LOG\_INFO, "SPAMMEDURL: $host in $uribl ($matches{$\_}) Action: $action";
  
                return ("WARN: $note");
  
            } }
  
        }
  
return "OK";
  
    \#    return "WARN";
  
closelog;
  
}
  
  
1;
  

```  

* * *
# Archived Comments


