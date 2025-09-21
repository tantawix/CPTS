- الhosr discovery مهم جدا عشان اعرف الشبكه اللي بتارجيتها فيها كام جهاز شغال
- وعشان برضو مقعدش اتيست علي ip معين وميكونش فالاخر شغال
```
```shell-session
0xTantawyy@htb[/htb]$ sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5

10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```

| **Scanning Options** | **Description**                                                  |
| -------------------- | ---------------------------------------------------------------- |
| `10.129.2.0/24`      | Target network range.                                            |
| `-sn`                | Disables port scanning.                                          |
| `-oA tnet`           | Stores the results in all formats starting with the name 'tnet'. |

---
## Scan IP List
```shell-session
0xTantawyy@htb[/htb]$ sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

10.129.2.18
10.129.2.19
10.129.2.20
```

| **Scanning Options** | **Description**                                                      |
| -------------------- | -------------------------------------------------------------------- |
| `-sn`                | Disables port scanning.                                              |
| `-oA tnet`           | Stores the results in all formats starting with the name 'tnet'.     |
| `-iL`                | Performs defined scans against targets in provided 'hosts.lst' list. |

---
## Scan Multiple IPs

```shell-session
0xTantawyy@htb[/htb]$ sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20| grep for | cut -d" " -f5

10.129.2.18
10.129.2.19
10.129.2.20
```
```shell-session
0xTantawyy@htb[/htb]$ sudo nmap -sn -oA tnet 10.129.2.18-20| grep for | cut -d" " -f5

10.129.2.18
10.129.2.19
10.129.2.20
```
---
## Scan Single IP

```shell-session
0xTantawyy@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-14 23:59 CEST
Nmap scan report for 10.129.2.18
Host is up (0.087s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
```

|**Scanning Options**|**Description**|
|---|---|
|`10.129.2.18`|Performs defined scans against the target.|
|`-sn`|Disables port scanning.|
|`-oA host`|Stores the results in all formats starting with the name 'host'.|
- طبيعي Nmap لما يعمل Ping Scan بيجرّب يبعث **ICMP Echo Requests** (اللي هو أمر `ping` العادي).
- بس فيه حاجة أهم: لو الـ target في نفس الشبكة بتاعتك (نفس الـ subnet)، قبل ما يبعث ICMP بيبعث **ARP Ping**.
ليه؟
- الARP أسرع وأكتر دقة في الشبكات المحلية (LAN)، لأن ARP بيشتغل على الطبقة الثانية (Layer 2) وبيسأل: "مين صاحب الـ IP ده؟"
- فأول ما الجهاز يرد على الـ ARP، Nmap يعرف إن الجهاز شغّال ومش محتاج يبعت ICMP أصلاً.
- - لازم تضيف الـ option `-PE`:
    `sudo nmap -sn -PE 192.168.1.0/24`
- هنا بتقوله: “يا Nmap، حتى لو أخدت ARP رد، برضه ابعت ICMP Echo Requests

```shell-session
0xTantawyy@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:08 CEST
SENT (0.0074s) ARP who-has 10.129.2.18 tell 10.10.14.2
RCVD (0.0309s) ARP reply 10.129.2.18 is-at DE:AD:00:00:BE:EF
Nmap scan report for 10.129.2.18
Host is up (0.023s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
```

| **Scanning Options** | **Description**                                                          |
| -------------------- | ------------------------------------------------------------------------ |
| `10.129.2.18`        | Performs defined scans against the target.                               |
| `-sn`                | Disables port scanning.                                                  |
| `-oA host`           | Stores the results in all formats starting with the name 'host'.         |
| `-PE`                | Performs the ping scan by using 'ICMP Echo requests' against the target. |
| `--packet-trace`     | Shows all packets sent and received                                      |
Another way to determine why Nmap has our target marked as "alive" is with the "`--reason`" option.

```shell-session
0xTantawyy@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host -PE --reason 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:10 CEST
SENT (0.0074s) ARP who-has 10.129.2.18 tell 10.10.14.2
RCVD (0.0309s) ARP reply 10.129.2.18 is-at DE:AD:00:00:BE:EF
Nmap scan report for 10.129.2.18
Host is up, received arp-response (0.028s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds
```

|**Scanning Options**|**Description**|
|---|---|
|`10.129.2.18`|Performs defined scans against the target.|
|`-sn`|Disables port scanning.|
|`-oA host`|Stores the results in all formats starting with the name 'host'.|
|`-PE`|Performs the ping scan by using 'ICMP Echo requests' against the target.|
|`--reason`|Displays the reason for specific result.|
to disable ARP pings by `--disable-arp-ping`
```shell-session
0xTantawyy@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:12 CEST
SENT (0.0107s) ICMP [10.10.14.2 > 10.129.2.18 Echo request (type=8/code=0) id=13607 seq=0] IP [ttl=255 id=23541 iplen=28 ]
RCVD (0.0152s) ICMP [10.129.2.18 > 10.10.14.2 Echo reply (type=0/code=0) id=13607 seq=0] IP [ttl=128 id=40622 iplen=28 ]
Nmap scan report for 10.129.2.18
Host is up (0.086s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
```