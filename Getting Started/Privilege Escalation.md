once we compromised the machine usually in the context of a low-privileged user, ف احنا هنعوز نعلي الصلاحيات بتاعتنا عشان نعمل حاجات اكبر علي السيستم ونكون ادمن فا نعرف نوصل لحاجات حساسه
فا احنا هنبدأ ندور علي ثغره او اي حاجه تخلينا اننا نعلي صلاحيتنا ونبقي admin علي الماشين او root

---
## PrivEsc Checklists
One excellent resource is [HackTricks](https://book.hacktricks.xyz/), which has an excellent checklist for both [Linux](https://book.hacktricks.wiki/en/linux-hardening/linux-privilege-escalation-checklist.html) and [Windows](https://book.hacktricks.wiki/en/windows-hardening/checklist-windows-privilege-escalation.html) local privilege escalation. Another excellent repository is [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings), which also has checklists for both [Linux](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md) and [Windows](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md). We must start experimenting with various commands and techniques and get familiar with them to understand multiple weaknesses that can lead to escalating our privileges.

---
## Enumeration Scripts
We can run many scripts to automatically enumerate the server by running common commands that return any interesting findings. Some of the common Linux enumeration scripts include [LinEnum](https://github.com/rebootuser/LinEnum.git) and [linuxprivchecker](https://github.com/sleventyeleven/linuxprivchecker), and for Windows include [Seatbelt](https://github.com/GhostPack/Seatbelt) and [JAWS](https://github.com/411Hall/JAWS), Another useful tool we may use for server enumeration is the [Privilege Escalation Awesome Scripts SUITE (PEASS)](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite), as it is well maintained to remain up to date and includes scripts for enumerating both Linux and Windows
run PEASS script called `linpeas.sh`
```shell-session
htb[/htb]$ ./linpeas.sh
...SNIP...

Linux Privesc Checklist: https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist
 LEYEND:
  RED/YELLOW: 99% a PE vector
  RED: You must take a look at it
  LightCyan: Users with console
  Blue: Users without console & mounted devs
  Green: Common things (users, groups, SUID/SGID, mounts, .sh scripts, cronjobs)
  LightMangenta: Your username


====================================( Basic information )=====================================
OS: Linux version 3.9.0-73-generic
User & Groups: uid=33(www-data) gid=33(www-data) groups=33(www-data)
...SNIP...
```

----
## Kernel Exploits
- in kernel exploits we potential kernel vulnerabilities that may exist
- for example the above script shown us the linux version `shell-session Linux version 3.9.0-73-generic
if we search and use the public expolits techniques and search in google or searchsploit for a expolit we would find `CVE-2016-5195` otherwise known as `DirtyCow`. We can search for and download the DirtyCow exploit and run it on the server to gain root access
- The same concept also applies to Windows
---
## Vulnerable Software
- طريقه تانيه وهي اننا نشوف ايه الsoftwares او البرامج اللي متسطبه علي الجهاز ممكن يكون في برنامج معين مصاب بثغره معينه ممكن خلالها نرفع صلاحيتنا وعشان نشوف الsoftwares اللي عندنا ده بيتم عن طريق `dpkg -l` لو بتستعمل لينكس وخش علي فولدر `C:\Program Files` لو الجهاز ده ويندوز
---
## User Privileges
- من اهم الطرق اللي لازم نشيك عليها وهي اننا نشيك ونشوف كل يوزر مسموح ليه ينفذ اوامر ايه عن طريق الsudo من غير auth
- بعد ما توصل لجهاز (Linux أو Windows)، لازم تشوف الـ **Privileges** اللي عندك، يعني إيه الصلاحيات اللي الأكونت اللي دخلت بيه مسموح له يعملها. لو معاك صلاحيات أعلى من العادي، ممكن ترفع نفسك لـ root أو system user
على **Linux**: `sudo` و `SUID`

- **sudo**: بيخليك تشغّل أوامر كمستخدم تاني (غالبًا root).

    - لو كتبت:

        `sudo -l`

        هتشوف إيه الأوامر اللي مسموحلك تشغّلها بـ sudo.

    - لو لقيت:

        `(ALL : ALL) ALL`

        يبقى تقدر تشغّل أي أمر كـ root، يعني عندك Full Access.
- ساعات تلاقي أوامر معينة بس اللي ينفع تشغّلها بـ sudo، وفيه حاجات ممكن تبقى من غير Password (`NOPASSWD`).
    - مثال:
        `(user : user) NOPASSWD: /bin/echo`

معناها تقدر تشغّل `/bin/echo` كـ user ده من غير ما تكتب الباسورد.
        
- **SUID**: ملفات ليها صلاحية خاصة (Set User ID)، لو استغليتها صح ممكن تبقى root.

- Once we find a particular application we can run with `sudo`, we can look for ways to exploit it to get a shell as the root user. [GTFOBins](https://gtfobins.github.io/) contains a list of commands and how they can be exploited through `sudo`. We can search for the application we have `sudo` privilege over, and if it exists, it may tell us the exact command we should execute to gain root access using the `sudo` privilege we have.
- [LOLBAS](https://lolbas-project.github.io/#) also contains a list of Windows applications which we may be able to leverage to perform certain functions, like downloading files or executing commands in the context of a privileged user
---
## Scheduled Tasks
سواء في **Linux** أو **Windows**، فيه ميكانيزم بيشغّل سكريبتات أو أوامر في أوقات محددة أو بشكل دوري علشان ينفّذ مهام (زي antivirus scan أو backup).
- الأنظمة دي اسمها:
-في Linux → **cron jobs**
-في Windows → **Scheduled Tasks**
- اللي ممكن تستغله كهاكر هو إنك تلاقي “job” أو “task” شغّالة بــ **صلاحيات عالية** (زي root أو SYSTEM)، وتقدر تعدّلها أو تغيّر السكريبت اللي بتشغّله
**ازاي استغلها**
- بيكون في طريقتين عشان استغل النقطه دي وهما:
1. Add new scheduled tasks/cron jobs
	-لو ليك إذن تضيف تاسك ممكن انك تضيف تاسك بتنفذ اسكريبت معين بصلاحيات كبيره فا تقدر تعمل privesc
2. Trick them to execute a malicious software
-بشوف انهي job شغاله بصلاحيات الroot وابدأ العب فالفايل اللي مسؤول عن تشغليها واخليه بدل ماينفذ سكريبت عادي ينفذ سكريبت خبيث وبكده اكون قدر اعمل privesc

The easiest way is to check if we are allowed to add new scheduled tasks. In Linux, a common form of maintaining scheduled tasks is through `Cron Jobs`. There are specific directories that we may be able to utilize to add new cron jobs if we have the `write` permissions over them. These include:

3. `/etc/crontab`
4. `/etc/cron.d`
5. `/var/spool/cron/crontabs/root`
If we can write to a directory called by a cron job, we can write a bash script with a reverse shell command, which should send us a reverse shell when executed.

----
## Exposed Credentials
- ممكن يكون في exposed creds في ملفات الconfiguration او الlog files زي ال`bash_history` in Linux and `PSReadLine` in Windows
```shell-session
...SNIP...
[+] Searching passwords in config PHP files
[+] Finding passwords inside logs (limit 70)
...SNIP...
/var/www/html/config.php: $conn = new mysqli(localhost, 'db_user', 'password123');
```
حاجه زي كده مثلا في creds متسربه اهي وباين عليها انها بتاعت database

---
## SSH Keys
- اخر حاجه الssh keys وهيا دي عباره عن مفتاح بيسمحلي اني اكونكت ssh علي السيرفر من غير auth
- لو قدرت اوصل للفايل ده `/home/user/.ssh/id_rsa` or `/root/.ssh/id_rsa` هتحصل علي الkey وتقدر تكونكت ssh (لو السيرفر مفتوح عليه ssh)
```shell-session
htb[/htb]$ vim id_rsa
htb[/htb]$ chmod 600 id_rsa
htb[/htb]$ ssh root@10.10.10.10 -i id_rsa

root@10.10.10.10#
```
- طب لو لقيت عندك **صلاحية الكتابة** على مجلد ‎`.ssh`‎ بتاع يوزر (زي ‎`/home/user/.ssh/`‎)، ممكن تضيف مفتاحك العام في فايل ‎`authorized_keys`‎ بتاعه.
الخطوات:

1. على جهازك، أنشئ زوج مفاتيح SSH جديد:
    
    `ssh-keygen -f key`
ده هيولّد:
    
    - **key** → المفتاح الخاص (تستخدمه مع ‎`ssh -i`‎).
        
    - **key.pub** → المفتاح العام.
        
2. انسخ محتوى ‎`key.pub`‎ لجهاز الضحية، وحطه في:
    
    `echo "ssh-rsa AAAA... user@parrot" >> /home/user/.ssh/authorized_keys`
1. بعد كده تقدر تعمل SSH مباشرة باستخدام المفتاح الخاص:
    
    `ssh user@TARGET -i key`
-----
