## SSH
- Secure Shell (SSH) enables two computers to establish an encrypted and direct connection within a possibly insecure network on the standard port `TCP 22`
- الssh هو بروتوكول بيسمحلي اني اخد shell علي جهاز تاني من خلال user و pass واتحكم فيه من عندي وخلي بالك الاتصال بيكون مشفر بالكامل
- ممكن اconfigure الssh انه يقبل اتصال من خلال جهاز معين بس
- الSSH بيشتغل علي windows و linux
##### SSH Authentication Methods
1. Password authentication
2. Public-key authentication
3. Host-based authentication
4. Keyboard authentication
5. Challenge-response authentication
6. GSSAPI authentication
https://www.golinuxcloud.com/openssh-authentication-methods-sshd-config/
بص بصه هنا
#### Public Key Authentication
- لما باجي اكونكت ssh علي سيرفر معين السيرفر بيبعتلي ال`public host key` وانا بستخدم الkey ده عشان اتأكد من هوية السيرفر
- خلي بالك ال`host key` مينفعش يتقلد لأنه بيكون مرتبط بprivate key pair
- **المفتاح الخاص (Private Key)** = المفتاح اللي معاك في جيبك. **خاص** وممنوع حد يشوفه.
- **المفتاح العام (Public Key)** = نسخة من المفتاح تقدر تديها لأي حد أو تزرعها على أي سيرفر. زي نسخة من المفتاح تتركها عند باب البيت علشان الباب يفتحلك
لما تحاول تدخل على سيرفر بـ SSH:
1. السيرفر بيبعتلك تحدّي (challenge).
    
2. جهازك يثبت إنه معاَه المفتاح الخاص (بيوقع التحدّي رقميًا) — السيرفر يقدر يتأكد من التوقيع باستخدام المفتاح **العام** اللي عنده.
    
3. لو التوقيع صح، السيرفر بيسمحلك تدخل من غير ماتكتب كلمة سر على السيرفر.
---
## Default Configuration

```shell-session
0xTroj6n@htb[/htb]$ cat /etc/ssh/sshd_config  | grep -v "#" | sed -r '/^\s*$/d'

Include /etc/ssh/sshd_config.d/*.conf
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem       sftp    /usr/lib/openssh/sftp-server
```
---
## Footprinting the Service
#### SSH-Audit

```shell-session
0xTroj6n@htb[/htb]$ git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
0xTroj6n@htb[/htb]$ ./ssh-audit.py 10.129.14.132

# general
(gen) banner: SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
(gen) software: OpenSSH 8.2p1
(gen) compatibility: OpenSSH 7.4+, Dropbear SSH 2018.76+
(gen) compression: enabled (zlib@openssh.com)                                   

# key exchange algorithms
(kex) curve25519-sha256                     -- [info] available since OpenSSH 7.4, Dropbear SSH 2018.76                            
(kex) curve25519-sha256@libssh.org          -- [info] available since OpenSSH 6.5, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp256                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp384                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp521                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) diffie-hellman-group-exchange-sha256 (2048-bit) -- [info] available since OpenSSH 4.4
(kex) diffie-hellman-group16-sha512         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73
(kex) diffie-hellman-group18-sha512         -- [info] available since OpenSSH 7.3
(kex) diffie-hellman-group14-sha256         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73

# host-key algorithms
(key) rsa-sha2-512 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) rsa-sha2-256 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) ssh-rsa (3072-bit)                    -- [fail] using weak hashing algorithm
                                            `- [info] available since OpenSSH 2.5.0, Dropbear SSH 0.28
                                            `- [info] a future deprecation notice has been issued in OpenSSH 8.2: https://www.openssh.com/txt/release-8.2
(key) ecdsa-sha2-nistp256                   -- [fail] using weak elliptic curves
                                            `- [warn] using weak random number generator could reveal the key
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(key) ssh-ed25519                           -- [info] available since OpenSSH 6.5
...SNIP...
```
#### Change Authentication Method

```shell-session
0xTroj6n@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config 
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
```

```shell-session
0xTroj6n@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
debug1: Next authentication method: password

cry0l1t3@10.129.14.132's password:
```
---
## Rsync
- الrsync دي تول سريعه وقويه جدا ف عملية نقل الملفات بشكل remotely
- It can be used to copy files locally on a given machine and to/from remote hosts
- بتشتغل علي بورت `873`
#### Scanning for Rsync

```shell-session
0xTroj6n@htb[/htb]$ sudo nmap -sV -p 873 127.0.0.1

