# CLI DNSBL checker

Cron friendly CLI [DNSBL][] checker utility.

### Introduction

Check if a hostname or IP address is blacklisted by any of
the [DNSBL][] sevices commonly subscribed by SPAM filters.

### Requires

 - [Python 3.8+](https://www.python.org)
 - [dnspython2](https://www.dnspython.org)
 - [PyYAML](https://PyYAML.org)

### Installation

```sh
git clone https://github.com/yds/dnsbl.git
cp dnsbl/dnsbl.yml /etc/
cp dnsbl/dnsbl /usr/local/bin/
chmod 0555 /usr/local/bin/dnsbl
```

### Usage

Edit and install the included `crontab` file:
```sh
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
```yaml
$ dnsbl -v 118.26.173.212
...
The DNS query name does not exist: A 212.173.26.118.black.junkemailfilter.com.
The DNS response does not contain an answer to the question: TXT 212.173.26.118.hostkarma.junkemailfilter.com.
The DNS operation timed out after 5.001934766769409 seconds: A 212.173.26.118.ip.v4bl.org.
The DNS query name does not exist: A 212.173.26.118.dnsbl.calivent.com.pe.
The DNS response does not contain an answer to the question: A 212.173.26.118.forbidden.icm.edu.pl.
The DNS query name does not exist: A 212.173.26.118.88.blocklist.zap.
All nameservers failed to answer the query: A 212.173.26.118.black.mail.abusix.zone.

	118.26.173.212 is BLACKLISTED:

https://MultiRBL.Valli.org/lookup/118.26.173.212.html
https://HetrixTools.com/blacklist-check/118.26.173.212

Hostkarma blacklist
http://wiki.junkemailfilter.com/index.php/Spam_DNS_Lists
[127.0.1.1]	212.173.26.118.hostkarma.junkemailfilter.com.
[127.0.0.3]	212.173.26.118.hostkarma.junkemailfilter.com.

Mailspike Rep
http://mailspike.org/
a=666;t=666;s=1628295936;r=2
[127.0.0.17]	212.173.26.118.rep.mailspike.net.

Barracuda Reputation Block List
http://www.barracudacentral.org/rbl/
http://www.barracudanetworks.com/reputation/?pr=1&ip=118.26.173.212
[127.0.0.2]	212.173.26.118.b.barracudacentral.org.

Barracuda Reputation Block List (for SpamAssassin)
http://www.barracudacentral.org/rbl/
http://www.barracudanetworks.com/reputation/?pr=1&ip=118.26.173.212
[127.0.0.2]	212.173.26.118.bb.barracudacentral.org.
```
Without the _verbose_ `-v` option only the BLACKLISTED section is
printed if there is anything blacklisted, otherwise there is no
output to keep `cron` quiet.

For each failed zone the BLACKLISTED section prints a label, any
URLs associated with that blacklist and the returned error code(s).

All the error messages printed with the _verbose_ `-v` option above
the BLACKLISTED section indicate the IP address is **NOT** blacklisted
in those zones.

### Blacklist providers

The distributed `dnsbl.yml` file of [DNSBL][] zones to query was compiled
from scraping https://HetrixTools.com/blacklist-check/ then running
`dnsbl -l` to merge http://MultiRBL.Valli.org/list/ "alive" zones and
prune any zones from the "dead" section.

Running `dnsbl -m` will output a [YAML][] format listing of all the IPv4
"blacklist" [DNSBL][] zones in the "alive" section of
http://MultiRBL.Valli.org/list/

Running `dnsbl -l` will output a [YAML][] format listing of all the zones
in the local `/etc/dnsbl.yml` file **plus** all the IPv4 "blacklist"
[DNSBL][] zones in the "alive" section of http://MultiRBL.Valli.org/list/
**minus** any matching [DNSBL][] zones from the "dead" section.

If your `/etc/dnsbl.yml` is up to date then running the following should
**not** produce _any_ output:
```sh
dnsbl -l > /tmp/dnsbl.yml
diff -u /etc/dnsbl.yml /tmp/dnsbl.yml
```
Otherwise you will see a unified `diff` which you can merge with your
production `/etc/dnsbl.yml` as need be.

### Config file format

`/etc/dnsbl.yml` is a [YAML][] dict with the `keys` made up of
[DNSBL][] zones to `query`. Optionally each `keys`' value _should_
have a URL followed by a tab `\t` and a description of the [DNSBL][].
The URL _may_ have a `{}` placeholder which if present will be
replaced by the blacklisted IP address in the output. Standard
[YAML][] comments and empty values are allowed.

Both `-l` and `-m` output a valid config file sorted by reverse
domain name. This sorting groups related zones together and allows
for `diff`ing against the installed `/etc/dnsbl.yml` config file.

### Prior Art

 - [blacklist-check-unix-linux-utility](https://github.com/adionditsak/blacklist-check-unix-linux-utility)
 - [MultiRBL.Valli.org](https://MultiRBL.Valli.org)
 - [HetrixTools.com](https://HetrixTools.com/blacklist-check/)

### License

[MIT](https://GitHub.com/yds/dnsbl/blob/master/LICENSE "MIT open source")

[DNSBL]:https://en.wikipedia.org/wiki/Domain_Name_System-based_blackhole_list "Domain Name System-based blackhole list"
[YAML]:https://YAML.org/ "YAML Ain't Markup Language™"
