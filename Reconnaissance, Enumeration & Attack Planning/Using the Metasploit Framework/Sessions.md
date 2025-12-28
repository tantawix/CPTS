- الmetasploit ممكن تهندل اكتر من modules  ف نفس الوقت من غير مايقفل ااتصال علي هدف عشان يشتغل علي التاني ودي ميزة بتدي للPentester حتة flexibility ف استخدام الtool وده بيتم عن طريق الsessions
- خلي بالك انت ممكن تعمل اكتر من session وتتنقل بينهم بكل بسهوله وتربط كذا module ببعض
- كل session ليها دنيتها وتحكم ليها واوامر ليها
---
## Using Sessions
- لو مثلا احنا شغالين علي exploit معين وعارفين انه هياخد وقت ممكن فالحالة دي نحط الsession بتاعته فالbackground ونعمل اي حاجه تانيه فالاداة عاوزين نعملها
- عشان نحط session معينه فالbackground واحنا جواها هندوس CTRL + Z او ممكن نكتب امر background لو انت شغال meterpreter
#### Listing Active Sessions

We can use the `sessions` command to view our currently active sessions.

```shell-session
msf6 exploit(windows/smb/psexec_psh) > sessions

Active sessions
===============

  Id  Name  Type                     Information                 Connection
  --  ----  ----                     -----------                 ----------
  1         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ MS01  10.10.10.129:443 -> 10.10.10.205:50501 (10.10.10.205)
```
#### Interacting with a Session

You can use the `sessions -i [no.]` command to open up a specific session.

```shell-session
msf6 exploit(windows/smb/psexec_psh) > sessions -i 1
[*] Starting interaction with 1...

meterpreter > 
```
- ممكن بعد كده نشغل بقي module تاني علي نفس الجهاز اللي انت اخترقتة عادي يعني مثلا paylaod بيسحب الcreds مثلا او بيعمل scan للشبكه
----
## Jobs
- لو مثلا انت دلوقتي بتنفذ exploit علي بورت معين وانت عاوز تشغل علي نفس البورت ده Module تاني لو جيت توقف الexploit الاولاني ب CTRL + C مش هتقف هيا هتبان قدامك انها وقفت بس هيا فعليا موقفتش
- لو دوسنا CTRL + Z البورت هيكون still in use وده مش هيخلينا نعرف نستخدم الModule التاني طب ايه الحل؟
- الحل هيكون ف الJobs وده كوماند في الMetasploit بيعرضلنا كل الtasks والjobs اللي شغاله وممكن نقدر نقفل اي task شغاله عادي
#### Viewing the Jobs Command Help Menu

We can view the help menu for this command, like others, by typing `jobs -h`

```shell-session
msf6 exploit(multi/handler) > jobs -h
Usage: jobs [options]

Active job manipulation and interaction.

OPTIONS:

    -K        Terminate all running jobs.
    -P        Persist all running jobs on restart.
    -S <opt>  Row search filter.
    -h        Help banner.
    -i <opt>  Lists detailed information about a running job.
    -k <opt>  Terminate jobs by job ID and/or range.
    -l        List all running jobs.
    -p <opt>  Add persistence to job by job ID
    -v        Print more detailed info.  Use with -i and -l
```

```shell-session
msf6 exploit(multi/handler) > exploit -h
Usage: exploit [options]

Launches an exploitation attempt.

OPTIONS:

    -J        Force running in the foreground, even if passive.
    -e <opt>  The payload encoder to use.  If none is specified, ENCODER is used.
    -f        Force the exploit to run regardless of the value of MinimumRank.
    -h        Help banner.
    -j        Run in the context of a job.
	
<SNIP
```

#### Running an Exploit as a Background Job

```shell-session

msf6 exploit(multi/handler) > exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.10.14.34:4444
```
- عشان اعمل رن لأي Expolit لازم اكتب `run` او `exploit` طب لو عايز اشغل الexploit فالbackground او كا job واكمل انا شغلي علي الاداة هكتب `exploit -j`
#### Listing Running Jobs

To list all running jobs, we can use the `jobs -l` command. To kill a specific job, look at the index no. of the job and use the `kill [index no.]` command. Use the `jobs -K` command to kill all running jobs.

```shell-session

msf6 exploit(multi/handler) > jobs -l

Jobs
====

 Id  Name                    Payload                    Payload opts
 --  ----                    -------                    ------------
 0   Exploit: multi/handler  generic/shell_reverse_tcp  tcp://10.10.14.34:4444
```