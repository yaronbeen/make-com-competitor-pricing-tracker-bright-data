# Competitor Pricing Tracker with Bright Data MCP

[![Make.com](https://img.shields.io/badge/Make.com-6D00CC?style=flat&logo=make&logoColor=white)](https://www.make.com/)
[![Bright Data MCP](https://img.shields.io/badge/Bright%20Data-MCP-00D4AA?style=flat)](https://brightdata.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A Make.com automation that monitors competitor pricing pages using Bright Data MCP, compares prices against existing records in Google Sheets, and updates only the rows where a price change is detected.

## What It Does

- Scrapes a competitor's pricing page (e.g., ClickUp) using Bright Data MCP
- Extracts structured plan data: name, price, and key features
- Reads existing pricing records from Google Sheets for comparison
- Detects price changes by comparing old vs. new prices per plan
- Updates only the rows where the price has actually changed
- Skips plans with unchanged prices to minimize unnecessary writes

## How It Works

```
Input URL --> Bright Data MCP Fetch --> Parse JSON --> Read Old Prices from Sheets --> Router:
    |-- Price Changed --> Update Row in Google Sheets
    +-- Price Unchanged --> Skip
```

You provide a competitor's pricing page URL (e.g., `https://clickup.com/pricing`). The Bright Data MCP scrapes the page and extracts structured pricing data for specific plans. The automation then reads existing prices from Google Sheets, compares them, and updates only the rows where a price difference is detected.

## Input Variables

| Variable | Default                       | Description                 |
| -------- | ----------------------------- | --------------------------- |
| `URL`    | `https://clickup.com/pricing` | Competitor pricing page URL |

## Output Schema

| Column       | Type | Description                           |
| ------------ | ---- | ------------------------------------- |
| Plan         | text | Plan name (e.g., Free, Business)      |
| Price        | text | Current price for the plan            |
| Key Features | text | Comma-separated list of plan features |

## Prerequisites

- [Make.com](https://www.make.com/) account (free or paid)
- [Bright Data](https://brightdata.com/) account with MCP access (free tier: 5,000 credits, no credit card required)
- Google account (for Google Sheets output)

## Quick Start

1. **Import** the `blueprint.json` into Make.com ([IMPORT-GUIDE.md](IMPORT-GUIDE.md))
2. **Connect** Bright Data MCP ([SETUP.md](SETUP.md))
3. **Create** a Google Sheet using the [CSV template](google-sheets-template.csv)
4. **Pre-populate** the sheet with the plans you want to track
5. **Run** the scenario

## Error Handling

- **MCP errors**: If the Bright Data MCP call fails, error source and message are captured for debugging
- **Invalid JSON**: If the MCP response can't be parsed as JSON, the error is logged separately
- **No price change**: Plans where the price hasn't changed are silently skipped

## Make It Your Own

This blueprint is a **flexible building block**, not a rigid template. Every part of it -- the prompts, the data structure, the comparison logic, and the output -- is designed to be adjusted. Use it as-is for pricing tracking, or reshape it into any "scrape, compare, and update" workflow.

### Adjust the MCP Prompt

The MCP prompt is where the magic happens. It tells Bright Data what to scrape and how to structure the response. You can rewrite it to extract completely different data:

- **Track different plans**: Change the plan names in the prompt to match any SaaS pricing page
- **Extract more fields**: Add `billing_cycle`, `user_limits`, `storage`, `support_tier`, or `trial_duration`
- **Track multiple competitors**: Duplicate the scenario with different URLs for each competitor
- **Change the structure**: Request a different JSON format to match your tracking needs
- **Add comparison logic**: Tell the MCP to "highlight plans that cost more than $50/month" or "flag plans without a free tier"

### Swap the Data Source

The input doesn't have to be a SaaS pricing page. You can point this at:

- **E-commerce product pages** to track product prices (Amazon, Shopify stores)
- **Subscription boxes** to compare monthly pricing across services
- **API pricing pages** to monitor changes in usage-based pricing
- **A webhook** that receives URLs from another system
- **A Google Sheets feeder** that loops through multiple competitor URLs

### Change the Output

Google Sheets is just one option. Replace the output module with:

- **Airtable** for a richer database with views, filters, and linked records
- **Notion** for a team-accessible competitive intelligence dashboard
- **Slack/Email** for instant alerts when a competitor changes their pricing
- **A webhook** to feed price changes into your CRM, billing system, or internal tools
- **A database** (MySQL, PostgreSQL via Make.com connectors) for long-term historical tracking

### Extend the Logic

The compare-and-update pattern is a powerful building block you can build on:

- **Price history**: Add a second Sheet or table that appends every price change with timestamps
- **Change alerts**: Add a Slack/email notification that fires only when a price drops or increases
- **Feature diff**: Compare feature lists too, not just prices, and flag when features are added/removed
- **Multi-competitor dashboard**: Run the scenario for multiple competitors and aggregate into a single comparison view
- **Trend analysis**: Use the historical data to chart pricing trends over time
- **Schedule runs**: Set the scenario to run daily or weekly for continuous monitoring

### Example: Turn This Into a Product Price Tracker

1. Change the URL to an e-commerce product page
2. Adjust the MCP prompt to extract: `product_name`, `price`, `availability`, `rating`, `review_count`
3. Update the Google Sheet columns to match
4. Add a Slack notification for when prices drop below a threshold

The blueprint handles the hard parts (MCP connection, JSON parsing, Sheets comparison, conditional updates, error handling). You just change what goes in, what gets compared, and where the results go.

## Documentation

- [SETUP.md](SETUP.md) -- Step-by-step setup guide
- [ARCHITECTURE.md](ARCHITECTURE.md) -- Technical deep-dive
- [IMPORT-GUIDE.md](IMPORT-GUIDE.md) -- How to import and customize

## License

MIT
