# Architecture

## Flow Diagram

```
[2] Input URL
 |
 v
[5] Fetch Competitor Pricing (Bright Data MCP) -----> onerror:
 |                                                      [8] MCP Error Info
 v
[9] Parse JSON Results -----> onerror:
 |                              [10] Invalid JSON Error Info
 v
[4] Read Rows of Old Pricing (Google Sheets)
 |
 v
[22] Router: Compare Old Price vs New Price
 |                    |
 v                    v
Route 1              Route 2
(Price Changed)      (Price Unchanged)
 |                    |
 v                    v
[24] Update Price    [23] Log "Price
in Google Sheets     Not Changed"
```

## Module Inventory

| ID  | Type                       | Name                           | Purpose                                              |
| --- | -------------------------- | ------------------------------ | ---------------------------------------------------- |
| 2   | `util:SetVariable2`        | Input URL                      | Set the competitor pricing page URL variable         |
| 5   | `mcp-client:DoAnAction`    | Fetch competitor pricing       | Call Bright Data MCP to scrape and extract pricing   |
| 8   | `util:SetVariables`        | MCP error info                 | Capture MCP error source and message (error handler) |
| 9   | `json:ParseJSON`           | Convert MCP result into JSON   | Parse MCP response into structured data              |
| 10  | `util:SetVariables`        | Invalid JSON error info        | Capture JSON parse error details (error handler)     |
| 4   | `google-sheets:filterRows` | Read rows of old pricing       | Fetch existing pricing records from Google Sheets    |
| 22  | `builtin:BasicRouter`      | Compare old price vs new price | Route based on whether price has changed             |
| 24  | `google-sheets:updateRow`  | Update price                   | Update the row in Google Sheets with new price       |
| 23  | `util:SetVariable2`        | Price not changed              | Log that price is unchanged (fallback route)         |

## Module Details

### [2] Input URL

Sets a single variable `URL` containing the competitor's pricing page URL. Uses roundtrip scope. Default: `https://clickup.com/pricing`.

### [5] Fetch Competitor Pricing (MCP)

Calls Bright Data MCP with a prompt instructing it to scrape the given URL and extract specific plans. The prompt requests only these plans: Free, Unlimited, Business, Enterprise, Brain AI, Everything AI. Each plan returns: `name` (string), `price` (string), `features` (string array). The response must be a valid JSON array with no extra text.

### [9] Parse JSON Results

Converts the MCP string response into structured data with fields: name, price, features. The parsed data is iterated per plan.

### [4] Read Rows of Old Pricing

Reads existing rows from Google Sheets, filtering where column A (Plan) matches the current plan name from the parsed JSON. This retrieves the stored price for comparison.

### [22] Compare Old Price vs New Price Router

Two-way router with fallback:

- **Route 1**: Filter `old price (column B) != new price` -- price has changed, proceed to update
- **Route 2** (fallback): Price is the same -- log "Not changed" and skip

### [24] Update Price

Updates the matched row in Google Sheets with the new price. Preserves the plan name (column A) and key features (column C), updating only the price (column B).

### [23] Price Not Changed

Sets a variable `Price = "Not changed"` for logging/debugging purposes.

## Error Handling

1. **MCP Error**: Module [5] onerror --> [8] Captures error_source="MCP" and error_message
2. **JSON Parse Error**: Module [9] onerror --> [10] Captures error_source="Parse JSON" and error_message

## Router Logic

| Route | Filter Condition               | Action                         |
| ----- | ------------------------------ | ------------------------------ |
| 1     | Old price (col B) != new price | Update row with new price      |
| 2     | Fallback (price unchanged)     | Log "Not changed", skip update |

## Data Flow

```
URL variable
    |
    v
MCP scrapes pricing page
    |
    v
JSON array of plans [{name, price, features}, ...]
    |
    v (iterates per plan)
Google Sheets lookup: find row where Plan == name
    |
    v
Compare: stored_price vs fetched_price
    |
    +-- Different --> Update row (Plan, new Price, Features)
    +-- Same ------> Skip
```
