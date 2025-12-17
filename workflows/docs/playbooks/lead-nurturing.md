# Lead Nurturing Agent Playbook

## Overview

**Purpose:** Maintain engagement throughout the 6+ month typical lead nurturing cycle with instant response and personalized drip campaigns.

**Key Metric:** Leads contacted within 5 minutes convert at 100x the rate of those contacted after 30 minutes.

## Workflow Diagram

```
                    +------------------+
                    | New Lead Webhook |
                    +--------+---------+
                             |
                             v
                    +------------------+
                    | Normalize Data   |
                    +--------+---------+
                             |
              +--------------+--------------+
              |                             |
    +----Has Phone?----+           +--------+
    |         |        |           |
    v         v        v           v
[Gen SMS] [Gen Email]  |    [Gen Email Only]
    |         |        |           |
    v         v        |           |
[Send SMS] [Send Email]|           |
    |         |        |           |
    +----+----+--------+-----------+
         |
         v
  +------------------+
  | Log to Sheet     |
  | Set Drip Stage=1 |
  +------------------+

HOURLY DRIP CHECK:
  +------------------+
  | Get Leads        |
  +--------+---------+
           |
           v
  +------------------+
  | Filter Due       |
  | (Next Touchpoint)|
  +--------+---------+
           |
           v
  +------------------+
  | Generate Email   |
  | Based on Stage   |
  +--------+---------+
           |
           v
  +------------------+
  | Update Stage     |
  | Set Next Date    |
  +------------------+

DAILY COLD CHECK:
  +------------------+
  | Get Leads 30+    |
  | Days No Contact  |
  +--------+---------+
           |
           v
  +------------------+
  | Re-engagement    |
  | Email            |
  +------------------+
```

## Setup Steps

### 1. Import Workflow
- Import `re_lead_nurturing_agent.json` into n8n
- Activate the workflow

### 2. Create Google Sheet
Create a sheet named "Leads" with columns:
```
Lead ID | Name | Email | Phone | Source | Type | Property Interest |
Received At | Initial SMS Sent | Initial Email Sent | Lead Score |
Drip Stage | Last Contact | Next Touchpoint | Status
```

### 3. Configure Credentials
Update these credential IDs:
- `OPENROUTER_CREDENTIAL_ID` → Your OpenRouter credential
- `GMAIL_CREDENTIAL_ID` → Your Gmail OAuth2
- `TWILIO_CREDENTIAL_ID` → Your Twilio credential
- `GOOGLE_SHEETS_CREDENTIAL_ID` → Your Google Sheets OAuth2

### 4. Update Variables
In the workflow nodes, replace:
- `YOUR_AGENT_NAME` → Agent's name
- `YOUR_AGENT_PHONE` → Agent's phone
- `YOUR_GOOGLE_SHEET_ID` → Your leads sheet ID
- `YOUR_ACCOUNT_SID` → Twilio Account SID
- `+1YOUR_TWILIO_NUMBER` → Your Twilio phone number

### 5. Test Webhook
```bash
curl -X POST https://your-n8n.com/webhook/new-lead \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "+15551234567",
    "source": "Zillow",
    "type": "buyer",
    "property_address": "123 Main St"
  }'
```

## Drip Schedule

| Stage | Timing | Content Focus |
|-------|--------|---------------|
| 1 | Day 0 | Instant response - acknowledge inquiry |
| 2 | Day 1 | Value add - market insight or resource |
| 3 | Day 3 | Soft follow-up - ask about timeline |
| 4 | Day 7 | Market update specific to interest |
| 5 | Day 14 | Success story / testimonial |
| 6 | Day 30 | Check-in - still looking? |
| 7+ | Every 14 days | Ongoing nurture |

## Cold Lead Re-engagement

Leads with 30+ days no contact receive:
- Personalized "just checking in" email
- Reference to original interest
- Soft question to gauge interest level
- Status updated to "Re-engaged"

## Integration Points

### Inbound Triggers
- CRM webhook (Follow Up Boss, kvCORE)
- Website form submission
- Zillow/Realtor.com inquiry webhook
- Open house sign-in integration

### Outbound Handoffs
- When lead converts → Transaction Coordinator
- POST to `/webhook/orchestrator/lead-converted`

## Success Metrics

| Metric | Target | How to Track |
|--------|--------|--------------|
| Response Time | < 5 minutes | Sheet timestamp diff |
| Open Rate | > 40% | Gmail tracking |
| Reply Rate | > 15% | Manual tracking |
| Lead-to-Appointment | > 10% | Sheet status updates |
| Nurture-to-Close | > 2% | Cross-reference with TC |

## Troubleshooting

### SMS Not Sending
1. Check Twilio credentials
2. Verify phone number format (+1XXXXXXXXXX)
3. Check Twilio balance
4. Review Twilio error logs

### Emails Going to Spam
1. Set up SPF/DKIM for sending domain
2. Warm up sending reputation gradually
3. Avoid spam trigger words
4. Test with mail-tester.com

### Drip Not Running
1. Verify schedule trigger is active
2. Check sheet permissions
3. Verify "Next Touchpoint" date format
4. Check workflow execution logs

## Customization Options

### Add Lead Scoring
Add code node to calculate score based on:
- Source quality (referral = +20)
- Response engagement (+10 per reply)
- Property price range (+5 for higher)
- Timeline urgency (+15 for immediate)

### Add SMS Drip
Extend drip to include SMS on key stages:
- Stage 3: SMS if no email open
- Stage 5: SMS with appointment link

### CRM Sync
Add HTTP node to sync lead status back to CRM:
```json
{
  "lead_id": "{{$json.lead_id}}",
  "status": "{{$json.status}}",
  "last_contact": "{{$json.last_contact}}"
}
```
