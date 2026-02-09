# Import Guide

## Method 1: Import via Make.com UI

1. Download `blueprint.json` from this repository
2. Log in to [Make.com](https://www.make.com/)
3. Click **Scenarios** and then **Create a new scenario**
4. Click the **...** menu at the bottom and select **Import Blueprint**
5. Choose the downloaded `blueprint.json` file

## Method 2: Clone and Import

```bash
git clone https://github.com/yaronbeen/make-com-competitor-pricing-tracker-bright-data.git
cd make-com-competitor-pricing-tracker-bright-data
```

Then import `blueprint.json` via the Make.com UI.

## Post-Import Configuration

### Required Connections

| Connection      | Module                                  | Setup Guide                                              |
| --------------- | --------------------------------------- | -------------------------------------------------------- |
| Bright Data MCP | Fetch competitor pricing                | [SETUP.md](SETUP.md#2-set-up-bright-data-mcp-connection) |
| Google Sheets   | Read rows of old pricing / Update price | [SETUP.md](SETUP.md#3-set-up-google-sheets-connection)   |

### Required Variables

| Variable | Required | Notes                                  |
| -------- | -------- | -------------------------------------- |
| `URL`    | Yes      | Must be a valid competitor pricing URL |

### Google Sheets Setup

Create your output spreadsheet:

- Import `google-sheets-template.csv` into Google Sheets, **OR**
- Create a blank sheet with headers: Plan, Price, Key features

**Important**: Pre-populate the sheet with the plan names you want to track. The automation reads existing rows to compare prices, so it needs baseline data.

Then update the spreadsheet ID in **both** Google Sheets modules (Read and Update).

## Customization Tips

- **Different competitor**: Change the URL and update the MCP prompt with the correct plan names
- **More plans**: Add plan names to the MCP prompt's extraction list
- **Additional fields**: Modify the MCP prompt to extract more data (billing cycle, limits, etc.)
- **Alerts**: Add a Slack/email module after the "Update price" step to notify on price changes
- **History tracking**: Add a second Google Sheet that appends price changes with timestamps
- **Multiple competitors**: Clone the scenario and change the URL for each competitor

## Version History

| Version | Date       | Changes         |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-02-09 | Initial release |
