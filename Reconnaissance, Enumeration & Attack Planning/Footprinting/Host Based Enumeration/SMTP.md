- The `Simple Mail Transfer Protocol` (`SMTP`) is protocol used for seding emails in an IP Netowrk
- it's a client-server protocol and often combined with IMAP or POP3
- SMTP accept connection request on port `25` but newer SMTP protocol also use TCP `587`
- this port used to recieve mail from auth users and using STARTTLS to encrypt data
- SMTP server is preventing spam using authentication mechanisms that allow only authorized users to send e-mails and for this purpose SMTP support extension ESMTP with SMTP-Auth
- SMTP Client known as `Mail User Agent (MUA)`
- `Mail Transfer Agent` (`MTA`) converts it into a header and a body and uploads both to the SMTP server

# ملخص نظام الإيميل وSMTP (مختصر عملي)

## المكوّنات الأساسية
| المكون      |            الاسم بالعربي | شرح مختصر                                                                                     | بورت / ملاحظات                                         |
| ----------- | -----------------------: | --------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| **MUA**     |          Mail User Agent | برنامج العميل عند المستخدم (Outlook, Thunderbird, واجهة الويب). هو اللي بيكتب وبيرسل الإيميل. | عمومًا client-side                                     |
| **MSA**     |    Mail Submission Agent | نقطة الاستلام من الـ MUA؛ بتتحقق من مصداقية المرسل (AUTH) قبل ما تبعت للـ MTA.                | عادة على بورت **587 (Submission)**                     |
| **MTA**     |      Mail Transfer Agent | السيرفر اللي بينقل الإيميل بين خوادم (مثال: Postfix, Exim, Sendmail). ممكن يكون relay.        | عادة **SMTP على 25**؛ لو مفتوح لأي حد → **Open Relay** |
| **MDA**     |      Mail Delivery Agent | يوصل الرسالة لصندوق المستلم (يحطها في Maildir أو DB أو يسلّمها لبروتوكولات POP/IMAP).         | داخلي للسيرفر                                          |
| **Mailbox** | صندوق البريد (POP3/IMAP) | المكان اللي المستخدم يقرأ منه الإيميل.                                                        | **POP3 (110/995)**، **IMAP (143/993)**                 |

`MUA → MSA → (MTA relay) → MDA → Mailbox (POP3/IMAP)`

---
##### مشاكل تصميمية في SMTP (ببساطة)
- مافيش مصادقة افتراضية لما تفتح اتصال على الـ SMTP (port 25) — سهل يعملوا **mail spoofing**.  
- مفيش معيار مضبوط للـ delivery receipt — الردود غالبًا error messages بالإنجليزي.  
- لو السيرفر معمول عليه **Open Relay** أي حد يقدر يبعت منه spam بكثرة.

---
##### أساليب الحماية المهمة

- الـ**ESMTP** — امتداد للـ SMTP بيدعم أوامر جديدة (EHLO) وميزات زي AUTH وSTARTTLS.  
- الـ**STARTTLS** — يرقي الاتصال إلى TLS بعد أمر `STARTTLS`، بيشفّر الجلسة قبل المصادقة.  
- الـ**SMTP-AUTH** — مصادقة المستخدمين قبل الإرسال (AUTH PLAIN / LOGIN / ...)، عادة على 587 أو بعد STARTTLS.  
- الـ**SPF** — سجل DNS (TXT) يحدد أي IPs مسموح لهم يبعتوا باسم الدومين.  
- الـ**DKIM** — توقيع رقمي على الرسائل؛ المستقبل يتحقق من التوقيع عبر مفتاح مخزن في DNS.  
- **الـDMARC** — سياسة بتجمع SPF/DKIM وتحدد إيه يعمل المستقبل لو الرسالة فشلت (none/quarantine/reject) وتطلب تقارير.  
- الـ**Spam filters / RBLs** — فلترة محتوى وسمعة الإرسال.
---
## Default Configuration
#### Default Configuration

  SMTP

