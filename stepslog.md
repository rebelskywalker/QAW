## Installed linux subsytem on windows using wsl 
- followed instructions on command prompt including restart
## Running ubuntu linkux
- Was stuck in an install loop, this should take only a few minutes.
- afer waiting for about 20 minutes, i used ctrl+c to breal the loop per a forum reccommendation
- Noticed that i was then able to create my username, and then password (had to enter twice after username to bring up password prompt , it took an extra CRLF)
## Used Curl to download nodejs from https://deb.nodesource.com/setup_18.x | sudo -E bash -
- entered the command above
- then followed with sudo apt install -y nodejs
## Checking installed using which to returnf irst found directory contianing those files, could also use program file name with -v option to check version
- which npm
- which nodejs
## nodejs -v
- Returned v18.20.4
## 
{
  "name": "",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {},
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "playwright": "^1.39.0"
  },
  "devDependencies": {
    "@playwright/test": "^1.39.0",
    "@types/node": "^20.8.9"
  }
}
const { chromium } = require("playwright");

function parseRelativeTime(relativeTime) {
  const now = new Date();
  const [amount, unit] = relativeTime.split(' ');

  if (unit.includes('minute')) {
    now.setMinutes(now.getMinutes() - parseInt(amount));
  } else if (unit.includes('hour')) {
    now.setHours(now.getHours() - parseInt(amount));
  } else if (unit.includes('day')) {
    now.setDate(now.getDate() - parseInt(amount));
  }

  return now;
}

async function sortHackerNewsArticles() {
  let browser;

  try {
    // Launch browser
    browser = await chromium.launch({ headless: false });
    const context = await browser.newContext();
    const page = await context.newPage();

    // Go to Hacker News "newest" page
    await page.goto("https://news.ycombinator.com/newest", { waitUntil: 'domcontentloaded' });

    // Extract article data
    const articles = await page.evaluate(() => {
      const items = Array.from(document.querySelectorAll('.athing'));
      return items.map((item, index) => {
        const titleElement     = item.querySelector('.titlelink');
        const title            = titleElement ? titleElement.textContent : 'No title';
        const link             = titleElement ? titleElement.href : 'No link';
        const timestampElement = item.nextElementSibling.querySelector('.age');
        const timestamp        = timestampElement ? timestampElement.textContent : 'No timestamp';
        const scoreElement     = item.nextElementSibling.querySelector('.score');
        const score            = scoreElement ? scoreElement.textContent : '0 points';

        return { title, link, timestamp, score, order: index };
      });
    });

    // Print extracted articles
    articles.forEach(article => {
      console.log(`Title: ${article.title}`);
      console.log(`Link: ${article.link}`);
      console.log(`Timestamp: ${article.timestamp}`);
      console.log(`Score: ${article.score}`);
      console.log(`Order: ${article.order}`);
      console.log('------------------------------');
    });

    // Parse timestamps and validate sorting
    const parsedArticles = articles.map(article => ({
      ...article,
      parsedTime: parseRelativeTime(article.timestamp)
    }));

    const isSorted = parsedArticles.every((article, index, array) => {
      if (index === 0) return true;

      const prev = array[index - 1];
      if (prev.parsedTime > article.parsedTime) return false;
      if (prev.parsedTime.getTime() === article.parsedTime.getTime()) {
        return prev.order < article.order; // Tiebreaker by order
      }

      return true;
    });

    if (isSorted) {
      console.log("Articles are sorted from newest to oldest.");
    } else {
      console.log("Articles are NOT sorted from newest to oldest.");
    }

  } catch (error) {
    console.error("An error occurred:", error.message);
  } finally {
    if (browser) {
      await browser.close();
    }
  }
}

(async () => {
  await sortHackerNewsArticles();
})();
