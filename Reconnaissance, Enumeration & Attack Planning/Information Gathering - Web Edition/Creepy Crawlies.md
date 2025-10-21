## Popular Web Crawlers

1. `Burp Suite Spider`: Burp Suite, a widely used web application testing platform, includes a powerful active crawler called Spider. Spider excels at mapping out web applications, identifying hidden content, and uncovering potential vulnerabilities.
2. `OWASP ZAP (Zed Attack Proxy)`: ZAP is a free, open-source web application security scanner. It can be used in automated and manual modes and includes a spider component to crawl web applications and identify potential vulnerabilities.
3. `Scrapy (Python Framework)`: Scrapy is a versatile and scalable Python framework for building custom web crawlers. It provides rich features for extracting structured data from websites, handling complex crawling scenarios, and automating data processing. Its flexibility makes it ideal for tailored reconnaissance tasks.
4. `Apache Nutch (Scalable Crawler)`: Nutch is a highly extensible and scalable open-source web crawler written in Java. It's designed to handle massive crawls across the entire web or focus on specific domains. While it requires more technical expertise to set up and configure, its power and flexibility make it a valuable asset for large-scale reconnaissance projects.
---
### ReconSpider

```shell-session
0xTroj6n@htb[/htb]$ python3 ReconSpider.py http://inlanefreight.com
```
- خلي بالك الreconspider بتخزن النتائج في ملف اسمه results.json

```json
{
    "emails": [
        "lily.floid@inlanefreight.com",
        "cvs@inlanefreight.com",
        ...
    ],
    "links": [
        "https://www.themeansar.com",
        "https://www.inlanefreight.com/index.php/offices/",
        ...
    ],
    "external_files": [
        "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf",
        ...
    ],
    "js_files": [
        "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2",
        ...
    ],
    "form_fields": [],
    "images": [
        "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png",
        ...
    ],
    "videos": [],
    "audio": [],
    "comments": [
        "<!-- #masthead -->",
        ...
    ]
}
```

|                  |                                                                        |
| ---------------- | ---------------------------------------------------------------------- |
| `emails`         | Lists email addresses found on the domain.                             |
| `links`          | Lists URLs of links found within the domain.                           |
| `external_files` | Lists URLs of external files such as PDFs.                             |
| `js_files`       | Lists URLs of JavaScript files used by the website.                    |
| `form_fields`    | Lists form fields found on the domain (empty in this example).         |
| `images`         | Lists URLs of images found on the domain.                              |
| `videos`         | Lists URLs of videos found on the domain (empty in this example).      |
| `audio`          | Lists URLs of audio files found on the domain (empty in this example). |
| `comments`       | Lists HTML comments found in the source code.                          |