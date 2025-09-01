---
layout: post
title:  "TB - Report: Phishing"
date:   2025-08-31
author: C. Casquatch
comments: false
tag: ['phishing', 'ledger',]
---

-- Phishing Attack regarding Ledger - this was an excercise for myself to see what I could find out. 

## Overview

There are letters being sent to Ledger customers that try to get the victim to input a secret passphrase into a website that is owned by scammers.

![Photo1](/assets/images/LedgerPhish/ledgerletter.HEIC)


[#StopTheScammers](https://www.ledger.com/phishing-campaigns-status)

I started investigating this a couple of days ago (roughly 29-30th of August 2025). Since then (currently 1st of September 2025) Virustotal has come in with the goods, so i can post them here. 

As of 17 hours ago Virustotal shows 1 security vendor flagging 'ledger.easycheckactivate.com', 1 for 'easycheckactivate.com', and 2 for 104.250.164.8.
You can check the detection, details, and relations for all three on the Virustotal website [here](https://www.virustotal.com/gui/ip-address/104.250.164.8/detection), [here](https://www.virustotal.com/gui/url/1cf8c362d67780e1a408c06be457c79b16794db23b22bd1e4de0d7d88ed020aa/details), and [here](https://www.virustotal.com/gui/url/502fe48c222dc06100fad157a8556e1709c01bde5adb8a4e59721b8c1f8115b5)

URL.io also has potential malicious activity listed. I got most of my details from here before they arrived on VT.

- The IP address for ledger.easycheckactivate.com is 104.250.164.8
- It comes from Iran and belongs to LimitedNetwork-AS Network LTD, GB.
- A TLS certificate was issued by XeroSSL RSA Domain Secure Site CA on August 28th 2025 and if valid for 3 months.

This domain name is targeting the [Ledger](https://www.ledger.com/ledger-live) Crypto Wallet brand. We assume costumer information for this phishing attempt has come from the [2020 Breach](https://haveibeenpwned.com/Breach/Ledger#:~:text=In%20June%202020%2C%20the%20hardware%20crypto%20wallet%20manufacturer,and%20included%20names%2C%20physical%20addresses%20and%20phone%20numbers.) that has compromised email addresses, names, phone numbers, and physical addresses. 

You can also read Ledger's response [here](https://www.ledger.com/addressing-the-july-2020-e-commerce-and-marketing-data-breach)

I found an article talking about it [here](https://meme-insider.com/en/article/ledger-transaction-check-update-2025/) - they just talk about the update but don't mention anything about a phishing campaign.

There are also a couple of reddit posts with similar or the same kind of letters - all calling out the phishing attempt.

* * *

## Start:

I wanted to figure out where the letter came from. So i started with the physical. 

I looked into the barcode on the envelope.
![Photo2](/assets/images/LedgerPhish/envelope.heic)


I did not take care with the stamp. I forcused on the barcode and lettering on top. 

- SWLF - Sydney West Letter Facility
- CFCP - Culler Facer Canceller Preparation, for sorting small letters (The CFCP applies a barcode using black ink)
- Machine number 462
- Scanned at 21.08.2025 @ 2130

I left the barcode covered, as the DPID (delivery Point Identifier) is there. 
- The customer information have been left as a 'filler' mark, which means that we don't get to know where it came from other than it was processed in Sydney.
- Basic bitch information on barcoding [This](https://auspost.com.au/content/dam/auspost_corp/media/documents/barcode-and-labels-guide.pdf) and [This](https://auspost.com.au/content/dam/auspost_corp/media/documents/barcoding-fact-sheet.pdf)
From [here](https://tritonstore.com.au/australia-post-barcode/) I gathered that it is a 4-state 37-cust barcode.

But [This](https://auspost.com.au/content/dam/auspost_corp/media/documents/customer-barcode-technical-specifications-aug2012.pdf) document is the one I used to decode the bar until i figured out that the customer information
is only a 'filler' bar. Which means I won't be able to pinpoint anything about the sender from here.
There are other options for decoding that i didn't use; they include coding/programming. 


[This](https://www.dynamsoft.com/barcode-reader/barcode-types/australian-post/) site has a good examlpe of what i was hoping to have in the 'customer information' section of the barcode. But alas, we are not so lucky.

## Middle 

After I decided that I had enough Sherlocking my way through the envelope, I decided it was time to try and get the url for the QR code. 

I opened the camera app on my phone and hovered over the QR code. When the url appeared, I copied it and put it through a few tests. 

URL: https(:)//ledger(.)easycheckactivate(.)com/cloudflare/(BASE64 Encoded target name)

The basis of the url is clever. We can see it trying to impersonate the ledger wedbsite with a subdomain at the front. 
- The domain 'easycheckactivate.com' appears to impersonate legitimate financial/banking services. 
- And the path at the end? It's a Base64 encoded string using the victim's name. I have left it out of this report as with other personal details.

I started with urlvoid.com:
![Photo3](/assets/images/LedgerPhish/urlvoidcom.png)

Here it says the domain was registered on the 2nd of August 2025. The letter was recieved a week after this registration. 
The letter also indicates a time crunch of the end of August for the target to follow the instructions from the QR code to apply a new security feature called 'Transaction Check'. 
- We can update ourselves about 'Transaction Check' [here](https://www.ledger.com/academy/glossary/transaction-check)

Next I did a whois lookup on the IP address from ipvoid.com:

For 104.250.164.8 I recieved the following information from this site:

```
# start

NetRange:       104.250.160.0 - 104.250.191.255
CIDR:           104.250.160.0/19
NetName:        PUREVPN
NetHandle:      NET-104-250-160-0-1
Parent:         NET104 (NET-104-0-0-0-0)
NetType:        Direct Allocation
OriginAS:       
Organization:   Secure Internet LLC (SIL-69)
RegDate:        2014-12-18
Updated:        2014-12-18
Ref:            https://rdap.arin.net/registry/ip/104.250.160.0


OrgName:        Secure Internet LLC
OrgId:          SIL-69
Address:        10685-B Hazelhurst Dr. #14783
Address:        Houston, TX 77043 USA
City:           Houston
StateProv:      TX
PostalCode:     77043
Country:        US
RegDate:        2013-01-17
Updated:        2024-11-25
Ref:            https://rdap.arin.net/registry/entity/SIL-69


OrgNOCHandle: GADIT3-ARIN
OrgNOCName:   Gadit, Uzair 
OrgNOCPhone:  +1-217-651-4225 
OrgNOCEmail:  admin@pointtoserver.com
OrgNOCRef:    https://rdap.arin.net/registry/entity/GADIT3-ARIN

OrgTechHandle: GADIT3-ARIN
OrgTechName:   Gadit, Uzair 
OrgTechPhone:  +1-217-651-4225 
OrgTechEmail:  admin@pointtoserver.com
OrgTechRef:    https://rdap.arin.net/registry/entity/GADIT3-ARIN

OrgAbuseHandle: GADIT3-ARIN
OrgAbuseName:   Gadit, Uzair 
OrgAbusePhone:  +1-217-651-4225 
OrgAbuseEmail:  admin@pointtoserver.com
OrgAbuseRef:    https://rdap.arin.net/registry/entity/GADIT3-ARIN

# end
```

Ahhh, VPN. of course. don't be stupid. 
- I looked into GADIT3-ARIN and found [this](https://www.whois.com/whois/104.37.4.3) and [this](https://www.abuseipdb.com/whois/45.74.0.243)
- I found Uzair Gadit [here](https://en.incarabia.com/disruptcom-commits-us100million-to-ai-startups-720046.html), [here](https://techeconomy.ng/disrupt-com-to-invest-100m-in-next-gen-ai-startups/), and [here](https://www.crunchbase.com/person/uzair-gadit) - this last one has that PureVPN from the information above.

OK, let's do a whois lookup on the domain 'easycheckactivate.com'. This time i used whois.domaintools.com - because why not. 

![Photo4](/assets/images/LedgerPhish/whoisdomaintools.png)

info from this check:
```
Domain Name: EASYCHECKACTIVATE.COM
Registry Domain ID: 3006320236_domain_com-vrsn
Registrar WHOIS Server: ver.whois.matbao.net
Registrar URL: https://www.matbao.net
Updated Date: 2025-08-27t23:48:26z
Creation Date: 2025-08-02t09:45:31z
Registrar Registration Expiration Date: 2026-08-02t09:45:31z
Registrar: mat bao corporation
Registrar IANA ID: 1586
Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
Registry Registrant ID: Not Available From Registry
Registrant Name: Domain Admin
Registrant Organization: Whoisprotection.cc
Registrant Street: Lot 2-1, Incubator 1, Technology Park Malaysia, Bukit Jalil  
Registrant City: Kuala Lumpur
Registrant State/Province: Wilayah Persekutuan
Registrant Postal Code: 57000
Registrant Country: MY
Registrant Phone: +60.389966788
Registrant Phone Ext: 
Registrant Fax: +60.389966788
Registrant Fax Ext: 
Registrant Email: 
Registry Admin ID: Not Available From Registry
Admin Name: Domain Admin
Admin Organization: Whoisprotection.cc
Admin Street: Lot 2-1, Incubator 1, Technology Park Malaysia, Bukit Jalil 
Admin City: Kuala Lumpur
Admin State/Province: Wilayah Persekutuan
Admin Postal Code: 57000
Admin Country: MY
Admin Phone: +60.389966788
Admin Phone Ext: 
Admin Fax: +60.389966788
Admin Fax Ext: 
Admin Email: 
Registry Tech ID: Not Available From Registry
Tech Name: Domain Admin
Tech Organization: Whoisprotection.cc
Tech Street: Lot 2-1, Incubator 1, Technology Park Malaysia, Bukit Jalil 
Tech City: Kuala Lumpur
Tech State/Province: Wilayah Persekutuan
Tech Postal Code: 57000
Tech Country: MY
Tech Phone: +60.389966788
Tech Phone Ext: 
Tech Fax: +60.389966788
Tech Fax Ext: 
Tech Email: 
Name Server: ns1.lethosting.info
Name Server: ns2.lethosting.info

DNSSEC: unsigned

Registrar Abuse Contact Email: 
Registrar Abuse Contact Phone: +84-36229999 EXT :8899
URL of the ICANN WHOIS Data Problem Reporting System: http://wdprs.internic.net/
```

what's matbao.net?

![Photo5](/assets/images/LedgerPhish/matbao.png)

Oh, right. Domain registration, web hosting services, website development, digital certificates, cloud computing. 

Keep up. Vietnam for domain services, Malaysia for fake registration, Iran for hosting. Ok. 

At this point i want to do a traceroute on the IP and the domain..

my traceroute for ledger.easycheckactivate.com:
```
traceroute to ledger.easycheckactivate.com (104.250.164.8), 64 hops max, 40 byte packets
1  [LOCAL ROUTER]  ~2 ms
2  [ISP GATEWAY]  ~25 ms  
3  [ISP NETWORK]  ~25 ms
4  [ISP NETWORK]  ~30 ms
 5  203-220-16-165.tpgi.com.au (203.220.16.165)  40.688 ms  34.626 ms  26.892 ms
 6  * nme-apt-col-wgw3-be90.tpgi.com.au (203.219.58.125)  30.951 ms  26.079 ms
 7  nme-apt-col-crt2-be-100.tpgi.com.au (27.32.160.2)  24.860 ms
    nme-apt-col-crt1-be-100.tpgi.com.au (27.32.160.1)  29.166 ms
    nme-apt-col-crt2-be-100.tpgi.com.au (27.32.160.2)  28.249 ms
 8  syd-apt-ros-crt4-be-60.tpgi.com.au (203.26.22.121)  39.372 ms
    syd-apt-ros-crt3-be-60.tpgi.com.au (203.26.22.117)  40.055 ms
    syd-apt-ros-crt4-be-60.tpgi.com.au (203.26.22.121)  31.749 ms
 9  syd-apt-ros-int3-be-200.tpg.com.au (203.29.134.122)  26.784 ms  37.828 ms
    syd-apt-ros-int3-be-100.tpg.com.au (203.29.134.59)  30.197 ms
10  ix-be-12.ecore1.lvw-losangeles.as6453.net (64.86.197.96)  305.700 ms  275.020 ms  204.539 ms
11  if-ae-45-2.tcore1.lvw-losangeles.as6453.net (64.86.197.56)  410.215 ms  361.132 ms *
12  if-ae-35-2.tcore1.sv1-santaclara.as6453.net (207.45.219.7)  349.728 ms  409.199 ms  354.271 ms
13  if-ae-0-3.tcore2.sv1-santaclara.as6453.net (63.243.250.61)  465.096 ms  374.879 ms *
14  * * *
15  * * *
16  * * *
17  if-bundle-29-2.qcore5.ldn-london.as6453.net (80.231.76.127)  352.420 ms * *
18  if-bundle-8-2.qcore2.av2-amsterdam.as6453.net (195.219.136.118)  430.173 ms *  420.661 ms
19  if-bundle-8-2.qcore2.av2-amsterdam.as6453.net (195.219.136.118)  409.341 ms *  473.310 ms
20  if-ae-38-2.thar2.hnn-haarlem.as6453.net (80.231.152.9)  359.721 ms  408.903 ms  406.476 ms
21  edge1.ams1.irm.macarne.com (92.118.206.3)  394.373 ms  408.924 ms  409.522 ms
22  edge1.ams2.nikhef.macarne.com (140.235.115.1)  347.208 ms  341.226 ms  368.134 ms
23  edge3.ams2.nikhef.macarne.com (140.235.115.150)  409.869 ms  408.500 ms  409.644 ms
24  ae6.4028.core2.eyg.as44592.net (194.62.6.9)  409.933 ms  347.072 ms
    ae6.4028.core1.eyg.as44592.net (194.62.6.8)  388.890 ms
25  * ae18.1179.edge-vrf.cr1.egh.as49581.net (185.91.127.86)  423.350 ms  370.456 ms
26  104.250.164.8 (104.250.164.8)  355.778 ms  430.869 ms  349.575 ms
```
(changed first 4 lines for privacy)
Our hops include Australia, Los Angeles, Santa Clara, London, Amsterdam. 

The last three lines, the traffic goes through two different network providers (AS44592 -> AS49581) before reaching a server that's deliberately obscuring its' hostname. 

The last line shows the final destination, the server hosting the scam site. 


Now traceroute for the IP address:

```
traceroute to 104.250.164.8 (104.250.164.8), 64 hops max, 40 byte packets
1  [LOCAL ROUTER]  ~2 ms
2  [ISP GATEWAY]  ~25 ms  
3  [ISP NETWORK]  ~25 ms
4  [ISP NETWORK]  ~30 ms
 5  203-220-16-161.tpgi.com.au (203.220.16.161)  36.462 ms  21.995 ms
    203-220-16-165.tpgi.com.au (203.220.16.165)  30.826 ms
 6  nme-apt-col-wgw3-be90.tpgi.com.au (203.219.58.125)  29.193 ms  30.082 ms  30.253 ms
 7  nme-apt-col-crt2-be-100.tpgi.com.au (27.32.160.2)  20.250 ms
    nme-apt-col-crt1-be-200.tpgi.com.au (27.32.160.65)  30.004 ms
    nme-apt-col-crt2-be-100.tpgi.com.au (27.32.160.2)  28.422 ms
 8  syd-apt-ros-crt4-be-60.tpgi.com.au (203.26.22.121)  38.294 ms
    syd-apt-ros-crt3-be-60.tpgi.com.au (203.26.22.117)  35.615 ms
    syd-apt-ros-crt4-be-60.tpgi.com.au (203.26.22.121)  39.952 ms
 9  syd-apt-ros-int3-be-100.tpg.com.au (203.29.134.59)  37.549 ms  39.725 ms  39.261 ms
10  sjo-b23-link.ip.twelve99.net (62.115.188.4)  200.933 ms  189.103 ms  189.326 ms
11  sjo-bb1-link.ip.twelve99.net (62.115.139.16)  190.575 ms  189.125 ms  189.037 ms
12  den-bb2-link.ip.twelve99.net (62.115.139.113)  268.534 ms
    den-bb1-link.ip.twelve99.net (62.115.139.105)  217.220 ms  219.824 ms
13  kanc-bb2-link.ip.twelve99.net (62.115.137.115)  392.523 ms
    chi-bb1-link.ip.twelve99.net (62.115.115.77)  349.645 ms
    kanc-bb2-link.ip.twelve99.net (62.115.137.115)  404.502 ms
14  chi-bb2-link.ip.twelve99.net (62.115.136.102)  408.500 ms  389.320 ms
    nyk-bb5-link.ip.twelve99.net (62.115.139.32)  340.997 ms
15  * ewr-bb2-link.ip.twelve99.net (62.115.132.134)  408.984 ms  349.303 ms
16  ldn-b3-link.ip.twelve99.net (62.115.140.73)  409.497 ms
    ldn-bb2-link.ip.twelve99.net (62.115.139.247)  412.965 ms  322.519 ms
17  ldn-b3-link.ip.twelve99.net (62.115.140.71)  341.515 ms
    interlinkgmbh-ic-381329.ip.twelve99-cust.net (62.115.154.161)  412.104 ms
    ldn-b3-link.ip.twelve99.net (62.115.122.181)  320.915 ms
18  r4-lon1-gb.as5405.net (94.103.180.20)  407.995 ms
    interlinkgmbh-ic-381329.ip.twelve99-cust.net (62.115.154.161)  334.327 ms  336.538 ms
19  r1-ams2-nl.as5405.net (94.103.180.68)  333.126 ms
    r4-lon1-gb.as5405.net (94.103.180.20)  390.466 ms
    r1-ams2-nl.as5405.net (94.103.180.68)  331.727 ms
20  r1-ams2-nl.as5405.net (94.103.180.68)  338.816 ms
    45.153.83.101 (45.153.83.101)  336.508 ms  319.293 ms
21  45.153.83.101 (45.153.83.101)  335.728 ms
    45.153.83.99 (45.153.83.99)  330.699 ms *
22  r1-ams2-nl.as5405.net (94.103.180.68)  355.339 ms
    45.153.83.101 (45.153.83.101)  425.097 ms
    ae18.1179.edge-vrf.cr1.egh.as49581.net (185.91.127.86)  408.355 ms
23  104.250.164.8 (104.250.164.8)  338.711 ms *  333.661 ms
```
(changed first 4 lines for privacy)

This shows a different route to the same destination.
- Hops 10-16: Twelve99 Network (Telia)
- Hop 10: San Jose, California (sjo = San Jose)
- Hop 11: San Jose backbone
- Hop 12: Denver, Colorado (den)
- Hop 13: Kansas City (kanc) and Chicago (chi)
- Hop 14: Chicago and New York (nyk)
- Hop 15: Newark (ewr)
- Hop 16: London (ldn)

- Hops 17-23: Final network hops
- Hop 17-18: Interlink GmbH customer connection and AS5405 network
- Hop 19-20: Amsterdam, Netherlands (ams2-nl)
- Hop 22: Same AS49581 network you saw before
- Hop 23: Final destination

What this reveals:

Multiple routing paths exist to this server - suggesting it's using anycast or load balancing.
Same final destination networks (AS49581) in both traces - confirms the server infrastructure.
Consistent European hosting despite different routes getting there.

Next, I have a look at urlscan.io for 'ledger(.)easycheckactivate(.)com' and this is what we see first:
(This was before Virustotal had been updated with the information of the IP address)
![Photo6](/assets/images/LedgerPhish/urlscanio1.png)

In the end, Virustotal confirms what we have revealed throughout this process. 

* * *

## End

I decided to stop here. I've had my fun. 

What have we learned, ladies and germs?

Targeted, Not Random
- The base64-encoded URL contained a specific name, indicating this isn't mass spam but targeted phishing.
- Suggests scammers used the obtained data from the 2020 breach.

Geographic Deception
- Domain registered shows Kuala Lumpur (likely fake/proxy servers)
- Actual Server IP geolocation points to Iran
- Multiple routing paths through US/Europe to same destination. Clear attempt to obscure true origins.

Sophisticated Operation
- Professional distributed hosting and infrastructure with load balancing.
- Anycast routing suggesting resilient, hard-to-block setup.
- Not amateur scammers - organised crime?

Cryptocurrency Focus
- "Ledger" subdomain targeting crypto hardware wallet users.
- PHysical mail + QR code attack vector shows multi-channel approach.
- High-value target demographic (crypto investors)

Security Implications
- Server deliberately hiding hostname information.
- International infrastructure makes law inforcement difficult.
- Physical mail delivery to Australia suggests broad international reach.
- Combination of Iranian hosting + Malaysian registration complicates jurisdiction.

Bottom line: This is a well-resourced, internatonally coordinated scam operation specifically targeting cryptocurrency users with personalised attacks and sophisticated technical infrastructure. 


<button onclick="history.back()">Go Back</button>
