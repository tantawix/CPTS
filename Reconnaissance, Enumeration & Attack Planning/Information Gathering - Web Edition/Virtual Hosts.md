- انت كا واحد سيكيورتي اكيد عارف سيرفر زي Apache او Nginx او IIS ودول عباره عن web servers والحقيقه هو كل واحد من دول عباره عن سيرفر واحد بس طب ازاي في ملايين المستخدمين بيستخدموه للمواقع بتاعتهم وهو سيرفر واحد بس؟
## How Virtual Hosts Work: Understanding VHosts and Subdomains

- زي ماقولنا فوق انه سيرفر واحد وفي ملايين المستخدمين بيستخدموه وكلهم ليهم نفس الIP يعني لو في موقع site-a.com و site-b.com الاتنين ليهم نفس الIP لأنهم علي نفس السيرفر السيرفر هنا بيحتار هو قصده ده ولا ده
- مهمة الVirtual Hosting الاساسية هيا قدرة الweb servers علي انها تفرق بين الويبسايتس اللي علي نفس الIP
- **الحل** هنا بيكمن في الHTTP Host Header يعني ف كل مره بتفتح website معين المتصفح بيبعت طلب للسيرفر ويحط معاه عنوان الموقع اللي انت كتبته بالظبط
- يعني مثلا عاوز تروح لsite-a.com فا الheader فالريكويست هيكون فيه الIP بتاع السيرفر والHost كده `Host:site-a.com`

 **Difference Between VHosts and subdomains is their relationship to the `Domain Name System (DNS)` and the web server's configuration.**
 1. **Subdomains:**
- الsubdomain هو امتداد للmain domain يعني example.com الصب دومين بتاعه مثلا blog.example.com والmain domain والsubdomain ليهم نفس الDNS Record الخاص بالدومين العادي بتاعهم وبيشيرو لنفس الIP او IP تاني مختلف
2. **Virtual Host:**
- الvhost ده بيكون configurations فالويب سيرفر بتمسح ان يكون في اكتر من ويب سايت او ابلكيشن علي نفس السيرفر سواء بقي main domain او subdomain
- عاوز الفت نظرك لحاجه ان بيكون في ويب سايتس ليها subdomains مش oublic ومش بتظهر فالDNS Records الsubdomains دي بتكونonly accessible internally or through specific configurations وعشان اوصل لحاجه زي دي ممكن تعمل حاجه اسمها `VHost fuzzing` 

```apacheconf
# Example of name-based virtual host configuration in Apache
<VirtualHost *:80>
    ServerName www.example1.com
    DocumentRoot /var/www/example1
</VirtualHost>

<VirtualHost *:80>
    ServerName www.example2.org
    DocumentRoot /var/www/example2
</VirtualHost>

<VirtualHost *:80>
    ServerName www.another-example.net
    DocumentRoot /var/www/another-example
</VirtualHost>
```

1. `Browser Requests a Website`: When you enter a domain name (e.g., `www.inlanefreight.com`) into your browser, it initiates an HTTP request to the web server associated with that domain's IP address.
2. `Host Header Reveals the Domain`: The browser includes the domain name in the request's `Host` header, which acts as a label to inform the web server which website is being requested.
3. `Web Server Determines the Virtual Host`: The web server receives the request, examines the `Host` header, and consults its virtual host configuration to find a matching entry for the requested domain name.
4. `Serving the Right Content`: Upon identifying the correct virtual host configuration, the web server retrieves the corresponding files and resources associated with that website from its document root and sends them back to the browser as the HTTP response.
---
### Types of Virtual Hosting
###### Name-Based Virtual Hosting
- الطريقة دي بتعتمد علي الHTTP Host Header عشان تفرق بين الويب سايتس
- دي اشهر واحسن طريقة ومش بتتطلب multiple ip ومش مشكله وسهلة كا setup
###### IP-Based Virtual Hosting
- الطريقه دي من الhosting بيتعين unique ip لكل ويب سايت موجود علي السيرفر
- السيرفر هنا مش بيفرق عن طريق الhost header بقي هيفرق عن طريق الip اللي مديه لكل ويب سايت
- هنا انا بحتاج multiple ip لأني هدي لكل ويب سايت ip فهحتاج كتير
###### Port-Based Virtual Hosting
- هنا انا بفرق المواقع عن بعض عن طريق الports
- يعني مثلا ويب سايت محطوط علي بورت 80 مثلا ممكن احط ويب سايت تاني علي 8080
----
## Virtual Host Discovery Tools
|Tool|Description|Features|
|---|---|---|
|[gobuster](https://github.com/OJ/gobuster)|A multi-purpose tool often used for directory/file brute-forcing, but also effective for virtual host discovery.|Fast, supports multiple HTTP methods, can use custom wordlists.|
|[Feroxbuster](https://github.com/epi052/feroxbuster)|Similar to Gobuster, but with a Rust-based implementation, known for its speed and flexibility.|Supports recursion, wildcard discovery, and various filters.|
|[ffuf](https://github.com/ffuf/ffuf)|Another fast web fuzzer that can be used for virtual host discovery by fuzzing the `Host` header.|Customizable wordlist input and filtering options.|
### gobuster
```shell-session
0xTroj6n@htb[/htb]$ gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
```
There are a couple of other arguments that are worth knowing:
- Consider using the `-t` flag to increase the number of threads for faster scanning.
- The `-k` flag can ignore SSL/TLS certificate errors.
- You can use the `-o` flag to save the output to a file for later analysis

```shell-session
0xTroj6n@htb[/htb]$ gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://inlanefreight.htb:81
[+] Method:          GET
[+] Threads:         10
[+] Wordlist:        /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
[+] User Agent:      gobuster/3.6
[+] Timeout:         10s
[+] Append Domain:   true
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
Found: forum.inlanefreight.htb:81 Status: 200 [Size: 100]
[...]
Progress: 114441 / 114442 (100.00%)
===============================================================
Finished
===============================================================
```