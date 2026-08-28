# rileys

Personal financial reader with macro mechanism spine. Static GH Pages — `index.html` reads JSON cold from `data/`.

## Architecture

```
You upload statement in Claude project (PII stays there)
  → Claude extracts: rate, balance, P&I split, APY, spending
  → Claude writes the monthly mechanism note
  → Claude gives you a Claude Code prompt with sanitized data only
  → Claude Code writes JSON into data/
  → GH Pages renders the compounding time series
```

No collector. No cron. No secrets. No API calls. No raw statements in git — ever.

## Data Flow

The Claude project is the PII firewall. Raw bank and mortgage statements are uploaded there, never here. What enters this repo is sanitized extracts: rates, balances, category totals, and interpretive prose. No account numbers, no lender identifiers, no transaction-level detail.

## Data Schema

### Manifest

`data/index.json` — array of available months:

```json
["2026-08", "2026-09", "2026-10"]
```

### Monthly Snapshot

`data/YYYY-MM.json`:

```json
{
  "month": "2026-08",
  "snapshot_date": "2026-08-28",
  "savings": {
    "apy": 4.50,
    "balance": 12500
  },
  "mortgage": {
    "rate": 6.25,
    "principal": 185000,
    "monthly_pi": 1450,
    "principal_portion": 380,
    "interest_portion": 1070,
    "escrow": 280
  },
  "macro_context": {
    "fed_funds": 5.25,
    "treasury_1y": 4.20,
    "treasury_10y": 3.85,
    "mortgage_30y_market": 6.50,
    "cpi_yoy": 2.80,
    "h8_summary": "Deposit costs rising, credit tightening"
  },
  "spending": {
    "total": 5200,
    "categories": [
      {"name": "Housing", "amount": 1730},
      {"name": "Groceries", "amount": 650},
      {"name": "Dining", "amount": 320},
      {"name": "Transportation", "amount": 280},
      {"name": "Utilities", "amount": 210},
      {"name": "Subscriptions", "amount": 95},
      {"name": "Insurance", "amount": 180},
      {"name": "Healthcare", "amount": 45},
      {"name": "Personal", "amount": 120},
      {"name": "Entertainment", "amount": 85},
      {"name": "Savings & Investments", "amount": 1000},
      {"name": "Other", "amount": 385}
    ]
  },
  "reads": {
    "savings": "Your APY sits above the 1Y Treasury benchmark...",
    "mortgage": "Your fixed rate is locked below current market...",
    "spending": "Groceries is your top discretionary category...",
    "macro_connection": "This month's H.8 showed deposit cost pressure...",
    "monthly_note": "Full monthly mechanism read connecting your household to the macro environment."
  }
}
```

### Spending Categories (Fixed Taxonomy)

Must use these exact names for month-over-month trending:

- Housing (mortgage P&I + escrow)
- Groceries
- Dining
- Transportation
- Utilities
- Subscriptions
- Insurance
- Healthcare
- Personal
- Entertainment
- Savings & Investments
- Other

### Read Fields

Every card carries an instructional read explaining the mechanism:

- `reads.savings` — APY positioning, deposit pricing power, rate trajectory
- `reads.mortgage` — Fixed rate relative value, refi math, 10Y trajectory
- `reads.spending` — Top categories, trends, notable changes, flags
- `reads.macro_connection` — How macro moved (or didn't) in your statements
- `reads.monthly_note` — Full prose mechanism read for the month

## Sections

| Section | What It Shows |
|---|---|
| Your Position | Savings APY/balance, mortgage rate/principal/P&I, instructional reads |
| Spending | Categorized breakdown, month-over-month deltas, synthesis |
| Macro Context | Fed funds, 1Y, 10Y, 30Y mkt, CPI, H.8 summary |
| Monthly Read | Full mechanism note connecting household to macro |
| Trends | APY vs 1Y Treasury, principal paydown (requires 2+ months) |
| Archive | Past monthly notes, clickable |

## Guardrails

- **No PII in this repo.** No account numbers, no lender names, no raw statements, no transaction-level detail. Ever.
- **Research side of the wall.** Same classification as EPOCH and Dashboard. Zero trading system contact.
- **Manual data entry only.** Every data point enters via a Claude Code prompt. No automation, no API calls.
- **Fixed spending taxonomy.** Use the exact category names above so trending works across months.