```shell-session
0xTroj6n@htb[/htb]$ cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"

smtpd_banner = ESMTP Server 
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 2
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
myhostname = mail1.inlanefreight.htb
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtp_generic_maps = hash:/etc/postfix/generic
mydestination = $myhostname, localhost 
masquerade_domains = $myhostname
mynetworks = 127.0.0.0/8 10.129.0.0/16
mailbox_size_limit = 0
recipient_delimiter = +
smtp_bind_address = 0.0.0.0
inet_protocols = ipv4
smtpd_helo_restrictions = reject_invalid_hostname
home_mailbox = /home/postfix
```

The sending and communication are also done by special commands that cause the SMTP server to do what the user requires.

| **Command**  | **Description**                                                                                           |
| ------------ | --------------------------------------------------------------------------------------------------------- |
| `AUTH PLAIN` | AUTH is a service extension used to authenticate the client.                                              |
| `HELO`       | The client logs in with its computer name and thus starts the session.                                    |
| `MAIL FROM`  | The client names the email sender.                                                                        |
| `RCPT TO`    | The client names the email recipient.                                                                     |
| `DATA`       | The client initiates the transmission of the email.                                                       |
| `RSET`       | The client aborts the initiated transmission but keeps the connection between client and server.          |
| `VRFY`       | The client checks if a mailbox is available for message transfer, enumerate existing users on the system. |
| `EXPN`       | The client also checks if a mailbox is available for messaging with this command.                         |
| `NOOP`       | The client requests a response from the server to prevent disconnection due to time-out.                  |
| `QUIT`       | The client terminates the session.                                                                        |
#### Telnet - HELO/EHLO

```shell-session
0xTroj6n@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 


HELO mail1.inlanefreight.htb

250 mail1.inlanefreight.htb


EHLO mail1

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING
```
#### Telnet - VRFY

```shell-session
0xTroj6n@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

VRFY root

252 2.0.0 root


VRFY cry0l1t3

252 2.0.0 cry0l1t3


VRFY testuser

252 2.0.0 testuser


VRFY aaaaaaaaaaaaaaaaaaaaaaaaaaaa

252 2.0.0 aaaaaaaaaaaaaaaaaaaaaaaaaaaa
```
#### Send an Email

```shell-session
0xTroj6n@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server


EHLO inlanefreight.htb

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING


MAIL FROM: <cry0l1t3@inlanefreight.htb>

250 2.1.0 Ok


RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure

250 2.1.5 Ok


DATA

354 End data with <CR><LF>.<CR><LF>

From: <cry0l1t3@inlanefreight.htb>
To: <mrb3n@inlanefreight.htb>
Subject: DB
Date: Tue, 28 Sept 2021 16:32:51 +0200
Hey man, I am trying to access our XY-DB but the creds don't work. 
Did you make any changes there?
.

250 2.0.0 Ok: queued as 6E1CF1681AB


QUIT

221 2.0.0 Bye
Connection closed by foreign host.
```
---
## Footprinting the Service
#### Nmap

```shell-session
0xTroj6n@htb[/htb]$ sudo nmap 10.129.14.128 -sC -sV -p25

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-27 17:56 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00025s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: mail1.inlanefreight.htb, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.09 seconds
```
#### Nmap - Open Relay

```shell-session
0xTroj6n@htb[/htb]$ sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-30 02:29 CEST
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Initiating ARP Ping Scan at 02:29
Scanning 10.129.14.128 [1 port]
Completed ARP Ping Scan at 02:29, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 02:29
Completed Parallel DNS resolution of 1 host. at 02:29, 0.03s elapsed
Initiating SYN Stealth Scan at 02:29
Scanning 10.129.14.128 [1 port]
Discovered open port 25/tcp on 10.129.14.128
Completed SYN Stealth Scan at 02:29, 0.06s elapsed (1 total ports)
NSE: Script scanning 10.129.14.128.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.07s elapsed
Nmap scan report for 10.129.14.128
Host is up (0.00020s latency).

PORT   STATE SERVICE
25/tcp open  smtp
| smtp-open-relay: Server is an open relay (16/16 tests)
|  MAIL FROM:<> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@nmap.scanme.org> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@ESMTP> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest%nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org"@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@[10.129.14.128]:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@ESMTP:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@[10.129.14.128]>
|_ MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@ESMTP>
MAC Address: 00:00:00:00:00:00 (VMware)

NSE: Script Post-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
           Raw packets sent: 2 (72B) | Rcvd: 2 (72B)
```