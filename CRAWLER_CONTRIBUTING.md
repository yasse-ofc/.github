# How to create a crawler <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->

- [How to setup your environment](#how-to-setup-your-environment)
- [Find out which crawler type to use](#find-out-which-crawler-type-to-use)
- [Crawler Creation](#crawler-creation)
  - [How to create HTTP Crawler](#how-to-create-http-crawler)
  - [How to create Cheerio Crawler](#how-to-create-cheerio-crawler)
  - [How to create Puppeteer Crawler](#how-to-create-puppeteer-crawler)
- [Any Questions?](#any-questions)
  - [New to Web Scraping?](#new-to-web-scraping)
  - [Crawler Framework](#crawler-framework)
  - [Crawler Type](#crawler-type)
    - [HTTP Crawler](#http-crawler)
    - [Cheerio Crawler](#cheerio-crawler)
    - [Puppeteer Crawler](#puppeteer-crawler)
  - [Anything else](#anything-else)

## How to setup your environment

[Described here]('./../CONTRIBUTING.md#yasse-crawlers').

## Find out which crawler type to use

- Does the page use an API to gather data? **Use HTTP Crawler**
- Is the page static? **Use Cheerio Crawler**
- Does the page need JS rendering? **Use Puppeteer Crawler**

Follow this order in figuring out which crawler to use, because it goes from the best scenario to the worst one.

## Crawler Creation

### How to create HTTP Crawler

Use [Goyabu Crawler](https://github.com/yasse-ofc/yasse-crawlers/tree/main/src/crawlers/http_crawlers/goyabu_crawler) as a template.

What will change is:

**main.ts**:

```ts
...

const startUrls = [LINK_TO_THE_API];

...

const title = SERIES_TITLE_FIELD_IN_API;
const href = SERIES_LINK_FIELD_IN_API;
const img = SERIES_IMG_LINK_FIELD_IN_API;
const latestChapter = SERIES_LATEST_CHAPTER_FIELD_IN_API;
const source = SOURCE_WEBSITE_NAME;

...

await insertManyToDB(COLLECTION_NAME_TO_ADD, processedData.slice(0, -1));
```

### How to create Cheerio Crawler

Use [BRMangas Crawler](https://github.com/yasse-ofc/yasse-crawlers/tree/main/src/crawlers/cheerio_crawlers/brmangas_crawler) as a template.

What will change is:

**main.ts**:

```ts
...

const startUrls = [LINK_TO_STARTING_WEBSITE];

...
```

**routes.ts**:

```ts
...

await enqueueLinks({
    globs: [GLOB_TO_INDIVIDUAL_SERIES_LINK],
    label: 'series_page'
});

await enqueueLinks({
    globs: [GLOB_TO_SERIES_LISTING_PAGES],
});

...

const title = $(CSS_SELECTOR_FOR_TITLE).toLowerCase();
const href = request.url;
const img = $(CSS_SELECTOR_FOR_IMAGE).attr('src');
const latestChapter = $(CSS_SELECTOR_FOR_LATEST_CHAPTER).text();
const source = SOURCE_WEBSITE_NAME;

await insertOneToDB(COLLECTION_NAME_TO_ADD, {
    title,
    href,
    img,
    latestChapter,
    source,
});
```

### How to create Puppeteer Crawler

Use [Lezhinus Crawler](https://github.com/yasse-ofc/yasse-crawlers/tree/main/src/crawlers/puppeteer_crawlers/lezhinus_crawler) as a template.

What will change is:

**main.ts**:

```ts
...

const startUrls = [LINK_TO_STARTING_WEBSITE];

...
```

**routes.ts**:

```ts
...

await enqueueLinks({
    globs: [GLOB_TO_INDIVIDUAL_SERIES_LINK],
    label: 'series_page'
});

await enqueueLinks({
    globs: [GLOB_TO_SERIES_LISTING_PAGES],
});

...

const title = (await page.$eval(CSS_SELECTOR_FOR_TITLE, el => el.textContent) ?? '').toLowerCase();
const href = request.url;
const img = await page.$eval(CSS_SELECTOR_FOR_IMAGE, el => el.getAttribute('src'));
const latestChapter = await page.$$eval(CSS_SELECTOR_FOR_LATEST_CHAPTER, eps => eps.map(e => e.textContent));
const source = SOURCE_WEBSITE_NAME;

await insertOneToDB(COLLECTION_NAME_TO_ADD, {
    title,
    href,
    img,
    latestChapter,
    source,
});
```

## Any Questions?

Please refer to the following:

### New to Web Scraping?

Some things you should know:

- How to use DevTools in any browser you like

> This is useful to get CSS selectors, find hidden APIs, check if a page is static or dynamic, among other things.

- What tools do you need to get the job done

> Get familiar with the following tools:
>
> For Node.js:
>
> - Cheerio
> - Puppeteer
> - Playwright
>
> For Python:
>
> - Selenium
> - Requests
> - BeautifulSoup

- Useful links

> - [Web Scraping with Node.js (Video)](https://www.youtube.com/watch?v=dXjKh66BR2U)
> - [Web Scraping with Node.js (Article)](https://www.freecodecamp.org/news/the-ultimate-guide-to-web-scraping-with-node-js-daa2027dcd3/)
> - [John Watson Rooney's (Video)](https://www.youtube.com/c/JohnWatsonRooney) *(Python only, but good for concepts and insights)*

### Crawler Framework

- [Crawlee Documentation](https://crawlee.dev/docs/quick-start)
- [Crawlee Discord](https://discord.com/invite/jyEM2PRvMU)
- [Create Amazon Scraper to Learn](https://www.youtube.com/watch?v=yTRHomGg9uQ)

### Crawler Type

#### HTTP Crawler

- [How to use HTTP Crawler](https://crawlee.dev/docs/examples/http-crawler)
  
#### Cheerio Crawler

- [Cheerio Documentation](https://cheerio.js.org/)
- [How to use Cheerio Crawler](https://crawlee.dev/docs/examples/cheerio-crawler)

#### Puppeteer Crawler

- [Puppeteer Documentation](https://devdocs.io/puppeteer/)
- [How to use Puppeteer Crawler](https://crawlee.dev/docs/examples/puppeteer-crawler)

### Anything else

- [Maybe there's a solution here](https://github.com/reanalytics-databoutique/webscraping-open-project)
- [Ask us](https://github.com/yasse-ofc/yasse-crawlers/issues/4)

**I can't emphasize this enough**: if you can't figure out something, don't be afraid to ask. Creating crawlers is a simple, but very tiring work, and it's the core for this project to work. So, any helping hands are appreciated, even if you know almost nothing about the subject.
