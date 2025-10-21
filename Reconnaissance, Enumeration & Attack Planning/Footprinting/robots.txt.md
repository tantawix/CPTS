- ده عباره عن ملف بيكون فيه configuration معين بيعمل كدليل توجية للsearch engines يعني بيكون فيه اوامر مثلا اني بقول للبرنامج انت مسمحولك تشوف الصفحات دي ومش مسموحلك تشوف دي وهكذا
##### إزاي بيشتغل؟
1. **الـCrawler بيجي:** أي برنامج زحف (Crawler) بيزور الموقع بتاعك، أول حاجة بيعملها هي إنه بيدور على ملف `robots.txt` (اللي بيكون موجود في العنوان ده: `example.com/robots.txt`).
2. **بيقرأ التعليمات:** بيقرأ التعليمات اللي فيه، ودي زي:
    
    - **`User-agent: *`**: (التعليمات دي لكل أنواع الروبوتات).
        
    - **`Disallow: /admin/`**: (ممنوع تدخل على مجلد الإدارة).
`www.example.com/robots.txt`
---
### Understanding robots.txt Structure
- الrobots.txt هوا ملف plain text يعني سهل يتقرأ ويتفهم
1. `User-agent`: This line specifies which crawler or bot the following rules apply to. A wildcard (`*`) indicates that the rules apply to all bots. Specific user agents can also be targeted, such as "Googlebot" (Google's crawler) or "Bingbot" (Microsoft's crawler).
2. `Directives`: These lines provide specific instructions to the identified user-agent

|Directive|Description|Example|
|---|---|---|
|`Disallow`|Specifies paths or patterns that the bot should not crawl.|`Disallow: /admin/` (disallow access to the admin directory)|
|`Allow`|Explicitly permits the bot to crawl specific paths or patterns, even if they fall under a broader `Disallow` rule.|`Allow: /public/` (allow access to the public directory)|
|`Crawl-delay`|Sets a delay (in seconds) between successive requests from the bot to avoid overloading the server.|`Crawl-delay: 10` (10-second delay between requests)|
|`Sitemap`|Provides the URL to an XML sitemap for more efficient crawling.|`Sitemap: https://www.example.com/sitemap.xml`|

---
## robots.txt in Web Reconnaissance
- Uncovering Hidden Directories
- Mapping Website Structure
- Detecting Crawler Traps