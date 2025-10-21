- الCrawling او ساعات بنقول عليها Spidering
- تخيل معايا اني الworld wide web ده عبارة عن شبكه عنكبوت ضحمه وكل صفحه علي الانترنت هي عقده في شبكة العنكبوت دي
- **الWeb Crawler**: ده برنامج الي وظيفته إنه يتصفح الانترنت بشكل منتظم وتلقائي
## How Web Crawlers Work
1. 1. `Homepage`: You start with the homepage containing `link1`, `link2`, and `link3`
```txt
Homepage
├── link1
├── link2
└── link3
```
2. `Visiting link1`: Visiting `link1` shows the homepage, `link2`, and also `link4` and `link5`
```txt
link1 Page
├── Homepage
├── link2
├── link4
└── link5
```
3. `Continuing the Crawl`: The crawler continues to follow these links systematically, gathering all accessible pages and their links.