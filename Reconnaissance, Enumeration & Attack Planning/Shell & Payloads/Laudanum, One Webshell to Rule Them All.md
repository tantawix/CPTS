- Laudanum is a repo of ready-made files that can be used to inject onto a victim and recive back access via a reverse shell
- الrepo دي فيها ملفات injectable لكذا web app langs وي `asp, aspx, jsp,php`
---
## Working with Laudanum
- بعد ما تنزل الdir ده عندك فيه ملفات جاهزه يعني مثلا الموقع معمول ب.NET هتخش علي dir الaspx وتاخد الملف جاهز وترفعه
- بس خلي بالك قبل ماترفع الملف لازم تعدل فيه الattacking host ip اللي هو ip جهازك كا هاكر يعني عشان يتعمل callback ليه
---
## Laudanum Demonstration
#### Move a Copy for Modification

```shell-session
0xTroj6n@htb[/htb]$ cp /usr/share/laudanum/aspx/shell.aspx /home/tester/demo.aspx
```

Add your IP address to the `allowedIps` variable on line `59`. Make any other changes you wish. It can be prudent to remove the ASCII art and comments from the file. These items in a payload are often signatured on and can alert the defenders/AV to what you are doing.
- بعد كده هتروح للمكان الfile upload وترفع الملف
- بعد ما ترفعه تروح للمكان اللي بيتخزن فيه علي السيرفر في المثال بتاعنا هو بيتخزن ف فايل اسمه `\\files\` فا هنروح نكتب الurl ده
`status.inlanefreight.local\\files\demo.aspx`
- هيفتح معاك الملف وتقدر تنفذ اوامر علي الos بتاع السيرفر
---
