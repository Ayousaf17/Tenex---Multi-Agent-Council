# Client Success Agent Playbook

## Overview

**Purpose:** Maintain relationships post-close for referrals and repeat business. Automate the "stay in touch" activities that agents neglect when busy with active transactions.

**Key Metric:** 66% of sellers find their agent through referrals or past relationships.

## Workflow Diagram

```
NEW CLOSED CLIENT:
+-------------------+
| New Client        |
| Webhook           |
+---------+---------+
          |
          v
+-------------------+
| Normalize Data    |
+---------+---------+
          |
          v
+-------------------+
| Add to Client DB  |
+---------+---------+
          |
          v
+-------------------+
| Send Congrats     |
| Email             |
+---------+---------+
          |
          v
+-------------------+
| Log Touchpoint    |
+-------------------+

DAILY ANNIVERSARY CHECK (9am):
+-------------------+
| Get All Clients   |
+---------+---------+
          |
          v
+-------------------+
| Check Anniversaries|
| & Birthdays       |
+---------+---------+
          |
    +-----+-----+
    |           |
    v           v
[Anniversary] [Birthday]
    |           |
    v           v
[AI Email]  [AI Email]
    |           |
    v           v
[Send]      [Send]
    |           |
    +-----+-----+
          |
          v
+-------------------+
| Log Touchpoint    |
+-------------------+

DAILY REVIEW REQUEST (10am):
+-------------------+
| Get Clients       |
| 30+ days post     |
+---------+---------+
          |
          v
+-------------------+
| Filter Review     |
| Candidates        |
+---------+---------+
          |
          v
+-------------------+
| Generate & Send   |
| Review Request    |
+---------+---------+
          |
          v
+-------------------+
| Update Status     |
+-------------------+

WEEKLY SPHERE NURTURE (Monday):
+-------------------+
| Get Clients       |
| 90+ days quiet    |
+---------+---------+
          |
          v
+-------------------+
| Generate Nurture  |
| Email (up to 5)   |
+---------+---------+
          |
          v
+-------------------+
| Update Last       |
| Contact           |
+-------------------+
```

## Setup Steps

### 1. Import Workflow
- Import `re_client_success_agent.json` into n8n
- Activate the workflow

### 2. Create Google Sheets

**Clients Sheet:**
```
Client ID | Name | Email | Phone | Property Address | Close Date |
Purchase Price | Birthday | Client Type | Review Status | Review Requested At |
Referral Count | Last Contact | Next Touchpoint | Touchpoint Stage | Created At
```

**Touchpoints Sheet:**
```
Client ID | Touchpoint Type | Channel | Date | Notes
```

### 3. Configure Credentials
- `OPENROUTER_CREDENTIAL_ID`
- `GMAIL_CREDENTIAL_ID`
- `GOOGLE_SHEETS_CREDENTIAL_ID`
- `TWILIO_CREDENTIAL_ID` (optional for SMS)

### 4. Update Variables
- `YOUR_AGENT_NAME`
- `YOUR_AGENT_PHONE`
- `YOUR_AGENT_EMAIL`
- `YOUR_CLIENTS_SHEET_ID`
- `YOUR_GOOGLE_REVIEW_LINK`
- `YOUR_ZILLOW_REVIEW_LINK`

### 5. Test Webhook
```bash
curl -X POST https://your-n8n.com/webhook/new-client \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Smith",
    "email": "john@email.com",
    "phone": "+15551234567",
    "address": "456 Maple Ave, Austin TX 78702",
    "close_date": "2024-01-15",
    "purchase_price": 525000,
    "birthday": "1985-06-15",
    "type": "buyer"
  }'
```

## Touchpoint Schedule

| Timing | Touchpoint | Purpose |
|--------|------------|---------|
| Close Day | Congratulations email | Celebrate milestone |
| 7 days | Check-in | Settling in okay? |
| 30 days | Review request | Google/Zillow review |
| 45 days | Review follow-up | Gentle reminder |
| 90 days | Sphere nurture | Market update |
| 6 months | Home anniversary | Celebrate + referral ask |
| 12 months | Annual anniversary | Bigger celebration |
| Birthday | Birthday email | Personal touch |
| Ongoing | Quarterly nurture | Stay top of mind |

## Review Generation System

### Initial Request (Day 30)
- Warm, personal email
- Direct links to Google + Zillow
- Simple ask - not pushy
- Reference their specific experience

### Follow-up (Day 45)
- Only if status = "requested" (no review received)
- Softer tone
- Offer alternative (text review they can copy/paste)

### Tracking
- `pending` → Never requested
- `requested` → Email sent, awaiting
- `received` → Review confirmed

## Sphere Nurturing Strategy

### Trigger: 90+ days since last contact

### Content Types (AI rotates):
1. Market update for their neighborhood
2. Home maintenance tips for season
3. Local event/restaurant recommendation
4. Just sold nearby success story
5. General check-in

### Limits:
- Max 5 nurture emails per week (avoid batch feel)
- Skip clients who've recently been contacted
- Skip those in active transactions

## Integration Points

### Inbound Triggers
- Transaction Coordinator: `transaction-closed` event
- CRM closing status change
- Manual webhook trigger

### Tracking Referrals
When a referral closes:
1. Update original client's `Referral Count`
2. Send thank-you email/gift
3. Log referral touchpoint

## Success Metrics

| Metric | Target | How to Track |
|--------|--------|--------------|
| Review rate | > 40% | Reviews / Closed clients |
| Referral rate | > 20% | Referrals / Sphere size |
| Open rate | > 50% | Gmail tracking |
| Repeat client rate | > 15% | Multi-transaction clients |

## Email Templates

### Congratulations (Close Day)
```
Subject: Congratulations on Your New Home, [First Name]!

Key elements:
- Genuine excitement
- Reference specific property
- Promise to stay in touch
- Soft referral mention in P.S.
```

### Home Anniversary
```
Subject: Happy [X]-Year Home Anniversary!

Key elements:
- Time milestone acknowledgment
- Brief reminiscing
- Neighborhood value update (if positive)
- Referral ask with context
```

### Birthday
```
Subject: Happy Birthday, [First Name]!

Key elements:
- Keep short and genuine
- Not salesy AT ALL
- Maybe restaurant recommendation
- Just a nice touch
```

### Review Request
```
Subject: Would you share your experience, [First Name]?

Key elements:
- Acknowledge their experience
- Explain reviews help future clients
- Direct link buttons
- No pressure
```

## Troubleshooting

### Emails Not Personalized
1. Check AI prompt includes all variables
2. Verify data pulled from sheet correctly
3. Review AI model response parsing

### Wrong Anniversaries
1. Check date parsing in code node
2. Verify MM-DD comparison logic
3. Check timezone handling

### Too Many/Few Nurtures
1. Adjust 90-day threshold as needed
2. Change weekly batch limit
3. Add additional filters (e.g., client type)

## Customization Options

### Add Gift Automation
Trigger physical gift sends:
- Close day: Small gift basket
- 1-year anniversary: Branded item
- Birthday: Card

### Add SMS Touchpoints
Add SMS for:
- Birthday (short text)
- Anniversary (short text)
- Market milestone (their home value up)

### Add Event Invitations
Track and invite past clients to:
- Client appreciation events
- Market update seminars
- Holiday parties
