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

## Documentation

- [SETUP.md](SETUP.md) -- Step-by-step setup guide
- [ARCHITECTURE.md](ARCHITECTURE.md) -- Technical deep-dive
- [IMPORT-GUIDE.md](IMPORT-GUIDE.md) -- How to import and customize

## License

MIT
