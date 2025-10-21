## What is a Zone Transfer

- تخيل ان سيرفر الDNS الرئيسي عامل backup من كل السجلات عشان يبعتها لسيرفر DNS احتياطي تاني فا العملية بتاعت النقل هنا بنسميها DNS Zone Transfer
###### Zone Transfer Components
1. **Zone Transfer Request (AXFR):**
- ده Secondary DNS Server بيبدأ يبعت طلب request للprimary server عشان ياخد منه السجلات الrequest ده بيستخدم الAXFR
2. **SOA Record Transfer:**
- لما الprimary server يستلم الطلب بيرد علي الsecond server ب SOA Record
- الSOA ده بيكون فيه vital info عن الzone 
3. **DNS Records Transmission:**
- هنا بقي عملية النقل بتتنفذ بشكل فعلي وطبعا السجلات دي بتكون معظم السجلات اللي اتعلمناها زي A, AAA , CNAME, NS, MX
4. **Zone Transfer Complete:**
- لما الداتا بتتنقل بقي خلاص الprimary server بينهي الzone transfer وهنا بيدي اشاره للسيرفر التاني انه خلاص انا نقلتلك كل حاجه يا باشا
5. **Acknowledgement (ACK):**
- السيرفر التاني بيدي ack علي الرساله اللي الprimary server قالو عليها انه انا خلصت نقل يعني فا أكني الsecondary server بيقولو خلصانه
---
## The Zone Transfer Vulnerability

- `Subdomains`: A complete list of subdomains, many of which might not be linked from the main website or easily discoverable through other means. These hidden subdomains could host development servers, staging environments, administrative panels, or other sensitive resources.
- `IP Addresses`: The IP addresses associated with each subdomain, providing potential targets for further reconnaissance or attacks.
- `Name Server Records`: Details about the authoritative name servers for the domain, revealing the hosting provider and potential misconfigurations.
---
#### Exploiting Zone Transfers

```shell-session
0xTroj6n@htb[/htb]$ dig axfr @nsztm1.digi.ninja zonetransfer.me
```

```shell-session
0xTroj6n@htb[/htb]$ dig axfr @nsztm1.digi.ninja zonetransfer.me

; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> axfr @nsztm1.digi.ninja zonetransfer.me
; (1 server found)
;; global options: +cmd
zonetransfer.me.	7200	IN	SOA	nsztm1.digi.ninja. robin.digi.ninja. 2019100801 172800 900 1209600 3600
zonetransfer.me.	300	IN	HINFO	"Casio fx-700G" "Windows XP"
zonetransfer.me.	301	IN	TXT	"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"
zonetransfer.me.	7200	IN	MX	0 ASPMX.L.GOOGLE.COM.
...
zonetransfer.me.	7200	IN	A	5.196.105.14
zonetransfer.me.	7200	IN	NS	nsztm1.digi.ninja.
zonetransfer.me.	7200	IN	NS	nsztm2.digi.ninja.
_acme-challenge.zonetransfer.me. 301 IN	TXT	"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"
_sip._tcp.zonetransfer.me. 14000 IN	SRV	0 0 5060 www.zonetransfer.me.
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me. 7200	IN PTR www.zonetransfer.me.
asfdbauthdns.zonetransfer.me. 7900 IN	AFSDB	1 asfdbbox.zonetransfer.me.
asfdbbox.zonetransfer.me. 7200	IN	A	127.0.0.1
asfdbvolume.zonetransfer.me. 7800 IN	AFSDB	1 asfdbbox.zonetransfer.me.
canberra-office.zonetransfer.me. 7200 IN A	202.14.81.230
...
;; Query time: 10 msec
;; SERVER: 81.4.108.41#53(nsztm1.digi.ninja) (TCP)
;; WHEN: Mon May 27 18:31:35 BST 2024
;; XFR size: 50 records (messages 1, bytes 2085)
```