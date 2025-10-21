- واحنا بنexpolre الDNS Records لدومين معين ممكن يكون في subdomains تحت الdomain الرئيسي ده وممكن نطلع منهم بحاجه
- الsubdomains هيا عباره عن extensions من الmain domain معمولين عشان ينظمو وظيفه معينه مثلا لو عندنا main domain `example.com` الsubdomain هنا هيبقي مثلا `blog.example.com` `shop.example.com`
## Subdomain Enumeration
- الsubdomain enumeration هيا عملية عشان اعرف من خلالها الsubdomains وده بيتم عن طريق الDNS
- الCNAME Record مستخدم عشان يعمل aliasing للsubdomains
#### 1. Active Subdomain Enumeration
- نفس الفكره برضو انا هنا بتفاعل بشكل مباشر مع التارجت عشان اجيب الsubdomains
- اول طريقه اني ممكن احاول اشوف الDNS Zone Transfer ممكن يكون فيها misconfiguration وفيها مثلا قايمة بالsubdomains
- تاني تكنيك وهي اني اعمل brute-force اللي هو اقعد اخمن subdomains يعني يكون عندي wordlist فيها كلمات معينه اجربها أنها تكون subdomains وده بيتم عن طريق ادوات زي `dnsenum` `Gobuster` `ffuf`
#### 2. Passive Subdomain Enumeration
- هنا انا كا attacker بحاول اشوف subdomains متسربه علي الانترنت وبرضو ممكن اجيبه من الCT Logs , الـCT Logs دي عبارة عن قواعد بيانات عامة بتسجل كل شهادات الـSSL/TLS اللي بتتصدر لدومين معين. بما إن كل Subdomain بيحتاج شهادة، فده بيخليها تتسجل في السجلات دي زي موقع Censys او crt.sh
- او من خلال الsearch engines زي Google Dorks مثلا
---
