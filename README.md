# Books Scraper — books.toscrape.com

Scrapes the first 100 books (pages 1–5) from https://books.toscrape.com,
loads them into SQLite, and answers a few analysis questions about price,
rating, and stock status.

## Files

- `scrape.py` (or notebook) — scrapes the site and produces `books.csv`
- `books.csv` — 100 books with columns: title, price, rating, in_stock, url
- `queries.sql` — the three analysis queries
- SQL loading script — loads `books.csv` into a local SQLite database
  (`books.db`) and runs `queries.sql`

## books.csv columns

- `title` — book title
- `price` — numeric price (e.g. `51.77`)
- `rating` — integer 1–5
- `in_stock` — `true` / `false`
- `url` — link to the book's page

## Usage

1. Run the scraper to produce `books.csv`
2. Run the SQL loading script to build `books.db` from the CSV and execute
   the three queries in `queries.sql`

## Part 3 — Five lines

- My first version of `BASE_URL` pointed at the site's homepage instead of
  `catalogue/page-{}.html`, so all 5 "pages" fetched the same content
  instead of paginating — I caught it because the CSV didn't have close to
  100 unique titles, and fixed the URL template before re-running.
- The rating isn't plain text — it's encoded as a CSS class (e.g.
  `star-rating Three`) — so parsing that correctly took more care than the
  request/response logic itself.
- Book links on the listing pages are relative (`../../../...`), so building
  correct absolute URLs took a bit of trial and error.
- Stock status required checking substring text ("In stock (x available)")
  rather than reading a clean boolean field.
- If the site blocked me after ~50 requests, I'd add randomized delays
  (1–3s) between requests, set a realistic User-Agent, reuse a
  `requests.Session()` for connection pooling, and add retry-with-backoff on
  429/503 responses — and rotate IPs/proxies or slow the crawl further if
  blocking persisted.
