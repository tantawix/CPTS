**Enumeration** is collecting as much information as possible
الenum هيا عملية تجميع المعلومات عن التارجت بتاعي
Most of the ways we can get access we can narrow down to the following two points:

- Functions and/or resources that allow us to interact with the target and/or provide additional information.
- Information that provides us with even more important information to access our target
- **Manual enumeration** is a **critical** component. Many scanning tools simplify and accelerate the process. However, these cannot always bypass the security measures of the services
----
## Introduction to Nmap

Network Mapper (`Nmap`) is an open-source network analysis and security auditing tool written in C, C++, Python, and Lua. It is designed to scan networks and identify which hosts are available on the network using raw packets, and services and applications, including the name and version, where possible. It can also identify the operating systems and versions of these hosts. Besides other features, Nmap also offers scanning capabilities that can determine if packet filters, firewalls, or intrusion detection systems (IDS) are configured as needed
- الnmap ممكن تساعدنا ف أننا نعمل:
-Host Discovery
-Port Scanning
-Service enum and detection
-OS Detection
-Scriptable interaction with the target service (Nmap Scripting Engine)
### Syntax
```
nmap <scan types> <options> <target>
```
### Scan Techniques
```shell-session
0xTantawyy@htb[/htb]$ nmap --help

<SNIP>
SCAN TECHNIQUES:
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
  -sU: UDP Scan
  -sN/sF/sX: TCP Null, FIN, and Xmas scans
  --scanflags <flags>: Customize TCP scan flags
  -sI <zombie host[:probeport]>: Idle scan
  -sY/sZ: SCTP INIT/COOKIE-ECHO scans
  -sO: IP protocol scan
  -b <FTP relay host>: FTP bounce scan
<SNIP>
```
- تكنيك الnmap الاساسي هو الSYN/ACK اللي هو `-sS` ده الافتراضي بتاعه الا لو انت حددتله نوع تاني
- فالenum بتاع الSYN/ACK الnmap بتبعت باكيت للتارجت بSYN Flag الرد التارجت:
-لو التارجت رد ب `SYN/ACK` معني كده ان البورت ده `open`
-لو التارجت رد ب`RST` معني كده ان البورت ده `closed`
-لو التارجت مردش خالص ساعتها هيبقي البورت حالته `filtered` ودي معناها ان الfirewall مانع الوصول للبورت ده

```shell-session
0xTantawyy@htb[/htb]$ sudo nmap -sS localhost

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-11 22:50 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000010s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5432/tcp open  postgresql
5901/tcp open  vnc-1

Nmap done: 1 IP address (1 host up) scanned in 0.18 seconds
```
اول column بيبقي رقم البورت تاني column بيبقي الstate بتاعته او حالته يعني وتالت column بتبقي الخدمه اللي شغاله علي البورت ده