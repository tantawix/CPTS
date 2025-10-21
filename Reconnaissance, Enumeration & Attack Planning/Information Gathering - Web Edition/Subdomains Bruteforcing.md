- الSubdomain Brute-Forece Enum ده من اقوي تكنيكس الactive subdomain discovery
- فكرته انه بيكون معايا wordlist فيها كلمات معينه محتمله انها تكون subdomains عامة وبيكون في اداة هيا اللي بتجربلي بقي الكلام ده كله

**الموضوع ده كله بيندرج تحت اربع خطوات**
 1. **Wordlist Selection:**
 - General-Purpose
 - Targeted
 - Custom
 2. **Iteration and Querying:**
 -  Script or tool Iterates through the wordlist appending each word pr phase to the main domain
3. **DNS Lookup:**
- A DNS query is performed for each potential subdomain to check if it resolves to an IP address
4. **Filtering and Validation:**
-  If a subdomain resolves successfully, it's added to a list of valid subdomains. Further validation steps might be taken to confirm the subdomain's existence and functionality
في أدوات كتير بتعملنا عملية الbrute force دي زي: 

|Tool|Description|
|---|---|
|[dnsenum](https://github.com/fwaeytens/dnsenum)|Comprehensive DNS enumeration tool that supports dictionary and brute-force attacks for discovering subdomains.|
|[fierce](https://github.com/mschwager/fierce)|User-friendly tool for recursive subdomain discovery, featuring wildcard detection and an easy-to-use interface.|
|[dnsrecon](https://github.com/darkoperator/dnsrecon)|Versatile tool that combines multiple DNS reconnaissance techniques and offers customisable output formats.|
|[amass](https://github.com/owasp-amass/amass)|Actively maintained tool focused on subdomain discovery, known for its integration with other tools and extensive data sources.|
|[assetfinder](https://github.com/tomnomnom/assetfinder)|Simple yet effective tool for finding subdomains using various techniques, ideal for quick and lightweight scans.|
|[puredns](https://github.com/d3mondev/puredns)|Powerful and flexible DNS brute-forcing tool, capable of resolving and filtering results effectively.|

---
### DNSEnum
- أداة الDNSenum اداة مستخدمه بشكل كبير جدا وقويه ومكتوبة بلغه برمجة Perl
- الأداه ممكن تعملك DNS Record Enumeration او Zone Transfer Attempts او Subdomain Brute-Forcing , Google Scraping , Reverse Lookup, WHOIS Lokkups
```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -r
```

- `-r`: This option enables recursive subdomain brute-forcing, meaning that if `dnsenum` finds a subdomain, it will then try to enumerate subdomains of that subdomain.

```shell-session
[!bash!]$ dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt 

dnsenum VERSION:1.2.6

-----   inlanefreight.com   -----


Host's addresses:
__________________

inlanefreight.com.                       300      IN    A        134.209.24.248

[...]

Brute forcing with /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:
_______________________________________________________________________________________

www.inlanefreight.com.                   300      IN    A        134.209.24.248
support.inlanefreight.com.               300      IN    A        134.209.24.248
[...]


done.
```