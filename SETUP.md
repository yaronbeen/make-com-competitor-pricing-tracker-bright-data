# Setup Guide

## 1. Import the Blueprint

1. Download `blueprint.json` from this repository
2. Open [Make.com](https://www.make.com/) and go to your dashboard
3. Click **Create a new scenario**
4. Click the **...** menu (bottom of the editor) and select **Import Blueprint**
5. Upload the `blueprint.json` file

## 2. Set Up Bright Data MCP Connection

### What is Bright Data MCP?

Bright Data MCP is a free AI-powered web scraping tool that connects directly to Make.com via the Model Context Protocol. It handles proxies, browser rendering, CAPTCHA solving, and anti-bot bypass automatically.

### Free Tier

- **5,000 free credits** on signup
- **No credit card required**
- Credits cover SERP scraping, webpage scraping, and batch operations

### Setup Steps

1. Sign up at [brightdata.com](https://brightdata.com) (free account)
2. Navigate to **MCP Integrations** in your dashboard
3. Copy your **MCP token**
4. In Make.com, click the **MCP Client** module ("Fetch competitor pricing")
5. Click **Add** next to Connection
6. Configure:
   - **Connection type**: SSE (Server-Sent Events)
   - **Server URL**: `mcp.brightdata.com`
   - **Authentication**: Token (paste your token)
7. Click **Save**

### Available MCP Tools

| Tool                  | Description                                      |
| --------------------- | ------------------------------------------------ |
| `search_engine`       | Scrape SERP results from Google, Bing, or Yandex |
| `scrape_as_markdown`  | Scrape a webpage and return content as Markdown  |
| `search_engine_batch` | Run multiple search queries simultaneously       |
| `scrape_batch`        | Scrape multiple URLs simultaneously              |

## 3. Set Up Google Sheets Connection

1. Click the **Google Sheets** module in the scenario (either "Read rows of old pricing" or "Update price")
2. Click **Add** next to Connection
3. Sign in with your Google account and grant permissions
4. Create a new Google Sheet:
   - Import the provided `google-sheets-template.csv`, **OR**
   - Create a blank sheet with headers: `Plan`, `Price`, `Key features`
5. **Pre-populate the sheet** with the plans you want to track (see example in the CSV template)
6. Update the **Spreadsheet ID** in both Google Sheets modules to point to your new sheet
7. Ensure the sheet name is **Sheet1** (or update both modules to match your sheet name)

> **How to find your Spreadsheet ID**: Open your Google Sheet in a browser. The URL looks like `https://docs.google.com/spreadsheets/d/YOUR_SPREADSHEET_ID/edit`. Copy the long string between `/d/` and `/edit`.

## 4. Configure Input Variables

Click the **Input URL** module and update:

| Variable | What to Set                                                        |
| -------- | ------------------------------------------------------------------ |
| `URL`    | Your competitor's pricing page, e.g. `https://clickup.com/pricing` |

### Important: What Gets Updated

The automation only updates the **Price** column when a change is detected. The **Key Features** column is preserved from your existing sheet data and is not overwritten by the MCP scrape. To update features, edit the sheet manually or modify the blueprint's update module.

### Customizing Tracked Plans

The MCP prompt is configured to extract specific plan names. To track different plans, edit the prompt in the "Fetch competitor pricing" module. The default extracts: Free, Unlimited, Business, Enterprise, Brain AI, Everything AI.

## 5. Test the Scenario

1. Click **Run once** in the Make.com editor
2. Check each module for green checkmarks
3. Open your Google Sheet to verify prices were compared
4. Change a price manually in the sheet, then run again to see the update logic work
5. Verify that only changed rows were updated

## 6. Troubleshooting

| Issue                      | Solution                                                                           |
| -------------------------- | ---------------------------------------------------------------------------------- |
| MCP connection fails       | Verify your Bright Data token is correct and hasn't expired                        |
| No plans extracted         | The MCP prompt specifies exact plan names. Verify they match the target page       |
| All rows say "Not changed" | This is correct if prices haven't changed since last run                           |
| Invalid JSON errors        | The MCP response format may vary. Check the error handler output                   |
| Google Sheets not updating | Verify the spreadsheet ID in BOTH Sheets modules (read and update)                 |
| Can't find error details   | Click the error handler module (pink) after a failed run to see captured variables |
| Wrong row updated          | The filter matches on Plan name (column A). Ensure plan names are exact            |
