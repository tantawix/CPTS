- الDNS بيشتغل كأنه GPS للإنترنت مهمته انه يحول الdomain بتاع موقع معين لIP يعني مثلا موقع www.example.com يروح يترجمه ل IP وليكن 192.0.2.1 لأن فالأساس اصلا ان المواقع دي عباره عن سيرفرات وليها ip عادي جدا
----
## How DNS Works
- تخيل معايا انت عاوز تفتح موقع www.example.com فا هتكتب ده فالمتصفح بتاعك بس الكمبيوتر مش فاهم الكلام اللي انت دخلته ده الكمبيوتر مبيفهمش غير ارقام اللي هو الIP
- طب ازاي هنجيب الIP بتاع الموقع بقي؟ هنا بيجي دور الDNS
1. **Your Computer Asks for Directions (DNS Query):**
- لما انت تكتب الdomain name الكمبيوتر بتاعك بيشوف الcache بتاعه لأن مثلا انت لو زورت موقع الفيسبوك قبل كده وحصلت عملية الdns والسيرفر جابلك الip والموقع فتح والكلام ده كله بيروح الكمبيوتر بتاعك مخزن الip ده جوا ملف ف الجهاز اللي هو الcache
- فا هنا اول لما باجي اكتب domain بتاع موقع الكمبيوتر بيروح يشوف الcache الاول لو موجود فالcache خلاص هيجبلك الip والموقع يفتح طب لو مش موجود؟ هيروح بقي للDNS الخاص بالISP بتاعك
2. **The DNS Resolver Checks its Map (Recursive Lookup):**
- لو الip طلع موجود فالdns بتاع الISP تمام خلاص طب لو مش موجود؟ بنروح بقي للDNS Resolver
- ولو مش موجود فالDNS Resolver هيروح بقي للroot name server
3. **Root Name Server Points the Way:**
- ده بقي الباشا الكبير بيشوف اذا كان موجود عنده الip ولا لأ لو موجود خلاص بيرجعو ليك وخلاص ولو مش موجود بيروح بقي للمعلم اللي هو الTop-Level Domain Server وده المسؤول عن تخزين الدومينات اللي بينتهو ب(.com , .net, .org)
4. **TLD Name Server Narrows It Down:**
- هنا بيتم عملية الresolve فال TLD Server وده اللي مسؤول عن specific domains زي ماقولنا فوق
5. **Authoritative Name Server Delivers the Address**
- هنا بقي لو كله عطل ومفيش ولا سيرفر من دول عنده ip الموقع بنروح للمعلم ده اخر حاجه وده اللي متخزن فيه كل حاجه وكده كده هيرجعلك الip
6. **The DNS Resolver Returns the Information:**
- هنا بقي الresolver بيستلم الip ويرجعو ليك ويعمله caching بحيث يخزنه عندك فالجهاز
---
### The Hosts File
- ده ملف بسيط كده بيكون فيه map لل hostname والIP بتاع الhost ده
- فايل الhost بيسمحلك تحط انت الhost والip بتاعه بشكل manual

- The `hosts` file is located in `C:\Windows\System32\drivers\etc\hosts` on Windows and in `/etc/hosts` on Linux and MacOS. Each line in the file follows the format:

```txt
<IP Address>    <Hostname> [<Alias> ...]
```

For example:

```txt
127.0.0.1       localhost
192.168.1.10    devserver.local
```
- طبعا عشان اعدل علي الملف ده سواء لينكس او ويندوز لازم اكون root او admin

```txt
127.0.0.1       myapp.local
```

testing connectivity by specifying an IP address:

```txt
192.168.1.20    testserver.local
```

or blocking unwanted websites by redirecting their domains to a non-existent IP address:

```txt
0.0.0.0       unwanted-site.com
```
---
### Key DNS Concepts

#### ايه هي "DNS Zone"؟

تخيل إن شبكة الإنترنت عبارة عن **مدينة ضخمة**، والـ**DNS Zone** هو زي **حي أو منطقة محددة** في المدينة دي.

- كل حي (Zone) بيكون ليه **صاحب واحد أو مدير واحد** مسؤول عن تنظيم كل حاجة جواه.
    
- المنطقة دي بتحتوي على اسم دومين رئيسي (زي `example.com`) وكل العناوين الفرعية اللي بتيجي تحته (زي `mail.example.com` أو `blog.example.com`).
    

**الخلاصة:** الـDNS Zone هي زي **الحاوية الافتراضية** اللي بتضم مجموعة من الدومينات الفرعية، وبتكون تحت إدارة شخص أو جهة واحدة.

#### إيه هو Zone File؟