Starting Nmap 7.92 ( https://nmap.org ) at 2022-09-19 09:31 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0058s latency).

PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.13 seconds
```
#### Probing for Accessible Shares

```shell-session
0xTroj6n@htb[/htb]$ nc -nv 127.0.0.1 873

(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
dev            	Dev Tools
@RSYNCD: EXIT
```
#### Enumerating an Open Share

```shell-session
0xTroj6n@htb[/htb]$ rsync -av --list-only rsync://127.0.0.1/dev

receiving incremental file list
drwxr-xr-x             48 2022/09/19 09:43:10 .
-rw-r--r--              0 2022/09/19 09:34:50 build.sh
-rw-r--r--              0 2022/09/19 09:36:02 secrets.yaml
drwx------             54 2022/09/19 09:43:10 .ssh

sent 25 bytes  received 221 bytes  492.00 bytes/sec
total size is 0  speedup is 0.00
```
---
## R-Services
- دي عباره عن خدمه بتستخدم عشان تتحكم في جهاز تاني عن بعد زي الSSH كده بس دي بتكون بين ال Unix Os بس
- الأوامر في الR-Services بتتبعت بين الclient والserver بطريقة غير مشفره تقدر تقول عليها زي الtelnet وده اللي بيخلي الهاكرز ممكن يستغلوها ف هجمات زي الMITM
- الR-Services بتشتغلي علي بورتات `512,513,514` وعشان تقدر تستخدم الخدمه دي لازم يكون عندك برنامج اسمه `r-commands`
- اي command ف r-services بيبدأ بr
- rcp (`remote copy`)
- rexec (`remote execution`)
- rlogin (`remote login`)
- rsh (`remote shell`)
- rstat
- ruptime
- rwho (`remote who`)

**The table below will provide a quick overview of the most frequently abused commands**

|**Command**|**Service Daemon**|**Port**|**Transport Protocol**|**Description**|
|---|---|---|---|---|
|`rcp`|`rshd`|514|TCP|Copy a file or directory bidirectionally from the local system to the remote system (or vice versa) or from one remote system to another. It works like the `cp` command on Linux but provides `no warning to the user for overwriting existing files on a system`.|
|`rsh`|`rshd`|514|TCP|Opens a shell on a remote machine without a login procedure. Relies upon the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files for validation.|
|`rexec`|`rexecd`|512|TCP|Enables a user to run shell commands on a remote machine. Requires authentication through the use of a `username` and `password` through an unencrypted network socket. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files.|
|`rlogin`|`rlogind`|513|TCP|Enables a user to log in to a remote host over the network. It works similarly to `telnet` but can only connect to Unix-like hosts. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files.|
- The /etc/hosts.equiv file contains a list of trusted hosts and is used to grant access to other systems on the network
- الاجهزه اللي بتكون فالملف ده بتاخد أكسس بشكل تلقائي بمجرد ماتتصل بالhost من غير حتي authentication
#### /etc/hosts.equiv

```shell-session
0xTroj6n@htb[/htb]$ cat /etc/hosts.equiv

# <hostname> <local username>
pwnbox cry0l1t3
```
#### Scanning for R-Services

```shell-session
0xTroj6n@htb[/htb]$ sudo nmap -sV -p 512,513,514 10.0.17.2

Starting Nmap 7.80 ( https://nmap.org ) at 2022-12-02 15:02 EST
Nmap scan report for 10.0.17.2
Host is up (0.11s latency).

PORT    STATE SERVICE    VERSION
512/tcp open  exec?
513/tcp open  login?
514/tcp open  tcpwrapped

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 145.54 seconds
```
---
## Access Control Issue
- الr-services كانت بتفترض إن لو جهاز معين trusted فكل اللي جي منه امن هو كمان
- يعني السيرفر اللي شغال عليه R-Services عنده لسته كده فيها أجهزة موثوق فيها بتقدر تأكسس علي الخدمه من غير auth زي ما قولنا المشكله هنا لو في هاكر سيطر علي جهاز معين من دول!!
#### Sample .rhosts File

```shell-session
0xTroj6n@htb[/htb]$ cat .rhosts

htb-student     10.0.17.5
+               10.0.17.10
+               +
```
#### Logging in Using Rlogin

```shell-session
0xTroj6n@htb[/htb]$ rlogin 10.0.17.2 -l htb-student

Last login: Fri Dec  2 16:11:21 from localhost

[htb-student@localhost ~]$
```
#### Listing Authenticated Users Using Rwho

```shell-session
0xTroj6n@htb[/htb]$ rwho

root     web01:pts/0 Dec  2 21:34
htb-student     workstn01:tty1  Dec  2 19:57  2:25       
```
#### Listing Authenticated Users Using Rusers

```shell-session
0xTroj6n@htb[/htb]$ rusers -al 10.0.17.5

htb-student     10.0.17.5:console          Dec 2 19:57     2:25
```