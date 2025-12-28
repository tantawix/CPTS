- عشان نبدأ نتفاعل مع الأداة ونشتغل عليها لازم نكتب كوماند `msfconsole` فالterminal
---
## Preparation
#### Launching MSFconsole

```shell-session
0xTroj6n@htb[/htb]$ msfconsole

       =[ metasploit v6.1.9-dev                           ]
+ -- --=[ 2169 exploits - 1149 auxiliary - 398 post       ]
+ -- --=[ 592 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 9 evasion                                       ]

Metasploit tip: Use sessions -1 to interact with the last opened session

msf6 > 
```
Alternatively, we can use the `-q` option, which does not display the banner.

  Introduction to MSFconsole

```shell-session
0xTroj6n@htb[/htb]$ msfconsole -q

msf6 > 
```
- طيب انا دلوقتي دخلت الاداة عاوز اعرف الاوامر؟ بروح كاتب `help`
- بس اول حاجه لازم نتأكد ان كل حاجه up to date ف هنكتب امر `msfupdate` بس دي طريقه قديمة ف هنحدث الاداه من بره من النظام نفسه من خلال امر `apt`

```shell-session
0xTroj6n@htb[/htb]$ sudo apt update && sudo apt install metasploit-framework

<SNIP>

(Reading database ... 414458 files and directories currently installed.)
Preparing to unpack .../metasploit-framework_6.0.2-0parrot1_amd64.deb ...
Unpacking metasploit-framework (6.0.2-0parrot1) over (5.0.88-0kali1) ...
Setting up metasploit-framework (6.0.2-0parrot1) ...
Processing triggers for man-db (2.9.1-1) ...
Scanning application launchers
Removing duplicate launchers from Debian
Launchers are updated
```
- اول حاجه بعد ما فتحنا الاداه هنسيرش علي expolit للtarget بتاعنا
- طبعا لازم نكون عامله enumeration عن التارجيت عشان نعرف هنستغل ايه بالظبط فيه
- اثناء الenumeration لازم ناخد بصة علي الpublic-facing services اللي شغاله هل في HTTP server او FTP او SQL db طبيعي ممكن تلاقي حاجات زي دي مفتوحه ودي اللي بتخليك تفكر ف عملية استغلال البورتات دي
- اول حاجه بنبص عليها وهي اصدار الخدمه اللي شغاله وبنشوف لو ليها استغلال او لأ
---
## MSF Engagement Structure

The MSF engagement structure can be divided into five main categories.
- Enumeration
- Preparation
- Exploitation
- Privilege Escalation
- Post-Exploitation