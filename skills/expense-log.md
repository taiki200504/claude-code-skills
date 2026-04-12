---
name: expense-log
description: Log expenses in natural language, parse into structured data, and append to a CSV or JSON ledger file
---

# Expense Log

Describe an expense in natural language and have it parsed into structured data — date, amount, category, description, tax-deductible flag — then appended to a persistent CSV or JSON ledger file. Supports Japanese yen and USD.

## When to Use

- Logging a business expense right after it happens
- Batch-entering receipts at the end of the day
- Tracking project costs for invoicing or tax preparation
- Quick expense capture without opening a spreadsheet app

## How It Works

### Step 1: Parse Natural Language Input

Accept expense descriptions in natural or casual language:

```
Lunch meeting with client, Y3,500, Shibuya
AWS bill $127.50
Taxi to Akihabara office 2,800 yen
Coffee x2 for team meeting 960
Domain renewal gugenlab.com $14/year
```

Extract these fields from the input:

| Field | Required | Default |
|-------|----------|---------|
| Date | No | Today's date (YYYY-MM-DD) |
| Amount | Yes | (must be present) |
| Currency | No | JPY if yen/Y/円, USD if $/dollars, else JPY |
| Category | No | Auto-classify (see categories below) |
| Description | Yes | Cleaned version of input |
| Tax-deductible | No | Auto-determine based on category |
| Payment method | No | "unspecified" |

### Step 2: Auto-Classify Category

Map expenses to standard categories:

| Category | Keywords / Signals |
|----------|--------------------|
| Meals & Entertainment | lunch, dinner, coffee, restaurant, cafe, 飲食 |
| Transportation | taxi, train, uber, flight, 交通, Suica |
| Software & SaaS | AWS, subscription, domain, API, license, monthly |
| Office & Supplies | stationery, equipment, desk, chair, 備品 |
| Communication | phone, internet, mobile, SIM |
| Professional Services | lawyer, accountant, consultant, design, 士業 |
| Marketing | ads, advertising, event, sponsorship, print |
| Travel | hotel, accommodation, airfare, 出張 |
| Miscellaneous | anything that doesn't match above |

Tax-deductible rules (Japan, simplified):
- Business meals (with client/partner): deductible (50% for entertainment, 100% for meetings)
- Transportation for business: deductible
- Software/SaaS for business: deductible
- Personal meals: not deductible

### Step 3: Confirm Parsed Data

Before saving, display the parsed result:

```markdown
## Parsed Expense

| Field | Value |
|-------|-------|
| Date | 2026-04-11 |
| Amount | 3,500 |
| Currency | JPY |
| Category | Meals & Entertainment |
| Description | Lunch meeting with client in Shibuya |
| Tax-deductible | Yes (business meeting) |
| Payment method | unspecified |

Save to expenses.csv? (yes / edit / cancel)
```

### Step 4: Append to Ledger File

**CSV** (default: `expenses.csv`): Create with headers if missing, then append. Headers: `date,amount,currency,category,description,tax_deductible,payment_method`

**JSON** (if `expenses.json` exists or user prefers): Append as JSON object to array.

After saving, show a running monthly summary: total amount, top category, tax-deductible percentage.

### Batch Mode

Accept multiple expenses at once (e.g., "Log these: taxi 1,200 / lunch 4,500 / AWS $89.99"). Parse all, display batch for confirmation, save all at once.

### Error Handling

- If no amount is found in the input, ask the user to specify
- If the amount is ambiguous (e.g., "3500" without currency context), default to JPY and note it
- If the ledger file exists but has different columns, warn before appending
- Never overwrite existing entries — always append
- If the file path has permission issues, suggest an alternative location
- Validate that amounts are positive numbers
- For currency conversion, note the exchange rate is not included — amounts are stored as-is

## Example

**User input:**
```
SoftBank meeting後のタクシー 2,400円、あとAWS 4月分 $156.78
```

**Output:**
```markdown
## Parsed Expenses (2 items)

| # | Date | Amount | Currency | Category | Description | Deductible |
|---|------|--------|----------|----------|-------------|------------|
| 1 | 2026-04-11 | 2,400 | JPY | Transportation | Taxi after SoftBank meeting | Yes |
| 2 | 2026-04-11 | 156.78 | USD | Software & SaaS | AWS April billing | Yes |

Save both to expenses.csv? (yes / edit / cancel)
```

After confirmation:
```
Saved 2 entries to expenses.csv.
April totals: Y52,600 + $156.78 across 14 entries.
```
