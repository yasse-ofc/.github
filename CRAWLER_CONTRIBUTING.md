# How to create a crawler <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->

- [How to set up your environment](#how-to-set-up-your-environment)
- [Find out which crawler type to use](#find-out-which-crawler-type-to-use)
  - [Best-case scenario](#best-case-scenario)
  - [Most common scenario](#most-common-scenario)
  - [Worst-case scenario](#worst-case-scenario)
- [Crawler Creation](#crawler-creation)
  - [How to create an HTTP Crawler](#how-to-create-an-http-crawler)
  - [How to create Cheerio Crawler](#how-to-create-cheerio-crawler)
  - [How to create a Puppeteer Crawler](#how-to-create-a-puppeteer-crawler)
- [Any Questions?](#any-questions)
  - [New to Web Scraping?](#new-to-web-scraping)
  - [Crawler Framework](#crawler-framework)
  - [Crawler Type](#crawler-type)
    - [HTTP Crawler](#http-crawler)
    - [Cheerio Crawler](#cheerio-crawler)
    - [Puppeteer Crawler](#puppeteer-crawler)
  - [Anything else](#anything-else)

## How to set up your environment

[Described here]('./../CONTRIBUTING.md#yasse-crawlers').

## Find out which crawler type to use

Follow this order in figuring out which crawler to use because it's sorted by resource usage.

### Best-case scenario

- If you check the `Network` tab in the DevTools, and there are some `json` files, check their response. If the data you want to gather is already there, you have hit the jackpot.

Use an **[HTTP Crawler](#how-to-create-an-http-crawler)**.

### Most common scenario

- Are the only things the user can interact with within the site search related?

Use **[Cheerio Crawler](#how-to-create-cheerio-crawler)**.

### Worst-case scenario

- Does the site have a login?
- When you go to the next page, there's no update to the site's URL?
- Were you blocked when using Cheerio Crawler previously?

Use a **[Puppeteer Crawler](#how-to-create-a-puppeteer-crawler)**.

## Crawler Creation

### How to create an HTTP Crawler

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

### How to create a Puppeteer Crawler

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

> This is useful to get CSS selectors, find hidden APIs, and check if a page is static or dynamic, among other things.

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

**I can't emphasize this enough**: if you can't figure out something, don't be afraid to ask. Creating crawlers is simple but very tiring work, and it's the core of this project to work. So, any helping hands are appreciated, even if you know almost nothing about the subject.
