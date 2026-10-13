# How to Scrape Pracuj.pl: Polish Job Postings (Salary, Contract Type, Work Mode)

> **Run it now, no code: [pracuj-jobs-scraper on the Apify Store](https://apify.com/bibian/pracuj-jobs-scraper)** &nbsp;|&nbsp; $1.20 per 1,000 job postings &nbsp;|&nbsp; no browser, no login

Pracuj.pl is Poland's largest job board. It has no public export or API for third parties and sits behind Cloudflare. This guide shows how to get its postings as clean structured data: title, company, location, salary, contract type and work mode, with a free Apify Actor, from the console or from code.

Searches this page answers: pracuj.pl scraper, polish job postings scraper, pracuj api, pracuj.pl job data.

## What you get from the Pracuj.pl scraper

- Every open posting, newest first, with title, hiring company and its Pracuj.pl employer page.
- Location, remote flag, salary range when disclosed, contract type, work schedule, work mode and position level.
- Posting and expiration dates.
- Optional full description as plain text.

Export the dataset as JSON, CSV or Excel, or read it through the Apify API. A real sample (five results, personal data removed, long texts truncated) is in [`sample-output.json`](sample-output.json).

## Output fields

| Field | What it is |
|---|---|
| `id`, `url` | Pracuj.pl posting id and public link |
| `title`, `company`, `companyUrl` | Job title, hiring company and its Pracuj.pl employer page |
| `location`, `remote` | Where the job is based, and whether it is remote |
| `salary` | Salary range when the posting discloses one, `null` otherwise |
| `contractTypes`, `workSchedules`, `workModes`, `positionLevels` | Arrays, as shown on the posting |
| `datePosted`, `expirationDate` | ISO dates |
| `description` | Plain text, only when full descriptions are enabled |

## Use cases

- **Benchmark Polish salaries.** Group `salary` by `location` or `positionLevels` to see what a given role pays across Poland.
- **Track hiring demand.** Count postings per city or contract type over time.
- **Build a daily job alert.** Schedule a run every morning and send only the newest postings to Slack, e-mail or a Google Sheet.
- **Feed a niche job board.** Use the structured fields and the canonical `url` to power your own aggregator for the Polish market.

## Input

| Field | What it does |
|---|---|
| `keywords` | Search text applied on pracuj.pl itself, for example `programista` or `księgowa`. Empty means every posting. |
| `maxItems` | The run stops once this many postings are saved (default 50). |
| `includeDescriptions` | Fetch each posting's own page for the full description (one extra request per posting). |

Example input:

```json
{
  "keywords": "programista",
  "maxItems": 50,
  "includeDescriptions": true
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/pracuj-jobs-scraper) and click **Try for free** (a free Apify account is enough).
2. Fill in the input form (keywords and the maximum number of results) and click **Start**.
3. When the run finishes, open the **Output** tab, check the table, then **Export** as CSV, Excel or JSON.
4. For a daily alert, open **Schedules**, create a daily schedule for the Actor and, optionally, add an integration that sends new results to Slack, e-mail or Google Sheets.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/pracuj-jobs-scraper').call({
    "keywords": "programista",
    "maxItems": 50,
    "includeDescriptions": true
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const item of items) {
    console.log(`${item.title} | ${item.company} | ${item.location} | remote: ${item.remote}`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/pracuj-jobs-scraper").call(run_input={
    "keywords": "programista",
    "maxItems": 50,
    "includeDescriptions": True,
})

for item in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{item['title']} | {item.get('company')} | {item.get('location')} | remote: {item.get('remote')}")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above.

## Pricing

Pay per result: **$1.20 per 1,000 job postings** (that is $0.0012 per job posting), plus a tiny start fee of $0.00005 per run. You pay only for the results saved to the dataset. Apify's free plan includes monthly credits, enough to try it on a few hundred results. Prices are those shown on the [Store page](https://apify.com/bibian/pracuj-jobs-scraper) when this guide was written; the Store page is the reference.

## Limits and honest notes

- Pracuj.pl sits behind Cloudflare; the Actor automatically escalates through stealth HTTP tiers so a run stays reliable without ever solving a captcha or logging in.
- Only what Pracuj.pl publishes is collected. Many postings do not disclose a salary, so `salary` is often `null`.
- A posting listed in several cities at once is split into one record per city.
- This is an unofficial tool, not affiliated with Pracuj.pl or Grupa Pracuj. Check the site terms and the data protection rules that apply to your use.

## FAQ

**Does it need a browser or a proxy?**  
No browser. The Actor may use a datacenter proxy when needed to stay reliable through Cloudflare, already included in the price.

**Does it work without `keywords`?**  
Yes. Leave it empty for a productive default run across the newest postings.

**Can I get the data in Excel or Google Sheets?**  
Yes. Export the dataset as CSV, Excel or JSON from the Apify console, or connect the Apify Google Sheets integration.

## Try it

**[Open pracuj-jobs-scraper on the Apify Store](https://apify.com/bibian/pracuj-jobs-scraper)**

The code in `examples/` is released under the MIT license (see [LICENSE](LICENSE)). The data belongs to its original publishers.
