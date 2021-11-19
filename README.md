# CLI DNSBL checker

Cron friendly CLI [DNSBL][] checker utility.

### Introduction

Check if a hostname or IP address is blacklisted by any of
the [DNSBL][] sevices commonly subscribed by SPAM filters.

### Requires

[Python 3.9+](https://www.python.org/dev/peps/pep-0584/) with [PyYAML](https://PyYAML.org).

### Installation

```sh
git clone https://github.com/yds/dnsbl.git
cp dnsbl/dnsbl.yml /etc/
cp dnsbl/dnsbl /usr/local/bin/
chmod 0555 /usr/local/bin/dnsbl
```

### Usage

Edit and install the included `crontab` file:
```crontab
MAILFROM=BlackMail@example.com
MAILTO=BlackMail@example.com
PATH=~/bin:/usr/local/bin:/usr/bin:/bin
#
#minute	hour	mday	month	wday	command
#
15,45	*	*	*	*	dnsbl example.com
```
 - Change the email address where **BLACKLISTED** alerts will be sent _to_ and _from_.
 - Change `dnsbl` target host address to the FQDN or IP address you need to scan.

### Examples

Use with FQDN or IP address
```sh
dnsbl -v example.com
dnsbl -v 118.26.173.212
```

### Sample output

    $ dnsbl -v 118.26.173.212
    ...
    [Errno 8] Name does not resolve [not listed] 212.173.26.118.black.mail.abusix.zone.
    [Errno 8] Name does not resolve [not listed] 212.173.26.118.dblack.mail.abusix.zone.
    [Errno 8] Name does not resolve [not listed] 212.173.26.118.exploit.mail.abusix.zone.

            118.26.173.212 is BLACKLISTED:

    https://MultiRBL.Valli.org/lookup/118.26.173.212.html
    https://HetrixTools.com/blacklist-check/118.26.173.212

    Hostkarma blacklist
    http://wiki.junkemailfilter.com/index.php/Spam_DNS_Lists
    [127.0.1.1]     212.173.26.118.hostkarma.junkemailfilter.com.
    [127.0.0.3]     212.173.26.118.hostkarma.junkemailfilter.com.

    Mailspike Rep
    http://mailspike.org/
    [127.0.0.17]    212.173.26.118.rep.mailspike.net.

    Barracuda Reputation Block List
    http://www.barracudacentral.org/rbl/
    [127.0.0.2]     212.173.26.118.b.barracudacentral.org.

    Barracuda Reputation Block List (for SpamAssassin)
    http://www.barracudacentral.org/rbl/
    [127.0.0.2]     212.173.26.118.bb.barracudacentral.org.

### Blacklist providers

The distributed `dnsbl.yml` file of [DNSBL][] zones to query was compiled
from scraping https://HetrixTools.com/blacklist-check/ then running
`dnsbl -l` to merge http://MultiRBL.Valli.org/list/ "alive" zones and
prune any zones from the "dead" section.

Running `dnsbl -m` will output a YAML format listing of all the IPv4
"blacklist" [DNSBL][] zones in the "alive" section of
http://MultiRBL.Valli.org/list/

Running `dnsbl -l` will output a YAML format listing of all the zones
in the local `/etc/dnsbl.yml` file **plus** all the IPv4 "blacklist"
[DNSBL][] zones in the "alive" section of http://MultiRBL.Valli.org/list/
**minus** any matching [DNSBL][] zones from the "dead" section.

If your `/etc/dnsbl.yml` is upto date then running:
```sh
dnsbl -l > /tmp/dnsbl.yml
diff -u /etc/dnsbl.yml /tmp/dnsbl.yml
```
should **not** produce _any_ output. Otherwise you will see a unified `diff`
which you can merge with your production `/etc/dnsbl.yml` as need be.

### Prior Art

 - [blacklist-check-unix-linux-utility](https://github.com/adionditsak/blacklist-check-unix-linux-utility)
 - [MultiRBL.Valli.org](https://MultiRBL.Valli.org)
 - [HetrixTools.com](https://HetrixTools.com/blacklist-check/)

### License

[MIT](https://GitHub.com/yds/dnsbl/blob/master/LICENSE "MIT open source")

[DNSBL]:https://en.wikipedia.org/wiki/Domain_Name_System-based_blackhole_list "Domain Name System-based blackhole list"
