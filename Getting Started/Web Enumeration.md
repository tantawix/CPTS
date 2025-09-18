## Gobuster
#### Directory/File Enumeration
  
GoBuster is a versatile tool that allows for performing DNS, vhost, and directory brute-forcing. The tool has additional functionality, such as enumeration of public AWS S3 buckets. For this module's purposes, we are interested in the directory (and file) brute-forcing modes specified with the switch `dir`.
```
0xTantawy@htb[/htb]$ gobuster dir -u http://10.10.10.121/ -w /usr/share/seclists/Discovery/Web-Content/common.txt

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.121/
[+] Threads:        10
[+] Wordlist:       /usr/share/seclists/Discovery/Web-Content/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/12/11 21:47:25 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/index.php (Status: 200)
/server-status (Status: 403)
/wordpress (Status: 301)
===============================================================
2020/12/11 21:47:46 Finished
===============================================================
```
200 => request was sucessful
403 => forbidden or you don't have permission to access to this file
301 => indicates that we are being redirected, which is not a failure case
- _1xx informational response_ – the request was received, continuing process
- _2xx successful_ – the request was successfully received, understood, and accepted
- _3xx redirection_ – further action needs to be taken in order to complete the request
- _4xx client error_ – the request contains bad syntax or cannot be fulfilled
- _5xx server error_ – the server failed to fulfil an apparently valid request

| Code    | Category     | Meaning                                                            |
| ------- | ------------ | ------------------------------------------------------------------ |
| **200** | Success      | OK → كل حاجة تمام، السيرفر رجع Response عادي.                      |
| **201** | Success      | Created → تم إنشاء Resource جديدة (زي User جديد).                  |
| **301** | Redirection  | Moved Permanently → الصفحة اتنقلت لمكان جديد بشكل دائم.            |
| **302** | Redirection  | Found → الصفحة اتحركت بشكل مؤقت (Temporary Redirect).              |
| **304** | Redirection  | Not Modified → الكاش لسه صالح ومفيش تغيير.                         |
| **400** | Client Error | Bad Request → في Error في الفورمات بتاع الريكوست.                  |
| **401** | Client Error | Unauthorized → محتاج Authentication.                               |
| **403** | Client Error | Forbidden → السيرفر رافض يديك Access حتى لو متأكد من وجود الصفحة.  |
| **404** | Client Error | Not Found → الصفحة مش موجودة.                                      |
| **405** | Client Error | Method Not Allowed → الميثود (POST/GET) مش مدعومة.                 |
| **408** | Client Error | Request Timeout → الريكوست خد وقت زيادة.                           |
| **409** | Client Error | Conflict → تعارض مع حالة السيرفر (زي User ID مكرر).                |
| **429** | Client Error | Too Many Requests → Rate Limit اتجاوز.                             |
| **500** | Server Error | Internal Server Error → مشكلة عامة في السيرفر.                     |
| **502** | Server Error | Bad Gateway → السيرفر رجع Response بايظ من سيرفر تاني.             |
| **503** | Server Error | Service Unavailable → السيرفر واقع أو Overloaded.                  |
| **504** | Server Error | Gateway Timeout → السيرفر اللي بيكلم Upstream Server مستناش كفاية. |
#### DNS Subdomain Enumeration
```
0xTantawy@htb[/htb]$ gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Domain:     inlanefreight.com
[+] Threads:    10
[+] Timeout:    1s
[+] Wordlist:   /usr/share/SecLists/Discovery/DNS/namelist.txt
===============================================================
2020/12/17 23:08:55 Starting gobuster
===============================================================
Found: blog.inlanefreight.com
Found: customer.inlanefreight.com
Found: my.inlanefreight.com
Found: ns1.inlanefreight.com
Found: ns2.inlanefreight.com
Found: ns3.inlanefreight.com
===============================================================
2020/12/17 23:10:34 Finished
===============================================================
```
here we replace `dir` flag with `dns` flag

----
## Web Enumeration Tips
#### Banner Grabbing / Web Server Headers
Web server headers provide a good picture of what is hosted on a web server. They can reveal the specific application framework in use, the authentication options, and whether the server is missing essential security options or has been misconfigured. We can use `cURL` to retrieve server header information from the command line. `cURL` is another essential addition to our penetration testing toolkit, and familiarity with its many options is encouraged.
```
$ curl -IL https://www.inlanefreight.com

HTTP/1.1 200 OK
Date: Fri, 18 Dec 2020 22:24:05 GMT
Server: Apache/2.4.29 (Ubuntu)
Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/"
Link: <https://www.inlanefreight.com/>; rel=shortlink
Content-Type: text/html; charset=UTF-8
```
-I => show headers only (ignore body)
-L => follow redirects
#### Whatweb
we can know web server version and another inforamtions by **whatweb** tool
```
$ whatweb 10.10.10.121

http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()]
```

```
$ whatweb --no-errors 10.10.10.0/24

http://10.10.10.11 [200 OK] Country[RESERVED][ZZ], HTTPServer[nginx/1.14.1], IP[10.10.10.11], PoweredBy[Red,nginx], Title[Test Page for the Nginx HTTP Server on Red Hat Enterprise Linux], nginx[1.14.1]
http://10.10.10.100 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.100], Title[File Sharing Service]
http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()]
http://10.10.10.247 [200 OK] Bootstrap, Country[RESERVED][ZZ], Email[contact@cross-fit.htb], Frame, HTML5, HTTPServer[OpenBSD httpd], IP[10.10.10.247], JQuery[3.3.1], PHP[7.4.12], Script, Title[Fine Wines], X-Powered-By[PHP/7.4.12], X-UA-Compatible[ie=edge]
```
#### Certificates
SSL/TLS certificates are another potentially valuable source of information if HTTPS is in use. Browsing to `https://10.10.10.121/` and viewing the certificate reveals the details below, including the email address and company name. These could potentially be used to conduct a phishing attack if this is within the scope of an assessment.
- شهادات الTLS/SSL ممكن تديك معلومات عن التارجت ممكن انها تreveal تفاصيل مهمه
![](Pasted%20image%2020250910191011.png)
#### Robots.txt
- `robots.txt` file, whose purpose is to instruct search engine web crawlers such as Googlebot which resources can and cannot be accessed for indexing
- The `robots.txt` file can provide valuable information such as the location of private files and admin pages.
#### Source Code
- حتة اني افحص الsource code بتاع الموقع مهمه جدا لأن ممكن يكون الdeveloper سايب معلومات معينه فالكود تقدر تستفاد انت منها كا pentester
![](Pasted%20image%2020250910191158.png)
شوف حاجه زي دي مثلا

----
