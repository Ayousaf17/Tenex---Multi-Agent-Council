# Transaction Orchestrator Playbook

## Overview

**Purpose:** Central hub monitoring all pipelines. Provides unified visibility across all four agents while they operate independently. Handles cross-agent triggers and exception escalation.

**Role:** Like the TENEX Project Manager (PM) - coordinates workflow and delegates to specialists.

## Workflow Diagram

```
CROSS-AGENT TRIGGERS:

Lead Converted to Transaction:
+-------------------+
| Lead Converted    |
| Event             |
+---------+---------+
          |
          v
+-------------------+
| Extract Lead Data |
+---------+---------+
          |
    +-----+-----+
    |           |
    v           v
[Create         [Update Lead
Transaction]    Status]
    |
    v
+-------------------+
| Trigger TC Agent  |
| /webhook/         |
| new-transaction   |
+-------------------+

Transaction Closed:
+-------------------+
| Transaction       |
| Closed Event      |
+---------+---------+
          |
          v
+-------------------+
| Extract Tx Data   |
+---------+---------+
          |
    +-----+-----+
    |           |
    v           v
[Create         [Trigger
Client]         Marketing
    |           Just Sold]
    v
+-------------------+
| Trigger CS Agent  |
| /webhook/         |
| new-client        |
+-------------------+

DAILY DASHBOARD (7am):
+-------------------+
| Get All Pipelines |
+---------+---------+
          |
    +-----+-----+-----+-----+
    |     |     |     |     |
    v     v     v     v     v
[Leads] [Txs] [Clients] [Listings] [Tasks]
    |     |     |     |     |
    +-----+-----+-----+-----+
          |
          v
+-------------------+
| Build Dashboard   |
| Summary           |
+---------+---------+
          |
          v
+-------------------+
| Send Daily        |
| Dashboard Email   |
+-------------------+

WEEKLY PERFORMANCE (Friday 4pm):
+-------------------+
| Aggregate Metrics |
+---------+---------+
          |
          v
+-------------------+
| Generate          |
| Performance       |
| Report            |
+-------------------+
```

## Setup Steps

### 1. Import Workflow
- Import `re_transaction_orchestrator.json` into n8n
- Activate after all agent workflows are active

### 2. Configure Agent Endpoints
Update the HTTP request nodes with your n8n instance URLs:
```
Lead Nurturing: https://your-n8n.com/webhook/new-lead
Transaction Coord: https://your-n8n.com/webhook/new-transaction
Client Success: https://your-n8n.com/webhook/new-client
Marketing: https://your-n8n.com/webhook/new-listing
Marketing: https://your-n8n.com/webhook/just-sold
```

### 3. Configure Credentials
- `GOOGLE_SHEETS_CREDENTIAL_ID` - Access all agent sheets
- `GMAIL_CREDENTIAL_ID` - Dashboard emails
- `OPENROUTER_CREDENTIAL_ID` - Report generation

### 4. Link All Sheets
Update sheet IDs for cross-agent visibility:
- `LEADS_SHEET_ID`
- `TRANSACTIONS_SHEET_ID`
- `CLIENTS_SHEET_ID`
- `LISTINGS_SHEET_ID`
- `CONTENT_SHEET_ID`

## Cross-Agent Event Triggers

### Lead → Transaction Handoff
**Trigger:** Lead converts to signed contract

**Webhook:**
```bash
curl -X POST https://your-n8n.com/webhook/orchestrator/lead-converted \
  -H "Content-Type: application/json" \
  -d '{
    "lead_id": "L-123",
    "lead_name": "John Smith",
    "lead_email": "john@email.com",
    "lead_phone": "+15551234567",
    "property_address": "123 Main St",
    "contract_date": "2024-01-15",
    "option_period_end": "2024-01-25",
    "financing_deadline": "2024-02-05",
    "closing_date": "2024-02-15",
    "purchase_price": 450000
  }'
```

**Actions:**
1. Update lead status to "Converted"
2. Create transaction in TC sheet
3. Trigger TC new-transaction webhook
4. Log handoff in orchestrator log

### Transaction → Client Success Handoff
**Trigger:** Transaction status = "Closed"

**Webhook:**
```bash
curl -X POST https://your-n8n.com/webhook/orchestrator/transaction-closed \
  -H "Content-Type: application/json" \
  -d '{
    "transaction_id": "TX-123",
    "client_name": "John Smith",
    "client_email": "john@email.com",
    "client_phone": "+15551234567",
    "property_address": "123 Main St",
    "close_date": "2024-02-15",
    "sale_price": 445000,
    "type": "buyer"
  }'
```

