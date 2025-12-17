# Transaction Coordinator Agent Playbook

## Overview

**Purpose:** Manage the 30 administrative hours per transaction. Track deadlines, coordinate documents, and ensure nothing falls through the cracks from contract to close.

**Key Metric:** Agents using transaction coordination report 25% productivity improvement and effective hourly rates jumping from $200 to $765.

## Workflow Diagram

```
NEW TRANSACTION:
+-------------------+
| New Transaction   |
| Webhook           |
+---------+---------+
          |
          v
+-------------------+
| Normalize Data    |
| (dates, parties)  |
+---------+---------+
          |
          v
+-------------------+
| Create Record     |
| in Sheet          |
+---------+---------+
          |
          v
+-------------------+
| AI Generate       |
| Task Checklist    |
+---------+---------+
          |
          v
+-------------------+
| Save Tasks        |
| to Sheet          |
+---------+---------+
          |
          v
+-------------------+
| Send Welcome      |
| Email to Client   |
+-------------------+

DEADLINE MONITORING (Every 6 hours):
+-------------------+
| Get All Tasks     |
+---------+---------+
          |
          v
+-------------------+
| Filter Alerts     |
| (48hr / Overdue)  |
+---------+---------+
          |
    +-----+-----+
    |           |
    v           v
[Critical]  [Due Soon]
    |           |
    v           v
[SMS Alert] [Email Alert]

DAILY SUMMARY (8am):
+-------------------+
| Get Active Txs    |
+---------+---------+
          |
          v
+-------------------+
| Build Dashboard   |
| Email             |
+-------------------+
```

## Setup Steps

### 1. Import Workflow
- Import `re_transaction_coordinator_agent.json` into n8n
- Activate the workflow

### 2. Create Google Sheets

**Transactions Sheet:**
```
Transaction ID | Property Address | Client Name | Client Email | Client Phone |
Type | Contract Date | Option Period End | Financing Deadline | Closing Date |
Purchase Price | Title Company | Lender | Lender Contact | Status | Created At
```

**Tasks Sheet:**
```
Transaction ID | Task ID | Task | Due Date | Priority | Category | Status |
Created At | Completed At | Notes
```

### 3. Configure Credentials
Update these credential IDs:
- `OPENROUTER_CREDENTIAL_ID`
- `GMAIL_CREDENTIAL_ID`
- `TWILIO_CREDENTIAL_ID`
- `GOOGLE_SHEETS_CREDENTIAL_ID`

### 4. Update Variables
- `YOUR_AGENT_NAME`
- `YOUR_AGENT_EMAIL`
- `YOUR_AGENT_PHONE`
- `YOUR_TRANSACTIONS_SHEET_ID`
- Twilio account details

### 5. Test Webhook
```bash
curl -X POST https://your-n8n.com/webhook/new-transaction \
  -H "Content-Type: application/json" \
  -d '{
    "property_address": "123 Oak Lane, Austin TX 78701",
    "client_name": "Jane Smith",
    "client_email": "jane@email.com",
    "client_phone": "+15551234567",
    "type": "buyer",
    "contract_date": "2024-01-15",
    "option_period_end": "2024-01-25",
    "financing_deadline": "2024-02-05",
    "closing_date": "2024-02-15",
    "purchase_price": 450000,
    "title_company": "Austin Title Co",
    "lender_name": "First National",
    "lender_contact": "loan@firstnational.com"
  }'
```

## Auto-Generated Task Checklist

The AI generates tasks based on transaction type and dates:

### Buyer Transaction Tasks
| Task | Typical Due | Priority | Category |
|------|-------------|----------|----------|
| Send executed contract | Day 0 | High | Documents |
| Order title commitment | Day 3 | High | Title |
| Verify earnest money | Day 3 | High | Financial |
| Schedule inspection | Day 5 | High | Inspection |
| Follow up with lender | Day 7 | Medium | Financing |
| Schedule appraisal | Day 10 | Medium | Financing |
| Review title commitment | Day 14 | Medium | Title |
| Confirm clear to close | Close-3 | High | Closing |
| Schedule final walkthrough | Close-1 | High | Closing |

### Seller Transaction Tasks
| Task | Typical Due | Priority | Category |
|------|-------------|----------|----------|
| Order payoff statements | Day 3 | High | Financial |
| Prepare property disclosures | Day 5 | High | Documents |
| Coordinate inspection access | Day 5 | High | Inspection |
| Review repair requests | Option End | High | Negotiation |
| Order HOA documents | Day 10 | Medium | Documents |
| Confirm title clearance | Day 20 | Medium | Title |
| Schedule closing | Close-7 | High | Closing |

## Alert Thresholds

| Alert Type | Condition | Action |
|------------|-----------|--------|
| **CRITICAL** | Task overdue | SMS + Email to agent |
| **High** | Due in 24 hours, Priority=High | Email to agent |
| **Medium** | Due in 48 hours | Email to agent |

## Daily Summary Email

The 8am summary includes:
- Active transaction count
- Overdue task count (red alert if > 0)
- Tasks due today
- Tasks due this week
- Pipeline sorted by days to close

## Integration Points

### Inbound Triggers
- Orchestrator: `lead-converted` event
- CRM contract status change
- DocuSign contract executed webhook

### Outbound Handoffs
- When transaction closes → Client Success Agent
- POST to `/webhook/new-client` with:
```json
{
  "name": "Client Name",
  "email": "client@email.com",
  "phone": "+15551234567",
  "address": "123 Oak Lane",
  "close_date": "2024-02-15",
  "purchase_price": 450000,
  "type": "buyer"
}
```

## Task Status Updates

Update task status via webhook:
```bash
curl -X POST https://your-n8n.com/webhook/update-task \
  -H "Content-Type: application/json" \
  -d '{
    "task_id": "TX-123-T5",
    "status": "completed",
    "notes": "Inspection passed with minor items"
  }'
```

## Success Metrics

| Metric | Target | How to Track |
|--------|--------|--------------|
| On-time closings | > 95% | Close date vs actual |
| Overdue tasks | < 5% | Daily summary stats |
| Deadline compliance | 100% | Critical deadlines hit |
| Agent hours saved | 25+ hrs/tx | Before/after comparison |

## Troubleshooting

### Tasks Not Generating
1. Check OpenRouter API key
2. Verify JSON response parsing
3. Check date format in request

### Alerts Not Sending
1. Verify schedule trigger active
2. Check Twilio balance for SMS
3. Verify Gmail credentials

### Wrong Due Dates
1. Check date parsing in normalize node
2. Verify timezone settings
3. Review AI prompt for date calculation

## Customization Options

### Add Document Checklist
Extend tasks sheet with document tracking:
- Document name
- Required/Optional
- Received date
- Storage link

### Add Party Notifications
Send updates to all parties:
- Title company
- Lender
- Other agent
- Clients

### Add Milestone SMS to Client
Notify clients at key milestones:
- Option period ending
- Clear to close
- Final walkthrough reminder