الـZone File ده هو **دفتر تسجيلات** المنطقة دي:
1. هو مجرد **ملف نصي عادي** بيكون موجود على سيرفر الـDNS.
2. الملف ده بيسجل كل المعلومات المهمة اللي محتاجها الإنترنت عشان "يترجم" اسم الدومين لـ**عنوان IP** يقدر الجهاز يوصل بيه.
#### مثال:

لو حد طلب `mail.example.com`، سيرفر الـDNS المسؤول عن الـZone دي هيبص في الـZone File ويقول: "آه، `mail.example.com` ده بيشاور على الـIP رقم `192.168.1.5`".

الـZone File هو اللي بيحتوي على الـ**Resource Records** (سجلات الموارد) اللي هي البيانات اللي بتوصل اسم الموقع بعنوانه الرقمي

```dns-zone
$TTL 3600 ; Default Time-To-Live (1 hour)
@       IN SOA   ns1.example.com. admin.example.com. (
                2024060401 ; Serial number (YYYYMMDDNN)
                3600       ; Refresh interval
                900        ; Retry interval
                604800     ; Expire time
                86400 )    ; Minimum TTL

@       IN NS    ns1.example.com.
@       IN NS    ns2.example.com.
@       IN MX 10 mail.example.com.
www     IN A     192.0.2.1
mail    IN A     198.51.100.1
ftp     IN CNAME www.example.com.
```

| DNS Concept                 | Description                                                                      | Example                                                                                                                                     |                                                                                                |
| --------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `Domain Name`               | A human-readable label for a website or other internet resource.                 | `www.example.com`                                                                                                                           |                                                                                                |
| `IP Address`                | A unique numerical identifier assigned to each device connected to the internet. | `192.0.2.1`                                                                                                                                 |                                                                                                |
| `DNS Resolver`              | A server that translates domain names into IP addresses.                         | Your ISP's DNS server or public resolvers like Google DNS (`8.8.8.8`)                                                                       |                                                                                                |
| `Root Name Server`          | The top-level servers in the DNS hierarchy.                                      | There are 13 root servers worldwide, named A-M: `a.root-servers.net`                                                                        |                                                                                                |
| `TLD Name Server`           | Servers responsible for specific top-level domains (e.g., .com, .org).           | [Verisign](https://en.wikipedia.org/wiki/Verisign) for `.com`, [PIR](https://en.wikipedia.org/wiki/Public_Interest_Registry) for `.org`     |                                                                                                |
| `Authoritative Name Server` | The server that holds the actual IP address for a domain.                        | Often managed by hosting providers or domain registrars.                                                                                    |                                                                                                |
| `DNS Record Types`          | Different types of information stored in DNS.                                    | A, AAAA, CNAME, MX, NS, TXT, etc.                                                                                                           |                                                                                                |
| Record Type                 | Full Name                                                                        | Description                                                                                                                                 | Zone File Example                                                                              |
| `A`                         | Address Record                                                                   | Maps a hostname to its IPv4 address.                                                                                                        | `www.example.com.` IN A `192.0.2.1`                                                            |
| `AAAA`                      | IPv6 Address Record                                                              | Maps a hostname to its IPv6 address.                                                                                                        | `www.example.com.` IN AAAA `2001:db8:85a3::8a2e:370:7334`                                      |
| `CNAME`                     | Canonical Name Record                                                            | Creates an alias for a hostname, pointing it to another hostname.                                                                           | `blog.example.com.` IN CNAME `webserver.example.net.`                                          |
| `MX`                        | Mail Exchange Record                                                             | Specifies the mail server(s) responsible for handling email for the domain.                                                                 | `example.com.` IN MX 10 `mail.example.com.`                                                    |
| `NS`                        | Name Server Record                                                               | Delegates a DNS zone to a specific authoritative name server.                                                                               | `example.com.` IN NS `ns1.example.com.`                                                        |
| `TXT`                       | Text Record                                                                      | Stores arbitrary text information, often used for domain verification or security policies.                                                 | `example.com.` IN TXT `"v=spf1 mx -all"` (SPF record)                                          |
| `SOA`                       | Start of Authority Record                                                        | Specifies administrative information about a DNS zone, including the primary name server, responsible person's email, and other parameters. | `example.com.` IN SOA `ns1.example.com. admin.example.com. 2024060301 10800 3600 604800 86400` |
| `SRV`                       | Service Record                                                                   | Defines the hostname and port number for specific services.                                                                                 | `_sip._udp.example.com.` IN SRV 10 5 5060 `sipserver.example.com.`                             |
| `PTR`                       | Pointer Record                                                                   | Used for reverse DNS lookups, mapping an IP address to a hostname.                                                                          | `1.2.0.192.in-addr.arpa.` IN PTR `www.example.com.`                                            |