**Actions:**
1. Update transaction status to "Closed"
2. Create client in CS sheet
3. Trigger CS new-client webhook
4. Trigger Marketing just-sold webhook
5. Log handoff

### New Listing → Marketing Trigger
**Trigger:** New listing added to system

**Webhook:**
```bash
curl -X POST https://your-n8n.com/webhook/orchestrator/new-listing \
  -H "Content-Type: application/json" \
  -d '{
    "listing_id": "MLS-123",
    "address": "456 Oak Ave",
    "price": 525000,
    "bedrooms": 3,
    "bathrooms": 2,
    "sqft": 2200,
    "description": "Beautiful updated home...",
    "photo_urls": ["url1", "url2"]
  }'
```

**Actions:**
1. Create listing record
2. Trigger Marketing new-listing webhook
3. Log listing addition

## Daily Dashboard

### Dashboard Contents

```
===========================================
AASANI REAL ESTATE DASHBOARD - [DATE]
===========================================

📊 PIPELINE OVERVIEW
-------------------
Active Leads: 45 (+3 this week)
Active Transactions: 8 (-1 closed)
Past Clients: 127 (+1 this week)
Active Listings: 5 (2 under contract)

🚨 ATTENTION NEEDED
-------------------
❗ 2 overdue tasks (Transaction Coordinator)
⚠️ 5 leads cold >30 days (Lead Nurturing)
📝 3 review requests pending (Client Success)

📈 THIS WEEK'S WINS
-------------------
✅ 1 Transaction Closed: 123 Main St
✅ 3 New Leads Captured
✅ 2 Reviews Received
✅ 15 Sphere Touches Sent

📅 UPCOMING
-------------------
• 2 Closings in next 7 days
• 3 Option periods ending
• 5 Financing deadlines

💰 PIPELINE VALUE
-------------------
Active Transactions: $3.2M
Pending Leads: $8.5M potential
```

### Send To
- Primary agent email
- Team leader (if applicable)
- Assistant (optional)

## Weekly Performance Report

### Report Contents

```
===========================================
WEEKLY PERFORMANCE REPORT
Week of [DATE]
===========================================

LEAD NURTURING
--------------
New Leads: 12
Response Time (avg): 3.2 minutes ✅
Email Open Rate: 42%
Leads Converted: 2 (16.7%)

TRANSACTIONS
------------
New Transactions: 2
Closed This Week: 1
On-Time Task Rate: 94%
Overdue Tasks: 3

CLIENT SUCCESS
--------------
New Clients Added: 1
Reviews Requested: 4
Reviews Received: 2 (50%)
Sphere Touches: 15

MARKETING
---------
Posts Published: 12
Total Reach: 4,500
Engagement Rate: 3.2%
New Listing Views: 890

OVERALL
-------
Lead-to-Close Rate: 8%
Avg Days to Close: 32
Client Satisfaction: N/A
Agent Hours Saved: ~28 hrs
```

## Exception Handling

### Critical Alert Escalation
When any agent detects critical issues:
1. Agent sends to orchestrator endpoint
2. Orchestrator immediately notifies agent via SMS
3. Logs exception with timestamp
4. Flags for weekly review

### Common Exceptions
| Exception | Source | Action |
|-----------|--------|--------|
| Lead unresponsive 7+ days | Lead Nurturing | Escalate to personal call |
| Task overdue 48+ hours | Transaction Coord | SMS alert |
| Closing at risk | Transaction Coord | All-party notification |
| Negative review | Client Success | Personal outreach |

## Integration Points

### Inbound
- All agent webhooks for handoffs
- Manual override endpoint
- External CRM sync

### Outbound
- Agent webhook triggers
- Dashboard email
- Performance report email
- SMS escalations

## Success Metrics

| Metric | Target | How to Track |
|--------|--------|--------------|
| Handoff completion | 100% | Cross-check logs |
| Dashboard delivery | Daily | Email logs |
| Exception response | < 1 hour | Timestamp diff |
| Data consistency | 100% | Cross-sheet audit |

## Troubleshooting

### Handoffs Failing
1. Check agent webhooks are active
2. Verify data format matches expectations
3. Check network connectivity
4. Review error logs

### Dashboard Missing Data
1. Verify all sheet IDs correct
2. Check sheet permissions
3. Review data aggregation code
4. Ensure consistent column names

### Duplicate Records
1. Add deduplication logic
2. Check unique ID generation
3. Review handoff triggers for double-firing
