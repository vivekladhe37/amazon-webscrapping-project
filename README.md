# Amazon Price Tracker

A simple Python script that monitors product prices on Amazon India and sends notifications when prices drop below your target thresholds.

## How It Works

1. You maintain a list of products in `amazon.csv` with your desired price points.
2. The script scrapes Amazon product pages and offer listing pages to fetch current prices.
3. If a price drops below your threshold, you get a desktop notification (and optionally a message/call alert).
4. All price checks are logged to `amazon.txt` with timestamps.

## Files

| File | Description |
|------|-------------|
| `amazon.py` | Main script that scrapes prices and sends alerts |
| `amazon.csv` | Product watchlist (ASIN, name, steal-deal price, buy-within price) |
| `amazon.txt` | Auto-generated log of price checks (created at runtime) |

## CSV Format

```
ASIN, Product Name, Steal Deal Price, Buy Within Price
```

- **ASIN** – Amazon Standard Identification Number (from the product URL)
- **Product Name** – A label for your reference
- **Steal Deal Price** – If price drops below this, it's flagged as a steal deal
- **Buy Within Price** – Secondary threshold for a "worth buying" alert

Example:
```
B07HZ8JWCL, Sony WH-1000XM3, 17000, 17989
```

## Requirements

- Python 3
- Libraries: `requests`, `beautifulsoup4`, `lxml`, `pync`

Install dependencies:
```bash
pip install requests beautifulsoup4 lxml pync
```

> **Note:** `pync` is macOS-only (uses Notification Center). On other platforms, you'd need to swap it out for something like `plyer` or `win10toast`.

## Usage

```bash
# Run with notifications
python amazon.py show

# Run silently (only steal-deal alerts)
python amazon.py
```

## Features

- Rotates through a list of user-agent strings to reduce chance of being blocked
- Checks both the main product page price and the offer listing page price
- Two-tier alert system (steal deal vs. buy within)
- Logs all results to a text file for historical tracking
- 2-second delay between requests to be polite to the server

## Notes

- Built for Amazon India (`amazon.in`). Change the base URL for other regions.
- The HTML selectors (`priceblock_ourprice`, `olpOfferPrice`) may break if Amazon updates their page layout — this is expected with any scraper.
- The AppleScript/FaceTime call features are macOS-specific and were set up for personal use.
- This was a personal DIY project, so error handling is broad (`try/except` blocks) rather than granular.

## Automating

You can schedule this with cron (macOS/Linux) to run periodically:

```bash
# Every 30 minutes
*/30 * * * * cd /path/to/project && python amazon.py show
```

On Windows, use Task Scheduler to achieve the same.
