### Contents

- [About this repo](#about-this-repo)

- [Using these ipsets](#using-these-ipsets)
 - [Which ones to use?](#which-ones-to-use)

 - [Why are open proxy lists included](#why-are-open-proxy-lists-included)
   
 - [Using them in FireHOL](#using-them-in-firehol)
    * [Adding the ipsets in your firehol.conf](#adding-the-ipsets-in-your-fireholconf)
    * [Updating the ipsets while the firewall is running](#updating-the-ipsets-while-the-firewall-is-running)
    
- [Dynamic List of ipsets included](#list-of-ipsets-included)

- [Comparison of ipsets](#comparison-of-ipsets)

---

# About this repo

This repository includes a list of ipsets dynamically updated with
firehol's (https://github.com/ktsaou/firehol) `update-ipsets.sh`
script found [here](https://github.com/ktsaou/firehol/blob/master/contrib/update-ipsets.sh).

This repo is self maintained. It it updated automatically from the script via a cron job.

You can find the ipset comparison to the pages of this repo: [http://ktsaou.github.io/blocklist-ipsets](http://ktsaou.github.io/blocklist-ipsets).

## Why do we need blocklists?

As time passes and the internet matures in our life, cyber crime is becoming increasingly sophisticated. Although there are many tools (detection of malware, viruses, intrusion detection and prevension systems, etc) to help us isolate the budguys, there are now a lot more than just such attacks.

What is more interesting is that the fraudsters or attackers in many cases are not going to do a direct damage to you or your systems. They will use you and your systems to gain something else, possibly not related or indirectly related to your business. Nowdays the attacks cannot be identified easily. They are distributed and come to our systems from a vast amount of IPs around the world.

To get an idea, check for example the [XRumer](http://en.wikipedia.org/wiki/XRumer) software. This thing mimics human behaviour to post ads, it creates email accounts, responds to emails it receives, bypasses captchas, it goes gently to stay unoticed, etc.

To increase our effectiveness we need to complement our security solutions with our shared knowledge, our shared experience in this fight.

Hopefully, there are many teams out there that do their best to identify the attacks and pinpoint the attackers. These teams release blocklists. Blocklists of IPs (for use in firewalls), domains & URLs
(for use in proxies), etc.

What we are interested here is IPs.

Using IP blocklists at the internet side of your firewall is a key component of internet security. These lists share key knowledge between us, allowing us to learn from each other and effectively isolate fraudsters and attackers from our services.

I decided to upload these lists to a github repo because:

1. They are freely available on the internet. The intention of their creators is to help internet security.
 Keep in mind though that a few of these lists may have special licences attached. Before using them, please check their source site for any information regarding proper use.

2. Github provides (via `git pull`) a unified way of updating all the lists together.
 Pulling this repo regularly on your machines, you will update all the IP lists at once.

3. Github also provides a unified version control. Using it we can have a history of what each list has done, which IPs or subnets were added and which were removed.

## DNSBLs

Check also another tool included in FireHOL v3+, called `dnsbl-ipset.sh`.

This tool is capable of creating an ipset based on your traffic by looking up information on DNSBLs and scoring it according to your preferences.

More information [here](https://github.com/ktsaou/firehol/wiki/dnsbl-ipset.sh).


---

# Using these ipsets

Please be very careful what you choose to use and how you use it. If you blacklist traffic using these lists you may end up blocking your users, your customers, even yourself (!) from accessing your services.

1. Go to to the site of each list and read how each list is maintained. You are going to trust these guys for doing their job right.

2. Most sites have either a donation system or commercial lists of higher quality. Try to support them. 

3. I have included the TOR network in these lists (`bm_tor`, `dm_tor`, `et_tor`). The TOR network is not necessarily bad and you should not block it if you want to allow your users be anonymous. I have included it because for certain cases, allowing an anonymity network might be a risky thing (such as eCommerce).

4. Apply any blacklist at the internet side of your firewall. Be very carefull. The `bogons` and `fullbogons` lists contain private, unroutable IPs that should not be routed on the internet. If you apply such a blocklist on your DMZ or LAN side, you will be blocked out of your firewall.

5. Always have a whitelist too, containing the IP addresses or subnets you trust. Try to build the rules in such a way that if an IP is in the whitelist, it should not be blocked by these blocklists.


## Which ones to use


### Level 1 - Basic

These are the ones I install on all my firewalls. **Level 1** provides basic security against the most well known attackers, with the minimum of false positives.

1. **Abuse.ch** lists `feodo`, `palevo`, `sslbl`, `zeus`, `zeus_badips`
   
   These folks are doing a great job tracking crimeware. Their blocklists are very focused.
   Keep in mind `zeus` may include some false positives. You can use `zeus_badips` instead.

2. **DShield.org** list `dshield`

   It contains the top 20 attacking class C (/24) subnets, over the last three days.

3. **Spamhaus.org** lists `spamhaus_drop`, `spamhaus_edrop`
   
   DROP (Don't Route Or Peer) and EDROP are advisory "drop all traffic" lists, consisting of netblocks that are "hijacked" or leased by professional spam or cyber-crime operations (used for dissemination of malware, trojan downloaders, botnet controllers).
   According to Spamhaus.org:

   > When implemented at a network or ISP's 'core routers', DROP and EDROP will help protect the network's users from spamming, scanning, harvesting, DNS-hijacking and DDoS attacks originating on rogue netblocks.
   > 
   > Spamhaus strongly encourages the use of DROP and EDROP by tier-1s and backbones.

 Spamhaus is very responsive to adapt these lists when a network owner updates them that the issue has been solved (I had one such incident with one of my users).

4. **Team-Cymru.org** list `bogons` or `fullbogons`

   These are lists of IPs that should not be routed on the internet. No one should be using them.
   Be very careful to apply either of the two on the internet side of your network.

### Level 2 - Essentials

**Level 2** provide protection against current brute force attacks. This level may have a small percentage of false positives, mainly due to dynamic IPs being re-used by other users.

1. **OpenBL.org** lists `openbl*`
   
   The team of OpenBL tracks brute force attacks on their hosts. They have a very short list for hosts, under their own control, collecting this information, to eliminate false positives.
   They suggest to use the default blacklist which has a retention policy of 90 days (`openbl`), but they also provide lists with different retention policies (from 1 day to 1 year).
   Their goal is to report abuse to the responsible provider so that the infection is disabled.

2. **Blocklist.de** lists `blocklist_de*`
   
   Is a network of users reporting abuse mainly using `fail2ban`. They eliminate false positives using other lists available. Since they collect information from their users, their lists may be subject to poisoning, or false positives.
   I asked them about poisoning. [Here](https://forum.blocklist.de/viewtopic.php?f=4&t=244&sid=847d00d26b0735add3518ff515242cad) you can find their answer. In short, they track it down so that they have an ignorable rate of false positives.
   Also, they only include individual IPs (no subnets) which have attacked their users the last 48 hours and their list contains 20.000 to 40.000 IPs (which is small enough considering the size of the internet).
   Like `openbl`, their goal is to report abuse back, so that the infection is disabled.
   They also provide their blocklist per type of attack (mail, web, etc).

Of course there are more lists included. You can check them and decide if they fit for your needs.

## Why are open proxy lists included

Of course, I haven't included them for you to use the open proxies. The port the proxy is listening, or the type of proxy, are not included (although most of them use the standard proxy ports and do serve web requests).

If you check the comparisons for the open proxy lists (`ri_connect_proxies`, `ri_web_proxies`, `xroxy`, `proxz`, `proxyrss`, etc)
you will find that they overlap to a great degree with other blocklists, like `blocklist_de`, `stopforumspam`, etc.

> This means the attackers also use open proxies to execute attacks.

So, if you are under attack, blocking the open proxies may help isolate a large part of the attack.

I don't suggest to permanenly block IPs using the proxy lists. Their purpose of existance is questionable.
Their quality though may be acceptable, since lot of these sites advertise that they test open proxies before including them in their lists, so that there are no false positives, at least at the time they tested them.

---

## Using them in FireHOL

`update-ipsets.sh` itself does not alter your firewall. It can be used to update ipsets both on disk and in the kernel for any firewall solution you use.

The information below, shows you how to configure FireHOL to use the provides ipsets.


### Adding the ipsets in your firehol.conf

I use something like this:

```sh
	# our wan interface
	wan="dsl0"
	
	# our whitelist
	ipset4 create whitelist hash:net
	ipset4 add whitelist A.B.C.D/E # A.B.C.D/E is whitelisted
	
	# subnets - netsets
	for x in fullbogons dshield spamhaus_drop spamhaus_edrop
	do
		ipset4 create  ${x} hash:net
		ipset4 addfile ${x} ipsets/${x}.netset
		blacklist4 full inface "${wan}" log "BLACKLIST ${x^^}" ipset:${x} \
			except src ipset:whitelist
	done

	# individual IPs - ipsets
	for x in feodo palevo sslbl zeus openbl blocklist_de
	do
		ipset4 create  ${x} hash:ip
		ipset4 addfile ${x} ipsets/${x}.ipset
		blacklist4 full inface "${wan}" log "BLACKLIST ${x^^}" ipset:${x} \
			except src ipset:whitelist
	done

	... rest of firehol.conf ...
```

If you are concerned about iptables performance, change the `blacklist4` keyword `full` to `input`.
This will block only inbound NEW connections, i.e. only the first packet for every NEW inbound connection will be checked.
All other traffic passes through unchecked.

> Before adding these rules to your `firehol.conf` you should run `update-ipsets.sh` to enable them.

### Updating the ipsets while the firewall is running

Just use the `update-ipsets.sh` script from the firehol distribution.
This script will update each ipset and call firehol to update the ipset while the firewall is running.

> You can add `update-ipsets.sh` to cron, to run every 30 mins. `update-ipsets.sh` is smart enough to download
> a list only when it needs to.

---

# List of ipsets included

The following list was automatically generated on Tue Aug 18 17:56:40 UTC 2015.

The update frequency is the maximum allowed by internal configuration. A list will never be downloaded sooner than the update frequency stated. A list may also not be downloaded, after this frequency expired, if it has not been modified on the server (as reported by HTTP `IF_MODIFIED_SINCE` method).

name|info|type|entries|update|
:--:|:--:|:--:|:-----:|:----:|
[alienvault_reputation](http://ktsaou.github.io/blocklist-ipsets/?ipset=alienvault_reputation)|[AlienVault.com](https://www.alienvault.com/) IP reputation database|ipv4 hash:ip|120742 unique IPs|updated every 6 hours  from [this link](https://reputation.alienvault.com/reputation.generic)
[asprox_c2](http://ktsaou.github.io/blocklist-ipsets/?ipset=asprox_c2)|[h3x.eu](http://atrack.h3x.eu/) ASPROX Tracker - Asprox C&C Sites|ipv4 hash:ip|936 unique IPs|updated every 1 day  from [this link](http://atrack.h3x.eu/c2)
[bambenek_banjori](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_banjori)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of banjori C&Cs with 90 minute lookback|ipv4 hash:ip|0 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/banjori-iplist.txt)
[bambenek_bebloh](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_bebloh)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of bebloh C&Cs with 90 minute lookback|ipv4 hash:ip|4 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/bebloh-iplist.txt)
[bambenek_c2](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_c2)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) master feed of known, active and non-sinkholed C&Cs IP addresses|ipv4 hash:ip|243 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/c2-ipmasterlist.txt)
[bambenek_cl](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_cl)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of cl C&Cs with 90 minute lookback|ipv4 hash:ip|0 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/cl-iplist.txt)
[bambenek_cryptowall](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_cryptowall)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of cryptowall C&Cs with 90 minute lookback|ipv4 hash:ip|17 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/cryptowall-iplist.txt)
[bambenek_dircrypt](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_dircrypt)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of dircrypt C&Cs with 90 minute lookback|ipv4 hash:ip|3 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/dircrypt-iplist.txt)
[bambenek_dyre](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_dyre)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of dyre C&Cs with 90 minute lookback|ipv4 hash:ip|2 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/dyre-iplist.txt)
[bambenek_geodo](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_geodo)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of geodo C&Cs with 90 minute lookback|ipv4 hash:ip|0 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/geodo-iplist.txt)
[bambenek_hesperbot](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_hesperbot)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of hesperbot C&Cs with 90 minute lookback|ipv4 hash:ip|2 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/hesperbot-iplist.txt)
[bambenek_matsnu](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_matsnu)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of matsnu C&Cs with 90 minute lookback|ipv4 hash:ip|13 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/matsnu-iplist.txt)
[bambenek_necurs](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_necurs)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of necurs C&Cs with 90 minute lookback|ipv4 hash:ip|1 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/necurs-iplist.txt)
[bambenek_p2pgoz](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_p2pgoz)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of p2pgoz C&Cs with 90 minute lookback|ipv4 hash:ip|0 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/p2pgoz-iplist.txt)
[bambenek_pushdo](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_pushdo)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of pushdo C&Cs with 90 minute lookback|ipv4 hash:ip|1 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/pushdo-iplist.txt)
[bambenek_pykspa](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_pykspa)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of pykspa C&Cs with 90 minute lookback|ipv4 hash:ip|5 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/pykspa-iplist.txt)
[bambenek_qakbot](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_qakbot)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of qakbot C&Cs with 90 minute lookback|ipv4 hash:ip|3 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/qakbot-iplist.txt)
[bambenek_ramnit](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_ramnit)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of ramnit C&Cs with 90 minute lookback|ipv4 hash:ip|4 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/ramnit-iplist.txt)
[bambenek_ranbyus](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_ranbyus)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of ranbyus C&Cs with 90 minute lookback|ipv4 hash:ip|0 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/ranbyus-iplist.txt)
[bambenek_simda](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_simda)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of simda C&Cs with 90 minute lookback|ipv4 hash:ip|62 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/simda-iplist.txt)
[bambenek_suppobox](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_suppobox)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of suppobox C&Cs with 90 minute lookback|ipv4 hash:ip|113 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/suppobox-iplist.txt)
[bambenek_symmi](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_symmi)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of symmi C&Cs with 90 minute lookback|ipv4 hash:ip|0 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/symmi-iplist.txt)
[bambenek_tinba](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_tinba)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of tinba C&Cs with 90 minute lookback|ipv4 hash:ip|11 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/tinba-iplist.txt)
[bambenek_volatile](http://ktsaou.github.io/blocklist-ipsets/?ipset=bambenek_volatile)|[Bambenek Consulting](http://osint.bambenekconsulting.com/feeds/) feed of current IPs of volatile C&Cs with 90 minute lookback|ipv4 hash:ip|3 unique IPs|updated every 30 mins  from [this link](http://osint.bambenekconsulting.com/feeds/volatile-iplist.txt)
[bds_atif](http://ktsaou.github.io/blocklist-ipsets/?ipset=bds_atif)|[Binary Defense Systems Artillery Threat Intelligence Feed and Banlist Feed](https://www.binarydefense.com/banlist.txt)|ipv4 hash:ip|14636 unique IPs|updated every 1 day  from [this link](https://www.binarydefense.com/banlist.txt)
[bi_any_2_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_any_2_1d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category any with score above 2 and age less than 1d|ipv4 hash:ip|1509 unique IPs|updated every 30 mins  from [this link](https://www.badips.com/get/list/any/2?age=1d)
[bi_any_2_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_any_2_30d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category any with score above 2 and age less than 30d|ipv4 hash:ip|11168 unique IPs|updated every 1 day  from [this link](https://www.badips.com/get/list/any/2?age=30d)
[bi_any_2_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_any_2_7d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category any with score above 2 and age less than 7d|ipv4 hash:ip|4212 unique IPs|updated every 6 hours  from [this link](https://www.badips.com/get/list/any/2?age=7d)
[bi_bruteforce_2_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_bruteforce_2_30d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category bruteforce with score above 2 and age less than 30d|ipv4 hash:ip|0 unique IPs|updated every 1 day  from [this link](https://www.badips.com/get/list/bruteforce/2?age=30d)
[bi_ftp_2_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_ftp_2_30d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category ftp with score above 2 and age less than 30d|ipv4 hash:ip|1561 unique IPs|updated every 1 day  from [this link](https://www.badips.com/get/list/ftp/2?age=30d)
[bi_http_2_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_http_2_30d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category http with score above 2 and age less than 30d|ipv4 hash:ip|71 unique IPs|updated every 1 day  from [this link](https://www.badips.com/get/list/http/2?age=30d)
[bi_mail_2_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_mail_2_30d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category mail with score above 2 and age less than 30d|ipv4 hash:ip|666 unique IPs|updated every 1 day  from [this link](https://www.badips.com/get/list/mail/2?age=30d)
[bi_proxy_2_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_proxy_2_30d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category proxy with score above 2 and age less than 30d|ipv4 hash:ip|79 unique IPs|updated every 1 day  from [this link](https://www.badips.com/get/list/proxy/2?age=30d)
[bi_sql_2_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_sql_2_30d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category sql with score above 2 and age less than 30d|ipv4 hash:ip|0 unique IPs|updated every 1 day  from [this link](https://www.badips.com/get/list/sql/2?age=30d)
[bi_ssh_2_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_ssh_2_30d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category ssh with score above 2 and age less than 30d|ipv4 hash:ip|8796 unique IPs|updated every 1 day  from [this link](https://www.badips.com/get/list/ssh/2?age=30d)
[bi_voip_2_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=bi_voip_2_30d)|[BadIPs.com](https://www.badips.com/) Bad IPs in category voip with score above 2 and age less than 30d|ipv4 hash:ip|32 unique IPs|updated every 1 day  from [this link](https://www.badips.com/get/list/voip/2?age=30d)
[blocklist_de](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de)|[Blocklist.de](https://www.blocklist.de/) IPs that have been detected by fail2ban in the last 48 hours|ipv4 hash:ip|23406 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/all.txt)
[blocklist_de_apache](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de_apache)|[Blocklist.de](https://www.blocklist.de/) All IP addresses which have been reported within the last 48 hours as having run attacks on the service Apache, Apache-DDOS, RFI-Attacks.|ipv4 hash:ip|13811 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/apache.txt)
[blocklist_de_bots](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de_bots)|[Blocklist.de](https://www.blocklist.de/) All IP addresses which have been reported within the last 48 hours as having run attacks on the RFI-Attacks, REG-Bots, IRC-Bots or BadBots (BadBots = he has posted a Spam-Comment on a open Forum or Wiki).|ipv4 hash:ip|2761 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/bots.txt)
[blocklist_de_bruteforce](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de_bruteforce)|[Blocklist.de](https://www.blocklist.de/) All IPs which attacks Joomlas, Wordpress and other Web-Logins with Brute-Force Logins.|ipv4 hash:ip|2492 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/bruteforcelogin.txt)
[blocklist_de_ftp](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de_ftp)|[Blocklist.de](https://www.blocklist.de/) All IP addresses which have been reported within the last 48 hours for attacks on the Service FTP.|ipv4 hash:ip|315 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/ftp.txt)
[blocklist_de_imap](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de_imap)|[Blocklist.de](https://www.blocklist.de/) All IP addresses which have been reported within the last 48 hours for attacks on the Service imap, sasl, pop3, etc.|ipv4 hash:ip|479 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/imap.txt)
[blocklist_de_mail](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de_mail)|[Blocklist.de](https://www.blocklist.de/) All IP addresses which have been reported within the last 48 hours as having run attacks on the service Mail, Postfix.|ipv4 hash:ip|14547 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/mail.txt)
[blocklist_de_sip](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de_sip)|[Blocklist.de](https://www.blocklist.de/) All IP addresses that tried to login in a SIP, VOIP or Asterisk Server and are included in the IPs list from infiltrated.net|ipv4 hash:ip|78 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/sip.txt)
[blocklist_de_ssh](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de_ssh)|[Blocklist.de](https://www.blocklist.de/) All IP addresses which have been reported within the last 48 hours as having run attacks on the service SSH.|ipv4 hash:ip|2983 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/ssh.txt)
[blocklist_de_strongips](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_de_strongips)|[Blocklist.de](https://www.blocklist.de/) All IPs which are older then 2 month and have more then 5.000 attacks.|ipv4 hash:ip|169 unique IPs|updated every 30 mins  from [this link](http://lists.blocklist.de/lists/strongips.txt)
[blocklist_net_ua](http://ktsaou.github.io/blocklist-ipsets/?ipset=blocklist_net_ua)|[blocklist.net.ua](https://blocklist.net.ua) The BlockList project was created to become protection against negative influence of the harmful and potentially dangerous events on the Internet. First of all this service will help internet and hosting providers to protect subscribers sites from being hacked. BlockList will help to stop receiving a large amount of spam from dubious SMTP relays or from attempts of brute force passwords to servers and network equipment.|ipv4 hash:ip|13592 unique IPs|updated every 10 mins  from [this link](https://blocklist.net.ua/blocklist.csv)
[bm_tor](http://ktsaou.github.io/blocklist-ipsets/?ipset=bm_tor)|[torstatus.blutmagie.de](https://torstatus.blutmagie.de) list of all TOR network servers|ipv4 hash:ip|6126 unique IPs|updated every 30 mins  from [this link](https://torstatus.blutmagie.de/ip_list_all.php/Tor_ip_list_ALL.csv)
[bogons](http://ktsaou.github.io/blocklist-ipsets/?ipset=bogons)|[Team-Cymru.org](http://www.team-cymru.org) private and reserved addresses defined by RFC 1918, RFC 5735, and RFC 6598 and netblocks that have not been allocated to a regional internet registry|ipv4 hash:net|13 subnets, 592708608 unique IPs|updated every 1 day  from [this link](http://www.team-cymru.org/Services/Bogons/bogon-bn-agg.txt)
[botscout](http://ktsaou.github.io/blocklist-ipsets/?ipset=botscout)|[BotScout](http://botscout.com/) helps prevent automated web scripts, known as bots, from registering on forums, polluting databases, spreading spam, and abusing forms on web sites. They do this by tracking the names, IPs, and email addresses that bots use and logging them as unique signatures for future reference. They also provide a simple yet powerful API that you can use to test forms when they're submitted on your site. This list is composed of the most recently-caught bots.|ipv4 hash:ip|44 unique IPs|updated every 30 mins  from [this link](http://botscout.com/last_caught_cache.htm)
[botscout_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=botscout_1d)|[BotScout](http://botscout.com/) helps prevent automated web scripts, known as bots, from registering on forums, polluting databases, spreading spam, and abusing forms on web sites. They do this by tracking the names, IPs, and email addresses that bots use and logging them as unique signatures for future reference. They also provide a simple yet powerful API that you can use to test forms when they're submitted on your site. This list is composed of the most recently-caught bots.|ipv4 hash:ip|1357 unique IPs|updated every 30 mins  from [this link](http://botscout.com/last_caught_cache.htm)
[botscout_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=botscout_30d)|[BotScout](http://botscout.com/) helps prevent automated web scripts, known as bots, from registering on forums, polluting databases, spreading spam, and abusing forms on web sites. They do this by tracking the names, IPs, and email addresses that bots use and logging them as unique signatures for future reference. They also provide a simple yet powerful API that you can use to test forms when they're submitted on your site. This list is composed of the most recently-caught bots.|ipv4 hash:ip|12686 unique IPs|updated every 30 mins  from [this link](http://botscout.com/last_caught_cache.htm)
[botscout_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=botscout_7d)|[BotScout](http://botscout.com/) helps prevent automated web scripts, known as bots, from registering on forums, polluting databases, spreading spam, and abusing forms on web sites. They do this by tracking the names, IPs, and email addresses that bots use and logging them as unique signatures for future reference. They also provide a simple yet powerful API that you can use to test forms when they're submitted on your site. This list is composed of the most recently-caught bots.|ipv4 hash:ip|6217 unique IPs|updated every 30 mins  from [this link](http://botscout.com/last_caught_cache.htm)
[bruteforceblocker](http://ktsaou.github.io/blocklist-ipsets/?ipset=bruteforceblocker)|[danger.rulez.sk bruteforceblocker](http://danger.rulez.sk/index.php/bruteforceblocker/) (fail2ban alternative for SSH on OpenBSD). This is an automatically generated list from users reporting failed authentication attempts. An IP seems to be included if 3 or more users report it. Its retention pocily seems 30 days.|ipv4 hash:ip|1463 unique IPs|updated every 3 hours  from [this link](http://danger.rulez.sk/projects/bruteforceblocker/blist.php)
[ciarmy](http://ktsaou.github.io/blocklist-ipsets/?ipset=ciarmy)|[CIArmy.com](http://ciarmy.com/) IPs with poor Rogue Packet score that have not yet been identified as malicious by the community|ipv4 hash:ip|434 unique IPs|updated every 3 hours  from [this link](http://cinsscore.com/list/ci-badguys.txt)
[cleanmx_viruses](http://ktsaou.github.io/blocklist-ipsets/?ipset=cleanmx_viruses)|[Clean-MX.de](http://support.clean-mx.de/clean-mx/viruses.php) IPs with viruses|ipv4 hash:ip|1948 unique IPs|updated every 30 mins  from [this link](http://support.clean-mx.de/clean-mx/xmlviruses.php?response=alive&fields=ip)
[dm_tor](http://ktsaou.github.io/blocklist-ipsets/?ipset=dm_tor)|[dan.me.uk](https://www.dan.me.uk) dynamic list of TOR nodes|ipv4 hash:ip|6145 unique IPs|updated every 30 mins  from [this link](https://www.dan.me.uk/torlist/)
[dragon_http](http://ktsaou.github.io/blocklist-ipsets/?ipset=dragon_http)|[Dragon Research Group](http://www.dragonresearchgroup.org/) IPs that have been seen sending HTTP requests to Dragon Research Pods in the last 7 days. This report lists hosts that are highly suspicious and are likely conducting malicious HTTP attacks. LEGITIMATE SEARCH ENGINE BOTS MAY BE IN THIS LIST. This report is informational.  It is not a blacklist, but some operators may choose to use it to help protect their networks and hosts in the forms of automated reporting and mitigation services.|ipv4 hash:net|1636 subnets, 494592 unique IPs|updated every 1 hour  from [this link](http://www.dragonresearchgroup.org/insight/http-report.txt)
[dragon_sshpauth](http://ktsaou.github.io/blocklist-ipsets/?ipset=dragon_sshpauth)|[Dragon Research Group](http://www.dragonresearchgroup.org/) IP address that has been seen attempting to remotely login to a host using SSH password authentication, in the last 7 days. This report lists hosts that are highly suspicious and are likely conducting malicious SSH password authentication attacks.|ipv4 hash:net|14720 subnets, 15577 unique IPs|updated every 1 hour  from [this link](https://www.dragonresearchgroup.org/insight/sshpwauth.txt)
[dragon_vncprobe](http://ktsaou.github.io/blocklist-ipsets/?ipset=dragon_vncprobe)|[Dragon Research Group](http://www.dragonresearchgroup.org/) IP address that has been seen attempting to remotely connect to a host running the VNC application service, in the last 7 days. This report lists hosts that are highly suspicious and are likely conducting malicious VNC probes or VNC brute force attacks.|ipv4 hash:net|107 subnets, 108 unique IPs|updated every 1 hour  from [this link](https://www.dragonresearchgroup.org/insight/vncprobe.txt)
[dshield](http://ktsaou.github.io/blocklist-ipsets/?ipset=dshield)|[DShield.org](https://dshield.org/) top 20 attacking class C (/24) subnets over the last three days|ipv4 hash:net|20 subnets, 5120 unique IPs|updated every 15 mins  from [this link](http://feeds.dshield.org/block.txt)
[dshield_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=dshield_1d)|[DShield.org](https://dshield.org/) top 20 attacking class C (/24) subnets over the last three days|ipv4 hash:net|110 subnets, 28416 unique IPs|updated every 15 mins  from [this link](http://feeds.dshield.org/block.txt)
[dshield_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=dshield_30d)|[DShield.org](https://dshield.org/) top 20 attacking class C (/24) subnets over the last three days|ipv4 hash:net|2694 subnets, 704000 unique IPs|updated every 15 mins  from [this link](http://feeds.dshield.org/block.txt)
[dshield_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=dshield_7d)|[DShield.org](https://dshield.org/) top 20 attacking class C (/24) subnets over the last three days|ipv4 hash:net|674 subnets, 175616 unique IPs|updated every 15 mins  from [this link](http://feeds.dshield.org/block.txt)
[et_block](http://ktsaou.github.io/blocklist-ipsets/?ipset=et_block)|[EmergingThreats.net](http://www.emergingthreats.net/) default blacklist (at the time of writing includes spamhaus DROP, dshield and abuse.ch trackers, which are available separately too - prefer to use the direct ipsets instead of this, they seem to lag a bit in updates)|ipv4 hash:net|693 subnets, 21242880 unique IPs|updated every 12 hours  from [this link](http://rules.emergingthreats.net/fwrules/emerging-Block-IPs.txt)
[et_botcc](http://ktsaou.github.io/blocklist-ipsets/?ipset=et_botcc)|[EmergingThreats.net Command and Control IPs](http://doc.emergingthreats.net/bin/view/Main/BotCC) These IPs are updates every 24 hours and should be considered VERY highly reliable indications that a host is communicating with a known and active Bot or Malware command and control server - (although they say this includes abuse.ch trackers, it does not - check its overlaps)|ipv4 hash:ip|488 unique IPs|updated every 12 hours  from [this link](http://rules.emergingthreats.net/fwrules/emerging-PIX-CC.rules)
[et_compromised](http://ktsaou.github.io/blocklist-ipsets/?ipset=et_compromised)|[EmergingThreats.net compromised hosts](http://doc.emergingthreats.net/bin/view/Main/CompromisedHost)|ipv4 hash:ip|1423 unique IPs|updated every 12 hours  from [this link](http://rules.emergingthreats.net/blockrules/compromised-ips.txt)
et_dshield|[EmergingThreats.net](http://www.emergingthreats.net/) dshield blocklist|ipv4 hash:net|disabled|updated every 12 hours  from [this link](http://rules.emergingthreats.net/fwrules/emerging-PIX-DSHIELD.rules)
et_spamhaus|[EmergingThreats.net](http://www.emergingthreats.net/) spamhaus blocklist|ipv4 hash:net|disabled|updated every 12 hours  from [this link](http://rules.emergingthreats.net/fwrules/emerging-PIX-DROP.rules)
[et_tor](http://ktsaou.github.io/blocklist-ipsets/?ipset=et_tor)|[EmergingThreats.net TOR list](http://doc.emergingthreats.net/bin/view/Main/TorRules) of TOR network IPs|ipv4 hash:ip|6150 unique IPs|updated every 12 hours  from [this link](http://rules.emergingthreats.net/blockrules/emerging-tor.rules)
[feodo](http://ktsaou.github.io/blocklist-ipsets/?ipset=feodo)|[Abuse.ch Feodo tracker](https://feodotracker.abuse.ch) trojan includes IPs which are being used by Feodo (also known as Cridex or Bugat) which commits ebanking fraud|ipv4 hash:ip|178 unique IPs|updated every 30 mins  from [this link](https://feodotracker.abuse.ch/blocklist/?download=ipblocklist)
[firehol_anonymous](http://ktsaou.github.io/blocklist-ipsets/?ipset=firehol_anonymous)|An ipset that includes all the anonymizing IPs of the world. (includes: firehol_proxies anonymous bm_tor dm_tor tor_exits)|ipv4 hash:net|29966 subnets, 88010 unique IPs|
[firehol_level1](http://ktsaou.github.io/blocklist-ipsets/?ipset=firehol_level1)|An ipset made from blocklists that provide the maximum of protection, with the minimum of false positives. Suitable for basic protection on all systems. (includes: fullbogons dshield feodo palevo sslbl zeus_badips spamhaus_drop spamhaus_edrop bambenek_c2)|ipv4 hash:net|4861 subnets, 684829581 unique IPs|
[firehol_level2](http://ktsaou.github.io/blocklist-ipsets/?ipset=firehol_level2)|An ipset made from blocklists that track attacks and abuse, during the last one or two days. (includes: openbl_1d dshield_1d blocklist_de stopforumspam_1d botscout_1d greensnow)|ipv4 hash:net|21425 subnets, 61253 unique IPs|
[firehol_level3](http://ktsaou.github.io/blocklist-ipsets/?ipset=firehol_level3)|An ipset made from blocklists that track attacks, spyware, viruses. It includes IPs than have been reported or detected in the last 30 days. (includes: openbl_30d dshield_30d stopforumspam_30d virbl malc0de shunlist malwaredomainlist bruteforceblocker ciarmy cleanmx_viruses snort_ipfilter ib_bluetack_spyware ib_bluetack_hijacked ib_bluetack_webexploit php_commenters php_dictionary php_harvesters php_spammers iw_wormlist zeus maxmind_proxy_fraud dragon_http dragon_sshpauth dragon_vncprobe bambenek_c2)|ipv4 hash:net|125302 subnets, 10824376 unique IPs|
[firehol_proxies](http://ktsaou.github.io/blocklist-ipsets/?ipset=firehol_proxies)|An ipset made from all sources that track open proxies. It includes IPs reported or detected in the last 30 days. (includes: ib_bluetack_proxies maxmind_proxy_fraud proxyrss_30d proxz_30d ri_connect_proxies_30d ri_web_proxies_30d xroxy_30d proxyspy_30d)|ipv4 hash:net|23859 subnets, 26566 unique IPs|
[fullbogons](http://ktsaou.github.io/blocklist-ipsets/?ipset=fullbogons)|[Team-Cymru.org](http://www.team-cymru.org) IP space that has been allocated to an RIR, but not assigned by that RIR to an actual ISP or other end-user|ipv4 hash:net|3451 subnets, 663213288 unique IPs|updated every 1 day  from [this link](http://www.team-cymru.org/Services/Bogons/fullbogons-ipv4.txt)
[geolite2_country](https://github.com/ktsaou/blocklist-ipsets/tree/master/geolite2_country)|[MaxMind GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/) databases are free IP geolocation databases comparable to, but less accurate than, MaxMind’s GeoIP2 databases. They include IPs per country, IPs per continent, IPs used by anonymous services (VPNs, Proxies, etc) and Satellite Providers.|ipv4 hash:net|All the world|updated every 7 days  from [this link](http://geolite.maxmind.com/download/geoip/database/GeoLite2-Country-CSV.zip)
[gofferje_sip](http://ktsaou.github.io/blocklist-ipsets/?ipset=gofferje_sip)|[Stefan Gofferje](http://stefan.gofferje.net/it-stuff/sipfraud/sip-attacker-blacklist) A personal blacklist of networks and IPs of SIP attackers. To end up here, the IP or network must have been the origin of considerable and repeated attacks on my PBX and additionally, the ISP didn't react to any complaint. Note from the author: I don't give any guarantees of accuracy, completeness or even usability! USE AT YOUR OWN RISK! Also note that I block complete countries, namely China, Korea and Palestine with blocklists from ipdeny.com, so some attackers will never even get the chance to get noticed by me to be put on this blacklist. I also don't accept any liabilities related to this blocklist. If you're an ISP and don't like your IPs being listed here, too bad! You should have done something about your customers' behavior and reacted to my complaints. This blocklist is nothing but an expression of my personal opinion and exercising my right of free speech.|ipv4 hash:net|397 subnets, 1094201 unique IPs|updated every 6 hours  from [this link](http://stefan.gofferje.net/sipblocklist.zone)
[greensnow](http://ktsaou.github.io/blocklist-ipsets/?ipset=greensnow)|[GreenSnow](https://greensnow.co/) is a team harvesting a large number of IPs from different computers located around the world. GreenSnow is comparable with SpamHaus.org for attacks of any kind except for spam. Their list is updated automatically and you can withdraw at any time your IP address if it has been listed. Attacks / bruteforce that are monitored are: Scan Port, FTP, POP3, mod_security, IMAP, SMTP, SSH, cPanel, etc.|ipv4 hash:ip|1118 unique IPs|updated every 30 mins  from [this link](http://blocklist.greensnow.co/greensnow.txt)
[haley_ssh](http://ktsaou.github.io/blocklist-ipsets/?ipset=haley_ssh)|[Charles Haley](http://charles.the-haleys.org) IPs launching SSH dictionary attacks.|ipv4 hash:ip|19560 unique IPs|updated every 4 hours  from [this link](http://charles.the-haleys.org/ssh_dico_attack_hdeny_format.php/hostsdeny.txt)
[ib_bluetack_badpeers](http://ktsaou.github.io/blocklist-ipsets/?ipset=ib_bluetack_badpeers)|[iBlocklist.com](https://www.iblocklist.com/) version of BlueTack.co.uk IPs that have been reported for bad deeds in p2p|ipv4 hash:ip|47940 unique IPs|updated every 12 hours  from [this link](http://list.iblocklist.com/?list=cwworuawihqvocglcoss&fileformat=p2p&archiveformat=gz)
[ib_bluetack_hijacked](http://ktsaou.github.io/blocklist-ipsets/?ipset=ib_bluetack_hijacked)|[iBlocklist.com](https://www.iblocklist.com/) version of BlueTack.co.uk hijacked IP-Blocks Hijacked IP space are IP blocks that are being used without permission|ipv4 hash:net|535 subnets, 9177856 unique IPs|updated every 12 hours  from [this link](http://list.iblocklist.com/?list=usrcshglbiilevmyfhse&fileformat=p2p&archiveformat=gz)
[ib_bluetack_level1](http://ktsaou.github.io/blocklist-ipsets/?ipset=ib_bluetack_level1)|[iBlocklist.com](https://www.iblocklist.com/) version of BlueTack.co.uk Level 1 (for use in p2p): Companies or organizations who are clearly involved with trying to stop filesharing (e.g. Baytsp, MediaDefender, Mediasentry a.o.). Companies which anti-p2p activity has been seen from. Companies that produce or have a strong financial interest in copyrighted material (e.g. music, movie, software industries a.o.). Government ranges or companies that have a strong financial interest in doing work for governments. Legal industry ranges. IPs or ranges of ISPs from which anti-p2p activity has been observed. Basically this list will block all kinds of internet connections that most people would rather not have during their internet travels.|ipv4 hash:net|218307 subnets, 764993634 unique IPs|updated every 12 hours  from [this link](http://list.iblocklist.com/?list=ydxerpxkpcfqjaybcssw&fileformat=p2p&archiveformat=gz)
[ib_bluetack_level2](http://ktsaou.github.io/blocklist-ipsets/?ipset=ib_bluetack_level2)|[iBlocklist.com](https://www.iblocklist.com/) version of BlueTack.co.uk Level 2 (for use in p2p). General corporate ranges. Ranges used by labs or researchers. Proxies.|ipv4 hash:net|72950 subnets, 348710251 unique IPs|updated every 12 hours  from [this link](http://list.iblocklist.com/?list=gyisgnzbhppbvsphucsw&fileformat=p2p&archiveformat=gz)
[ib_bluetack_level3](http://ktsaou.github.io/blocklist-ipsets/?ipset=ib_bluetack_level3)|[iBlocklist.com](https://www.iblocklist.com/) version of BlueTack.co.uk Level 3 (for use in p2p). Many portal-type websites. ISP ranges that may be dodgy for some reason. Ranges that belong to an individual, but which have not been determined to be used by a particular company. Ranges for things that are unusual in some way. The L3 list is aka the paranoid list.|ipv4 hash:net|17812 subnets, 139104927 unique IPs|updated every 12 hours  from [this link](http://list.iblocklist.com/?list=uwnukjqktoggdknzrhgh&fileformat=p2p&archiveformat=gz)
[ib_bluetack_proxies](http://ktsaou.github.io/blocklist-ipsets/?ipset=ib_bluetack_proxies)|[iBlocklist.com](https://www.iblocklist.com/) version of BlueTack.co.uk Open Proxies IPs list (without TOR)|ipv4 hash:ip|663 unique IPs|updated every 12 hours  from [this link](http://list.iblocklist.com/?list=xoebmbyexwuiogmbyprb&fileformat=p2p&archiveformat=gz)
[ib_bluetack_spyware](http://ktsaou.github.io/blocklist-ipsets/?ipset=ib_bluetack_spyware)|[iBlocklist.com](https://www.iblocklist.com/) version of BlueTack.co.uk known malicious SPYWARE and ADWARE IP Address ranges|ipv4 hash:net|3297 subnets, 339021 unique IPs|updated every 12 hours  from [this link](http://list.iblocklist.com/?list=llvtlsjyoyiczbkjsxpf&fileformat=p2p&archiveformat=gz)
[ib_bluetack_webexploit](http://ktsaou.github.io/blocklist-ipsets/?ipset=ib_bluetack_webexploit)|[iBlocklist.com](https://www.iblocklist.com/) version of BlueTack.co.uk web server hack and exploit attempts|ipv4 hash:ip|1450 unique IPs|updated every 12 hours  from [this link](http://list.iblocklist.com/?list=ghlzqtqxnzctvvajwwag&fileformat=p2p&archiveformat=gz)
infiltrated|[infiltrated.net](http://www.infiltrated.net) (this list seems to be updated frequently, but we found no information about it)|ipv4 hash:ip|disabled|updated every 12 hours  from [this link](http://www.infiltrated.net/blacklisted)
[ipdeny_country](https://github.com/ktsaou/blocklist-ipsets/tree/master/ipdeny_country)|[IPDeny.com](http://www.ipdeny.com/) geolocation database|ipv4 hash:net|All the world|updated every 1 day  from [this link](http://www.ipdeny.com/ipblocks/data/countries/all-zones.tar.gz)
[iw_spamlist](http://ktsaou.github.io/blocklist-ipsets/?ipset=iw_spamlist)|[ImproWare Antispam](http://antispam.imp.ch/) IPs sending spam, in the last 3 days|ipv4 hash:ip|3227 unique IPs|updated every 1 hour  from [this link](http://antispam.imp.ch/spamlist)
[iw_wormlist](http://ktsaou.github.io/blocklist-ipsets/?ipset=iw_wormlist)|[ImproWare Antispam](http://antispam.imp.ch/) IPs sending emails with viruses or worms, in the last 3 days|ipv4 hash:ip|7 unique IPs|updated every 1 hour  from [this link](http://antispam.imp.ch/wormlist)
[lashback_ubl](http://ktsaou.github.io/blocklist-ipsets/?ipset=lashback_ubl)|[The LashBack UBL](http://blacklist.lashback.com/) The Unsubscribe Blacklist (UBL) is a real-time blacklist of IP addresses which are sending email to names harvested from suppression files (this is a big list, more than 500.000 IPs)|ipv4 hash:ip|628789 unique IPs|updated every 1 day  from [this link](http://www.unsubscore.com/blacklist.txt)
[malc0de](http://ktsaou.github.io/blocklist-ipsets/?ipset=malc0de)|[Malc0de.com](http://malc0de.com) malicious IPs of the last 30 days|ipv4 hash:ip|441 unique IPs|updated every 1 day  from [this link](http://malc0de.com/bl/IP_Blacklist.txt)
[malwaredomainlist](http://ktsaou.github.io/blocklist-ipsets/?ipset=malwaredomainlist)|[malwaredomainlist.com](http://www.malwaredomainlist.com) list of malware active ip addresses|ipv4 hash:ip|1289 unique IPs|updated every 12 hours  from [this link](http://www.malwaredomainlist.com/hostslist/ip.txt)
[maxmind_proxy_fraud](http://ktsaou.github.io/blocklist-ipsets/?ipset=maxmind_proxy_fraud)|[MaxMind.com](https://www.maxmind.com/en/anonymous-proxy-fraudulent-ip-address-list) list of anonymous proxy fraudelent IP addresses.|ipv4 hash:ip|366 unique IPs|updated every 4 hours  from [this link](https://www.maxmind.com/en/anonymous-proxy-fraudulent-ip-address-list)
[maxmind_proxy_fraud_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=maxmind_proxy_fraud_1d)|[MaxMind.com](https://www.maxmind.com/en/anonymous-proxy-fraudulent-ip-address-list) list of anonymous proxy fraudelent IP addresses.|ipv4 hash:ip|366 unique IPs|updated every 4 hours  from [this link](https://www.maxmind.com/en/anonymous-proxy-fraudulent-ip-address-list)
[maxmind_proxy_fraud_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=maxmind_proxy_fraud_30d)|[MaxMind.com](https://www.maxmind.com/en/anonymous-proxy-fraudulent-ip-address-list) list of anonymous proxy fraudelent IP addresses.|ipv4 hash:ip|649 unique IPs|updated every 4 hours  from [this link](https://www.maxmind.com/en/anonymous-proxy-fraudulent-ip-address-list)
[maxmind_proxy_fraud_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=maxmind_proxy_fraud_7d)|[MaxMind.com](https://www.maxmind.com/en/anonymous-proxy-fraudulent-ip-address-list) list of anonymous proxy fraudelent IP addresses.|ipv4 hash:ip|366 unique IPs|updated every 4 hours  from [this link](https://www.maxmind.com/en/anonymous-proxy-fraudulent-ip-address-list)
[myip](http://ktsaou.github.io/blocklist-ipsets/?ipset=myip)|[myip.ms](http://www.myip.ms/info/about) IPs identified as web bots in the last 10 days, using several sites that require human action|ipv4 hash:ip|2003 unique IPs|updated every 1 day  from [this link](http://www.myip.ms/files/blacklist/csf/latest_blacklist.txt)
[nixspam](http://ktsaou.github.io/blocklist-ipsets/?ipset=nixspam)|[NiX Spam](http://www.heise.de/ix/NiX-Spam-DNSBL-and-blacklist-for-download-499637.html) IP addresses that sent spam in the last hour - automatically generated entries without distinguishing open proxies from relays, dialup gateways, and so on. All IPs are removed after 12 hours if there is no spam from there.|ipv4 hash:ip|30648 unique IPs|updated every 15 mins  from [this link](http://www.dnsbl.manitu.net/download/nixspam-ip.dump.gz)
[nt_malware_dns](http://ktsaou.github.io/blocklist-ipsets/?ipset=nt_malware_dns)|[No Think](http://www.nothink.org/) Malware DNS (the original list includes hostnames and domains, which are ignored)|ipv4 hash:ip|235 unique IPs|updated every 1 hour  from [this link](http://www.nothink.org/blacklist/blacklist_malware_dns.txt)
[nt_malware_http](http://ktsaou.github.io/blocklist-ipsets/?ipset=nt_malware_http)|[No Think](http://www.nothink.org/) Malware HTTP|ipv4 hash:ip|69 unique IPs|updated every 1 hour  from [this link](http://www.nothink.org/blacklist/blacklist_malware_http.txt)
[nt_malware_irc](http://ktsaou.github.io/blocklist-ipsets/?ipset=nt_malware_irc)|[No Think](http://www.nothink.org/) Malware IRC|ipv4 hash:ip|43 unique IPs|updated every 1 hour  from [this link](http://www.nothink.org/blacklist/blacklist_malware_irc.txt)
[nt_ssh_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=nt_ssh_7d)|[NoThink](http://www.nothink.org/) Last 7 days SSH attacks|ipv4 hash:ip|4431 unique IPs|updated every 1 hour  from [this link](http://www.nothink.org/blacklist/blacklist_ssh_week.txt)
openbl|[OpenBL.org](http://www.openbl.org/) default blacklist (currently it is the same with 90 days). OpenBL.org is detecting, logging and reporting various types of internet abuse. Currently they monitor ports 21 (FTP), 22 (SSH), 23 (TELNET), 25 (SMTP), 110 (POP3), 143 (IMAP), 587 (Submission), 993 (IMAPS) and 995 (POP3S) for bruteforce login attacks as well as scans on ports 80 (HTTP) and 443 (HTTPS) for vulnerable installations of phpMyAdmin and other web applications|ipv4 hash:ip|disabled|updated every 4 hours  from [this link](http://www.openbl.org/lists/base.txt)
[openbl_180d](http://ktsaou.github.io/blocklist-ipsets/?ipset=openbl_180d)|[OpenBL.org](http://www.openbl.org/) last 180 days IPs.  OpenBL.org is detecting, logging and reporting various types of internet abuse. Currently they monitor ports 21 (FTP), 22 (SSH), 23 (TELNET), 25 (SMTP), 110 (POP3), 143 (IMAP), 587 (Submission), 993 (IMAPS) and 995 (POP3S) for bruteforce login attacks as well as scans on ports 80 (HTTP) and 443 (HTTPS) for vulnerable installations of phpMyAdmin and other web applications.|ipv4 hash:ip|15366 unique IPs|updated every 4 hours  from [this link](http://www.openbl.org/lists/base_180days.txt)
[openbl_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=openbl_1d)|[OpenBL.org](http://www.openbl.org/) last 24 hours IPs.  OpenBL.org is detecting, logging and reporting various types of internet abuse. Currently they monitor ports 21 (FTP), 22 (SSH), 23 (TELNET), 25 (SMTP), 110 (POP3), 143 (IMAP), 587 (Submission), 993 (IMAPS) and 995 (POP3S) for bruteforce login attacks as well as scans on ports 80 (HTTP) and 443 (HTTPS) for vulnerable installations of phpMyAdmin and other web applications.|ipv4 hash:ip|217 unique IPs|updated every 1 hour  from [this link](http://www.openbl.org/lists/base_1days.txt)
[openbl_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=openbl_30d)|[OpenBL.org](http://www.openbl.org/) last 30 days IPs.  OpenBL.org is detecting, logging and reporting various types of internet abuse. Currently they monitor ports 21 (FTP), 22 (SSH), 23 (TELNET), 25 (SMTP), 110 (POP3), 143 (IMAP), 587 (Submission), 993 (IMAPS) and 995 (POP3S) for bruteforce login attacks as well as scans on ports 80 (HTTP) and 443 (HTTPS) for vulnerable installations of phpMyAdmin and other web applications.|ipv4 hash:ip|3288 unique IPs|updated every 4 hours  from [this link](http://www.openbl.org/lists/base_30days.txt)
[openbl_360d](http://ktsaou.github.io/blocklist-ipsets/?ipset=openbl_360d)|[OpenBL.org](http://www.openbl.org/) last 360 days IPs.  OpenBL.org is detecting, logging and reporting various types of internet abuse. Currently they monitor ports 21 (FTP), 22 (SSH), 23 (TELNET), 25 (SMTP), 110 (POP3), 143 (IMAP), 587 (Submission), 993 (IMAPS) and 995 (POP3S) for bruteforce login attacks as well as scans on ports 80 (HTTP) and 443 (HTTPS) for vulnerable installations of phpMyAdmin and other web applications.|ipv4 hash:ip|28818 unique IPs|updated every 4 hours  from [this link](http://www.openbl.org/lists/base_360days.txt)
[openbl_60d](http://ktsaou.github.io/blocklist-ipsets/?ipset=openbl_60d)|[OpenBL.org](http://www.openbl.org/) last 60 days IPs.  OpenBL.org is detecting, logging and reporting various types of internet abuse. Currently they monitor ports 21 (FTP), 22 (SSH), 23 (TELNET), 25 (SMTP), 110 (POP3), 143 (IMAP), 587 (Submission), 993 (IMAPS) and 995 (POP3S) for bruteforce login attacks as well as scans on ports 80 (HTTP) and 443 (HTTPS) for vulnerable installations of phpMyAdmin and other web applications.|ipv4 hash:ip|4997 unique IPs|updated every 4 hours  from [this link](http://www.openbl.org/lists/base_60days.txt)
[openbl_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=openbl_7d)|[OpenBL.org](http://www.openbl.org/) last 7 days IPs.  OpenBL.org is detecting, logging and reporting various types of internet abuse. Currently they monitor ports 21 (FTP), 22 (SSH), 23 (TELNET), 25 (SMTP), 110 (POP3), 143 (IMAP), 587 (Submission), 993 (IMAPS) and 995 (POP3S) for bruteforce login attacks as well as scans on ports 80 (HTTP) and 443 (HTTPS) for vulnerable installations of phpMyAdmin and other web applications.|ipv4 hash:ip|1077 unique IPs|updated every 4 hours  from [this link](http://www.openbl.org/lists/base_7days.txt)
[openbl_90d](http://ktsaou.github.io/blocklist-ipsets/?ipset=openbl_90d)|[OpenBL.org](http://www.openbl.org/) last 90 days IPs.  OpenBL.org is detecting, logging and reporting various types of internet abuse. Currently they monitor ports 21 (FTP), 22 (SSH), 23 (TELNET), 25 (SMTP), 110 (POP3), 143 (IMAP), 587 (Submission), 993 (IMAPS) and 995 (POP3S) for bruteforce login attacks as well as scans on ports 80 (HTTP) and 443 (HTTPS) for vulnerable installations of phpMyAdmin and other web applications.|ipv4 hash:ip|6789 unique IPs|updated every 4 hours  from [this link](http://www.openbl.org/lists/base_90days.txt)
[openbl_all](http://ktsaou.github.io/blocklist-ipsets/?ipset=openbl_all)|[OpenBL.org](http://www.openbl.org/) last all IPs.  OpenBL.org is detecting, logging and reporting various types of internet abuse. Currently they monitor ports 21 (FTP), 22 (SSH), 23 (TELNET), 25 (SMTP), 110 (POP3), 143 (IMAP), 587 (Submission), 993 (IMAPS) and 995 (POP3S) for bruteforce login attacks as well as scans on ports 80 (HTTP) and 443 (HTTPS) for vulnerable installations of phpMyAdmin and other web applications.|ipv4 hash:ip|103324 unique IPs|updated every 4 hours  from [this link](http://www.openbl.org/lists/base_all.txt)
[palevo](http://ktsaou.github.io/blocklist-ipsets/?ipset=palevo)|[Abuse.ch Palevo tracker](https://palevotracker.abuse.ch) worm includes IPs which are being used as botnet C&C for the Palevo crimeware|ipv4 hash:ip|13 unique IPs|updated every 30 mins  from [this link](https://palevotracker.abuse.ch/blocklists.php?download=ipblocklist)
php_bad|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) bad web hosts (this list is composed using an RSS feed)|ipv4 hash:ip|disabled|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=b&rss=1)
[php_commenters](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_commenters)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) comment spammers (this list is composed using an RSS feed)|ipv4 hash:ip|50 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=c&rss=1)
[php_commenters_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_commenters_1d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) comment spammers (this list is composed using an RSS feed)|ipv4 hash:ip|91 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=c&rss=1)
[php_commenters_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_commenters_30d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) comment spammers (this list is composed using an RSS feed)|ipv4 hash:ip|807 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=c&rss=1)
[php_commenters_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_commenters_7d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) comment spammers (this list is composed using an RSS feed)|ipv4 hash:ip|243 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=c&rss=1)
[php_dictionary](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_dictionary)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) directory attackers (this list is composed using an RSS feed)|ipv4 hash:ip|50 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=d&rss=1)
[php_dictionary_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_dictionary_1d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) directory attackers (this list is composed using an RSS feed)|ipv4 hash:ip|99 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=d&rss=1)
[php_dictionary_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_dictionary_30d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) directory attackers (this list is composed using an RSS feed)|ipv4 hash:ip|1230 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=d&rss=1)
[php_dictionary_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_dictionary_7d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) directory attackers (this list is composed using an RSS feed)|ipv4 hash:ip|359 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=d&rss=1)
[php_harvesters](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_harvesters)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) harvesters (IPs that surf the internet looking for email addresses) (this list is composed using an RSS feed)|ipv4 hash:ip|50 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=h&rss=1)
[php_harvesters_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_harvesters_1d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) harvesters (IPs that surf the internet looking for email addresses) (this list is composed using an RSS feed)|ipv4 hash:ip|78 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=h&rss=1)
[php_harvesters_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_harvesters_30d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) harvesters (IPs that surf the internet looking for email addresses) (this list is composed using an RSS feed)|ipv4 hash:ip|518 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=h&rss=1)
[php_harvesters_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_harvesters_7d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) harvesters (IPs that surf the internet looking for email addresses) (this list is composed using an RSS feed)|ipv4 hash:ip|199 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=h&rss=1)
[php_spammers](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_spammers)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) spam servers (IPs used by spammers to send messages) (this list is composed using an RSS feed)|ipv4 hash:ip|50 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=s&rss=1)
[php_spammers_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_spammers_1d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) spam servers (IPs used by spammers to send messages) (this list is composed using an RSS feed)|ipv4 hash:ip|99 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=s&rss=1)
[php_spammers_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_spammers_30d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) spam servers (IPs used by spammers to send messages) (this list is composed using an RSS feed)|ipv4 hash:ip|1242 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=s&rss=1)
[php_spammers_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=php_spammers_7d)|[projecthoneypot.org](http://www.projecthoneypot.org/?rf=192670) spam servers (IPs used by spammers to send messages) (this list is composed using an RSS feed)|ipv4 hash:ip|361 unique IPs|updated every 1 hour  from [this link](http://www.projecthoneypot.org/list_of_ips.php?t=s&rss=1)
[proxyrss](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxyrss)|[proxyrss.com](http://www.proxyrss.com) open proxies syndicated from multiple sources.|ipv4 hash:ip|1259 unique IPs|updated every 4 hours  from [this link](http://www.proxyrss.com/proxylists/all.gz)
[proxyrss_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxyrss_1d)|[proxyrss.com](http://www.proxyrss.com) open proxies syndicated from multiple sources.|ipv4 hash:ip|2765 unique IPs|updated every 4 hours  from [this link](http://www.proxyrss.com/proxylists/all.gz)
[proxyrss_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxyrss_30d)|[proxyrss.com](http://www.proxyrss.com) open proxies syndicated from multiple sources.|ipv4 hash:ip|21124 unique IPs|updated every 4 hours  from [this link](http://www.proxyrss.com/proxylists/all.gz)
[proxyrss_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxyrss_7d)|[proxyrss.com](http://www.proxyrss.com) open proxies syndicated from multiple sources.|ipv4 hash:ip|6480 unique IPs|updated every 4 hours  from [this link](http://www.proxyrss.com/proxylists/all.gz)
[proxyspy](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxyspy)|[ProxySpy](http://spys.ru/en/) open proxies (updated hourly)|ipv4 hash:ip|295 unique IPs|updated every 1 hour  from [this link](http://txt.proxyspy.net/proxy.txt)
[proxyspy_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxyspy_1d)|[ProxySpy](http://spys.ru/en/) open proxies (updated hourly)|ipv4 hash:ip|1215 unique IPs|updated every 1 hour  from [this link](http://txt.proxyspy.net/proxy.txt)
[proxyspy_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxyspy_30d)|[ProxySpy](http://spys.ru/en/) open proxies (updated hourly)|ipv4 hash:ip|3587 unique IPs|updated every 1 hour  from [this link](http://txt.proxyspy.net/proxy.txt)
[proxyspy_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxyspy_7d)|[ProxySpy](http://spys.ru/en/) open proxies (updated hourly)|ipv4 hash:ip|2823 unique IPs|updated every 1 hour  from [this link](http://txt.proxyspy.net/proxy.txt)
[proxz](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxz)|[proxz.com](http://www.proxz.com) open proxies (this list is composed using an RSS feed)|ipv4 hash:ip|24 unique IPs|updated every 1 hour  from [this link](http://www.proxz.com/proxylists.xml)
[proxz_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxz_1d)|[proxz.com](http://www.proxz.com) open proxies (this list is composed using an RSS feed)|ipv4 hash:ip|209 unique IPs|updated every 1 hour  from [this link](http://www.proxz.com/proxylists.xml)
[proxz_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxz_30d)|[proxz.com](http://www.proxz.com) open proxies (this list is composed using an RSS feed)|ipv4 hash:ip|2499 unique IPs|updated every 1 hour  from [this link](http://www.proxz.com/proxylists.xml)
[proxz_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=proxz_7d)|[proxz.com](http://www.proxz.com) open proxies (this list is composed using an RSS feed)|ipv4 hash:ip|894 unique IPs|updated every 1 hour  from [this link](http://www.proxz.com/proxylists.xml)
[ri_connect_proxies](http://ktsaou.github.io/blocklist-ipsets/?ipset=ri_connect_proxies)|[rosinstrument.com](http://www.rosinstrument.com) open CONNECT proxies (this list is composed using an RSS feed)|ipv4 hash:ip|148 unique IPs|updated every 1 hour  from [this link](http://tools.rosinstrument.com/proxy/plab100.xml)
[ri_connect_proxies_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=ri_connect_proxies_1d)|[rosinstrument.com](http://www.rosinstrument.com) open CONNECT proxies (this list is composed using an RSS feed)|ipv4 hash:ip|367 unique IPs|updated every 1 hour  from [this link](http://tools.rosinstrument.com/proxy/plab100.xml)
[ri_connect_proxies_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=ri_connect_proxies_30d)|[rosinstrument.com](http://www.rosinstrument.com) open CONNECT proxies (this list is composed using an RSS feed)|ipv4 hash:ip|3432 unique IPs|updated every 1 hour  from [this link](http://tools.rosinstrument.com/proxy/plab100.xml)
[ri_connect_proxies_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=ri_connect_proxies_7d)|[rosinstrument.com](http://www.rosinstrument.com) open CONNECT proxies (this list is composed using an RSS feed)|ipv4 hash:ip|1156 unique IPs|updated every 1 hour  from [this link](http://tools.rosinstrument.com/proxy/plab100.xml)
[ri_web_proxies](http://ktsaou.github.io/blocklist-ipsets/?ipset=ri_web_proxies)|[rosinstrument.com](http://www.rosinstrument.com) open HTTP proxies (this list is composed using an RSS feed)|ipv4 hash:ip|140 unique IPs|updated every 1 hour  from [this link](http://tools.rosinstrument.com/proxy/l100.xml)
[ri_web_proxies_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=ri_web_proxies_1d)|[rosinstrument.com](http://www.rosinstrument.com) open HTTP proxies (this list is composed using an RSS feed)|ipv4 hash:ip|598 unique IPs|updated every 1 hour  from [this link](http://tools.rosinstrument.com/proxy/l100.xml)
[ri_web_proxies_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=ri_web_proxies_30d)|[rosinstrument.com](http://www.rosinstrument.com) open HTTP proxies (this list is composed using an RSS feed)|ipv4 hash:ip|7017 unique IPs|updated every 1 hour  from [this link](http://tools.rosinstrument.com/proxy/l100.xml)
[ri_web_proxies_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=ri_web_proxies_7d)|[rosinstrument.com](http://www.rosinstrument.com) open HTTP proxies (this list is composed using an RSS feed)|ipv4 hash:ip|2172 unique IPs|updated every 1 hour  from [this link](http://tools.rosinstrument.com/proxy/l100.xml)
[sblam](http://ktsaou.github.io/blocklist-ipsets/?ipset=sblam)|[sblam.com](http://sblam.com) IPs used by web form spammers, during the last month|ipv4 hash:ip|17337 unique IPs|updated every 1 day  from [this link](http://sblam.com/blacklist.txt)
[shunlist](http://ktsaou.github.io/blocklist-ipsets/?ipset=shunlist)|[AutoShun.org](http://autoshun.org/) IPs identified as hostile by correlating logs from distributed snort installations running the autoshun plugin|ipv4 hash:ip|993 unique IPs|updated every 4 hours  from [this link](http://www.autoshun.org/files/shunlist.csv)
[snort_ipfilter](http://ktsaou.github.io/blocklist-ipsets/?ipset=snort_ipfilter)|[labs.snort.org](https://labs.snort.org/) supplied IP blacklist (this list seems to be updated frequently, but we found no information about it)|ipv4 hash:ip|9527 unique IPs|updated every 12 hours  from [this link](http://labs.snort.org/feeds/ip-filter.blf)
[sorbs_dul](http://ktsaou.github.io/blocklist-ipsets/?ipset=sorbs_dul)|[Sorbs.net](https://www.sorbs.net/) DUL, Dynamic User IPs extracted from deltas.|ipv4 hash:net|34 subnets, 564224 unique IPs|
[sorbs_http](http://ktsaou.github.io/blocklist-ipsets/?ipset=sorbs_http)|[Sorbs.net](https://www.sorbs.net/) HTTP proxies, extracted from deltas.|ipv4 hash:net|938 subnets, 1006 unique IPs|
[sorbs_misc](http://ktsaou.github.io/blocklist-ipsets/?ipset=sorbs_misc)|[Sorbs.net](https://www.sorbs.net/) MISC proxies, extracted from deltas.|ipv4 hash:net|938 subnets, 1006 unique IPs|
[sorbs_new_spam](http://ktsaou.github.io/blocklist-ipsets/?ipset=sorbs_new_spam)|[Sorbs.net](https://www.sorbs.net/) NEW Spam senders, extracted from deltas.|ipv4 hash:net|29977 subnets, 30648 unique IPs|
[sorbs_recent_spam](http://ktsaou.github.io/blocklist-ipsets/?ipset=sorbs_recent_spam)|[Sorbs.net](https://www.sorbs.net/) RECENT Spam senders, extracted from deltas.|ipv4 hash:net|29977 subnets, 30648 unique IPs|
[sorbs_smtp](http://ktsaou.github.io/blocklist-ipsets/?ipset=sorbs_smtp)|[Sorbs.net](https://www.sorbs.net/) SMTP Open Relays, extracted from deltas.|ipv4 hash:net|4 subnets, 4 unique IPs|
[sorbs_socks](http://ktsaou.github.io/blocklist-ipsets/?ipset=sorbs_socks)|[Sorbs.net](https://www.sorbs.net/) SOCKS proxies, extracted from deltas.|ipv4 hash:net|938 subnets, 1006 unique IPs|
[sorbs_spam](http://ktsaou.github.io/blocklist-ipsets/?ipset=sorbs_spam)|[Sorbs.net](https://www.sorbs.net/) Spam senders, extracted from deltas.|ipv4 hash:net|29977 subnets, 30648 unique IPs|
[sorbs_web](http://ktsaou.github.io/blocklist-ipsets/?ipset=sorbs_web)|[Sorbs.net](https://www.sorbs.net/) WEB exploits, extracted from deltas.|ipv4 hash:net|2380 subnets, 2381 unique IPs|
[spamhaus_drop](http://ktsaou.github.io/blocklist-ipsets/?ipset=spamhaus_drop)|[Spamhaus.org](http://www.spamhaus.org) DROP list (according to their site this list should be dropped at tier-1 ISPs globaly)|ipv4 hash:net|673 subnets, 21171712 unique IPs|updated every 12 hours  from [this link](http://www.spamhaus.org/drop/drop.txt)
[spamhaus_edrop](http://ktsaou.github.io/blocklist-ipsets/?ipset=spamhaus_edrop)|[Spamhaus.org](http://www.spamhaus.org) EDROP (extended matches that should be used with DROP)|ipv4 hash:net|70 subnets, 624896 unique IPs|updated every 12 hours  from [this link](http://www.spamhaus.org/drop/edrop.txt)
[sslbl](http://ktsaou.github.io/blocklist-ipsets/?ipset=sslbl)|[Abuse.ch SSL Blacklist](https://sslbl.abuse.ch/) bad SSL traffic related to malware or botnet activities|ipv4 hash:ip|105 unique IPs|updated every 30 mins  from [this link](https://sslbl.abuse.ch/blacklist/sslipblacklist.csv)
[sslbl_aggressive](http://ktsaou.github.io/blocklist-ipsets/?ipset=sslbl_aggressive)|[Abuse.ch SSL Blacklist](https://sslbl.abuse.ch/) The aggressive version of the SSL IP Blacklist contains all IPs that SSLBL ever detected being associated with a malicious SSL certificate. Since IP addresses can be reused (e.g. when the customer changes), this blacklist may cause false positives. Hence I highly recommend you to use the standard version instead of the aggressive one.|ipv4 hash:ip|757 unique IPs|updated every 30 mins  from [this link](https://sslbl.abuse.ch/blacklist/sslipblacklist_aggressive.csv)
[stopforumspam_180d](http://ktsaou.github.io/blocklist-ipsets/?ipset=stopforumspam_180d)|[StopForumSpam.com](http://www.stopforumspam.com) IPs used by forum spammers (last 180 days)|ipv4 hash:ip|504127 unique IPs|updated every 1 day  from [this link](http://www.stopforumspam.com/downloads/listed_ip_180.zip)
[stopforumspam_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=stopforumspam_1d)|[StopForumSpam.com](http://www.stopforumspam.com) IPs used by forum spammers in the last 24 hours|ipv4 hash:ip|9583 unique IPs|updated every 1 hour  from [this link](http://www.stopforumspam.com/downloads/listed_ip_1.zip)
[stopforumspam_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=stopforumspam_30d)|[StopForumSpam.com](http://www.stopforumspam.com) IPs used by forum spammers (last 30 days)|ipv4 hash:ip|93496 unique IPs|updated every 1 day  from [this link](http://www.stopforumspam.com/downloads/listed_ip_30.zip)
stopforumspam_365d|[StopForumSpam.com](http://www.stopforumspam.com) IPs used by forum spammers (last 365 days)|ipv4 hash:ip|disabled|updated every 1 day  from [this link](http://www.stopforumspam.com/downloads/listed_ip_365.zip)
[stopforumspam_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=stopforumspam_7d)|[StopForumSpam.com](http://www.stopforumspam.com) IPs used by forum spammers (last 7 days)|ipv4 hash:ip|34578 unique IPs|updated every 1 day  from [this link](http://www.stopforumspam.com/downloads/listed_ip_7.zip)
[stopforumspam_90d](http://ktsaou.github.io/blocklist-ipsets/?ipset=stopforumspam_90d)|[StopForumSpam.com](http://www.stopforumspam.com) IPs used by forum spammers (last 90 days)|ipv4 hash:ip|233949 unique IPs|updated every 1 day  from [this link](http://www.stopforumspam.com/downloads/listed_ip_90.zip)
stopforumspam_ever|[StopForumSpam.com](http://www.stopforumspam.com) all IPs used by forum spammers, ever (normally you don't want to use this ipset, use the hourly one which includes last 24 hours IPs or the 7 days one)|ipv4 hash:ip|disabled|updated every 1 day  from [this link](http://www.stopforumspam.com/downloads/bannedips.zip)
[talosintel_ipfilter](http://ktsaou.github.io/blocklist-ipsets/?ipset=talosintel_ipfilter)|[TalosIntel.com](http://talosintel.com/additional-resources/) List of known malicious network threats|ipv4 hash:ip|6312 unique IPs|updated every 4 hours  from [this link](http://talosintel.com/files/additional_resources/ips_blacklist/ip-filter.blf)
[tor_exits](http://ktsaou.github.io/blocklist-ipsets/?ipset=tor_exits)|[TorProject.org](https://www.torproject.org) list of all current TOR exit points (TorDNSEL)|ipv4 hash:ip|1002 unique IPs|updated every 30 mins  from [this link](https://check.torproject.org/exit-addresses)
[virbl](http://ktsaou.github.io/blocklist-ipsets/?ipset=virbl)|[VirBL](http://virbl.bit.nl/) is a project of which the idea was born during the RIPE-48 meeting. The plan was to get reports of virusscanning mailservers, and put the IP-addresses that were reported to send viruses on a blacklist.|ipv4 hash:ip|26 unique IPs|updated every 1 hour  from [this link](http://virbl.bit.nl/download/virbl.dnsbl.bit.nl.txt)
[voipbl](http://ktsaou.github.io/blocklist-ipsets/?ipset=voipbl)|[VoIPBL.org](http://www.voipbl.org/) a distributed VoIP blacklist that is aimed to protects against VoIP Fraud and minimizing abuse for network that have publicly accessible PBX's. Several algorithms, external sources and manual confirmation are used before they categorize something as an attack and determine the threat level.|ipv4 hash:net|11423 subnets, 11895 unique IPs|updated every 4 hours  from [this link](http://www.voipbl.org/update/)
[xroxy](http://ktsaou.github.io/blocklist-ipsets/?ipset=xroxy)|[xroxy.com](http://www.xroxy.com) open proxies (this list is composed using an RSS feed)|ipv4 hash:ip|61 unique IPs|updated every 1 hour  from [this link](http://www.xroxy.com/proxyrss.xml)
[xroxy_1d](http://ktsaou.github.io/blocklist-ipsets/?ipset=xroxy_1d)|[xroxy.com](http://www.xroxy.com) open proxies (this list is composed using an RSS feed)|ipv4 hash:ip|127 unique IPs|updated every 1 hour  from [this link](http://www.xroxy.com/proxyrss.xml)
[xroxy_30d](http://ktsaou.github.io/blocklist-ipsets/?ipset=xroxy_30d)|[xroxy.com](http://www.xroxy.com) open proxies (this list is composed using an RSS feed)|ipv4 hash:ip|617 unique IPs|updated every 1 hour  from [this link](http://www.xroxy.com/proxyrss.xml)
[xroxy_7d](http://ktsaou.github.io/blocklist-ipsets/?ipset=xroxy_7d)|[xroxy.com](http://www.xroxy.com) open proxies (this list is composed using an RSS feed)|ipv4 hash:ip|274 unique IPs|updated every 1 hour  from [this link](http://www.xroxy.com/proxyrss.xml)
[zeus](http://ktsaou.github.io/blocklist-ipsets/?ipset=zeus)|[Abuse.ch Zeus tracker](https://zeustracker.abuse.ch) standard, contains the same data as the ZeuS IP blocklist (zeus_badips) but with the slight difference that it doesn't exclude hijacked websites (level 2) and free web hosting providers (level 3). This means that this blocklist contains all IPv4 addresses associated with ZeuS C&Cs which are currently being tracked by ZeuS Tracker. Hence this blocklist will likely cause some false positives.|ipv4 hash:ip|201 unique IPs|updated every 30 mins  from [this link](https://zeustracker.abuse.ch/blocklist.php?download=ipblocklist)
[zeus_badips](http://ktsaou.github.io/blocklist-ipsets/?ipset=zeus_badips)|[Abuse.ch Zeus tracker](https://zeustracker.abuse.ch) badips includes IPv4 addresses that are used by the ZeuS trojan. It is the recommened blocklist if you want to block only ZeuS IPs. It excludes IP addresses that ZeuS Tracker believes to be hijacked (level 2) or belong to a free web hosting provider (level 3). Hence the false postive rate should be much lower compared to the standard ZeuS IP blocklist.|ipv4 hash:ip|199 unique IPs|updated every 30 mins  from [this link](https://zeustracker.abuse.ch/blocklist.php?download=badips)
