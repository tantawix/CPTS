- This often starts with the `S3 buckets` (AWS), `blobs` (Azure), `cloud storage` (GCP), which can be accessed without authentication if configured incorrectly
#### Company Hosted Servers

```shell-session
0xTroj6n@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

blog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250
```
- ممكن استخدم google dorks عشان تشوف المواقع اللي بتستخدم cloud service معينه يعني مثلا لو عاوز اشوف المواقع اللي بتستخدم أمازون `inurl:amazonaws.com` or Azure `inurl;:blob.core.windows.net`
- موقع domain.glass ممكن يدينا معلومات عن الinfra بتاعت الشركه اللي انا بتارجتها
- برضو في موقع **GrayHatWarfare** موقع/محرك بحث متخصص بيجمّع ويعرض “cloud storage” اللي اتكشف إنه **مفتوح للعامة** (خصوصاً Amazon S3 وبعدين Azure Blob وGoogle Cloud Storage). تقدر تدور بيه على ملفات وباكتس (buckets) مكشوفة، وتفلتر النتائج حسب امتداد الملف (pdf, xlsx, sql, db، إلخ)، الحجم، أو تاريخ التعديل — يعني تقدر تلاقي بسرعة ملفات حسّاسة لو كانت موجودة ومفتوحة
- ساعات ممكن تلاقي Private and Public SSH Keys Leaked لما تيجي تبحث عن التارجت greyhatwarfare
- الموظفين مع الشغل الكتير او الضغط ممكن يخطأو ويبقي في حاجات حساسه leaked
---
