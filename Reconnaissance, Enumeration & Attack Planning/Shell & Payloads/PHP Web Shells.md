-  الPHP دي open source scripting language بتستخدم فالويب وحاجات تانيه كتير بس المعظم يعني بتبقي فالوقت وهيا عباره عن server-side programming language
- لما بيكون في صفحة ويب معموله بالphp وبدخل بيانات مثلا زي user و pass البيانات دي بتروح للسيرفر تتعالج واللي بتعالجها هيا لغة الphp
---
## Hands-on With a PHP-Based Web Shell
- الrConfig معرض للاستغلال خصوصا الاصدار 3.9.6 عشان فيه مشكله ف حتة رفع الملفات
###### الخطوات اللي بتعملها — وإزاي الاستغلال بيحصل

1. تدخل على لوحة rConfig باستخدام (admin:admin) — لو النظام شغال بالإعدادات الافتراضية.
    
2. تروّح لـ **Devices → Vendors → Add Vendor** (عشان تضيف Vendor جديد).
    
3. في صفحة “Add Vendor” تلاقي خيار لرفع Logo أو صورة ـ بدل ما ترفع صورة حقيقية، ترفع **web‑shell PHP** (ملف PHP تحطه انت).
    
4. السيرفر — بسبب الثغرة — بيقبل الملف من غير ما يرفضه (لأن التحقق ضعيف). بالتالي الملف يترفع ويتخزن في مكان تُنفذه Webserver (Apache/PHP).
    
5. بعد كده تروح في البراوزر وتفتح الملف اللي رفعته — في اللحظة دي PHP هتُفسّر الكود وتشغّله — وبالتالي تقدر تنفّذ أوامر على السيرفر (shell, reverse shell, إلخ).
    

> بمعنى آخر: أنت بتحول endpoint “Upload Logo → صورة” إلى “Upload PHP shell → تنفيذ باك‑اند”.

6. لو عايز reverse shell: بدل web‑shell “مشغّلة › أمر واحد” ممكن ترفع كود PHP يولّد اتصال عكسي (reverse‑shell) من السيرفر لجهازك (attack‑box). لما تفتحه من البراوزر، الكود يتنفّذ ويربطك shell على الجهاز بتاعك
![](Pasted%20image%2020251202214919.png)

- هنستخدم موقع https://github.com/WhiteWinterWolf/wwwolf-php-webshell عشان نجيب php web shell code وناخده كوبي نحطه ف ملف .php
- دلوقتي انت بترفع logo يعني لازك يكون امتداد الصوره png او jpg او gif فا لما ترفعله ملف php مش هيقبله طب ازاي هنتخطي الموضوع ده؟ باستخدام الburp suite
----
## Bypassing the File Type Restriction
- هنعمل intercept للPOST Request بتاع رفع الملف
![](Pasted%20image%2020251202215245.png)
- اول حاجه هو بيمنع ازاي رفع ملفات غير الpng او jpg او ايا كان؟ هو بيبص علي الهيدر فالريكويست `Content-Type` المبرمج بتاع الموقع بيبقي عامله فا احنا هنخليه 
```
Content-Type: image/gif
```
عشان هنا السيرفر يفكر ان الملف ده صوره فا يعديه عادي ويكمل
![](Pasted%20image%2020251202215610.png)
- قالي هنا Added new vendor NetVen to Database معني كده ان الملف اترفع تمام واتسيف فالمكان ده /images/vendor
----
## Considerations when Dealing with Web Shells
1. **احتمال ان الملف يتمسح بعد مدة معينه**
2. **محدودية التفاعل يعني مش هتعرف تعمل كل حاجه زي الshell العادي**
3. **الweb shell مش مستقر**
4. **افضل حاجه هو الreverse shell**
---

