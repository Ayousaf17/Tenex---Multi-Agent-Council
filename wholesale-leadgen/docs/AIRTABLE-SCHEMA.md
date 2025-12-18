# Airtable Database Schema

Complete database structure for the wholesale lead generation system.

## Base Setup

Create a new Airtable base called "Wholesale Lead Gen" with the following tables:

---

## Table 1: Leads (Primary Table)

### Fields

| Field Name | Type | Description | Options/Formula |
|------------|------|-------------|-----------------|
| Address | Single line text | Property address (PRIMARY) | Required |
| City | Single line text | City | |
| State | Single line text | State abbreviation | |
| ZIP | Single line text | ZIP code | |
| Source | Single select | Where lead came from | Zillow FSBO, Craigslist, FB Marketplace, Expired MLS, Referral, Other |
| Status | Single select | Current lead status | New, Enriching, Ready, Contacted, Warm, HOT, Cold, Dead, No Contact Info |
| Priority | Single select | Deal priority | high, medium, low |
| Asking Price | Currency | Listed/asking price | USD |
| Estimated Value | Currency | Zestimate/market value | USD |
| Equity Estimate | Formula | Calculated equity | `{Estimated Value} - {Asking Price}` |
| Equity Percent | Formula | Equity as percentage | `IF({Estimated Value} > 0, ({Estimated Value} - {Asking Price}) / {Estimated Value} * 100, 0)` |
| Owner Name | Single line text | Property owner name | |
| Owner Phone | Phone number | Primary contact phone | |
| Owner Email | Email | Primary contact email | |
| All Phones | Long text | All discovered phones | Comma separated |
| Listing URL | URL | Original listing link | |
| Property Type | Single select | Type of property | Single Family, Multi-Family, Condo, Townhouse, Land, Commercial |
| Beds | Number | Bedrooms | Integer |
| Baths | Number | Bathrooms | Decimal (1 place) |
| Sqft | Number | Square footage | Integer |
| Year Built | Number | Year constructed | 4-digit year |
| Lot Size | Number | Lot size in sqft | |
| Date Found | Date | When lead discovered | Include time |
| Last Contact Date | Date | Last outreach sent | Include time |
| Last Response Date | Date | Last response received | Include time |
| Emails Sent | Number | Count of emails sent | Default: 0 |
| Last Email Subject | Single line text | Subject of last email | |
| AI Score | Number | AI deal score (0-100) | |
| AI Analysis | Long text | Full AI analysis JSON | |
| Response Analysis | Long text | Response analysis JSON | |
| Exit Strategy | Single select | Recommended exit | wholesale, fix-flip, rental, hold |
| Estimated ARV | Currency | After Repair Value | USD |
| Estimated Repairs | Currency | Estimated repair cost | USD |
| Call Readiness | Single select | Ready for call? | ready_now, needs_nurturing, not_ready |
| Description | Long text | Property description | |
| Notes | Long text | Manual notes | |
| Raw Data | Long text | Original scraped JSON | |
| Conversations | Link to another record | Link to Conversations | Link to Conversations table |

### Views to Create

1. **All Leads** - Default grid view, all records
2. **New Leads** - Filter: Status = "New"
3. **Ready for Outreach** - Filter: Status = "Ready"
4. **Contacted** - Filter: Status = "Contacted"
5. **Warm Leads** - Filter: Status = "Warm"
6. **HOT Leads** - Filter: Status = "HOT" (highlight red)
7. **Cold/Dead** - Filter: Status = "Cold" OR "Dead"
8. **High Priority** - Filter: Priority = "high", Sort by AI Score desc
9. **Needs Follow-up** - Filter: Status = "Contacted" AND Last Contact Date < 3 days ago
10. **By Source** - Group by Source field

### Automations (Optional)

1. When Status → "HOT", send Slack notification
2. When Status → "Ready", trigger n8n webhook for outreach
3. Daily: Find leads with no response in 7 days, update to "Cold"

---

## Table 2: Conversations

### Fields

| Field Name | Type | Description | Options |
|------------|------|-------------|---------|
| Lead | Link to another record | Link to Leads table | Link to Leads |
| Type | Single select | Conversation type | Email Outbound, Email Inbound, SMS Outbound, SMS Inbound, Phone Call, Note |
| Date | Date | When occurred | Include time |
| Subject | Single line text | Email subject (if email) | |
| Content | Long text | Full message content | |
| Sentiment | Single select | Detected sentiment | positive, neutral, negative |
| AI Analysis | Long text | AI analysis of message | JSON |
| Created By | Single select | Human or automation | System, Manual |

### Views

1. **All Conversations** - Sorted by Date desc
2. **By Lead** - Grouped by Lead field
3. **Inbound Only** - Filter: Type contains "Inbound"
4. **Recent (7 days)** - Filter: Date > 7 days ago

---

## Table 3: Outreach Templates

### Fields

| Field Name | Type | Description | Options |
|------------|------|-------------|---------|
| Template Name | Single line text | Name identifier | |
| Channel | Single select | Outreach channel | Email, SMS |
| Sequence Position | Number | Order in sequence | 1, 2, 3... |
| Subject Template | Single line text | Email subject (with variables) | |
| Body Template | Long text | Message body (with variables) | |
| Status | Single select | Template status | Active, Testing, Retired |
| Times Used | Number | Usage count | Auto-incremented |
| Response Rate | Percent | Calculated success rate | |
| Notes | Long text | Performance notes | |

### Template Variables

Use these variables in templates (n8n will replace):
- `{{owner_name}}` - Owner's name
- `{{address}}` - Property address
- `{{city}}` - City name
- `{{price}}` - Asking price
- `{{source}}` - Lead source
- `{{your_name}}` - Your name
- `{{your_phone}}` - Your phone

---

## Table 4: Analytics (Optional)

### Fields

| Field Name | Type | Description |
|------------|------|-------------|
| Date | Date | Report date |
| Leads Found | Number | New leads discovered |
| Leads Enriched | Number | Successfully enriched |
| Emails Sent | Number | Outreach emails sent |
| Responses Received | Number | Inbound responses |
| Hot Leads | Number | New HOT leads |
| Deals Closed | Number | Deals completed |
| Source Breakdown | Long text | JSON by source |
| Notes | Long text | Daily observations |

---

## Quick Setup Instructions

### Step 1: Create Base
1. Go to airtable.com and create new base
2. Name it "Wholesale Lead Gen"

### Step 2: Create Leads Table
1. Start with the default "Table 1"
2. Rename to "Leads"
3. Add all fields from schema above
4. Set up Single Select options for Status, Source, Priority, etc.

### Step 3: Create Conversations Table
1. Add new table "Conversations"
2. Add all fields
3. Link "Lead" field to Leads table

### Step 4: Create Views
1. In Leads table, create all views listed
2. Set up filters and sorts for each view
3. Color-code HOT leads (red background)

### Step 5: Get API Credentials
1. Go to airtable.com/create/tokens
2. Create new Personal Access Token
3. Add scopes: `data.records:read`, `data.records:write`, `schema.bases:read`
4. Add your base to access list
5. Copy token for n8n

### Step 6: Get Base ID
1. Open your base in browser
2. Look at URL: `airtable.com/appXXXXXXXXXXXXXX/...`
3. Copy the `appXXXXXXXXXXXXXX` part - this is your Base ID

---

## Formula Reference

### Equity Calculations
```
Equity Estimate = {Estimated Value} - {Asking Price}
```

```
Equity Percent = IF({Estimated Value} > 0,
  ROUND(({Estimated Value} - {Asking Price}) / {Estimated Value} * 100, 1),
  0
)
```

### Days Since Contact
```
Days Since Contact = IF({Last Contact Date},
  DATETIME_DIFF(NOW(), {Last Contact Date}, 'days'),
  ''
)
```

### Deal Score Status
```
Score Status = IF({AI Score} >= 70, "🔥 Hot",
  IF({AI Score} >= 50, "⚡ Warm",
    IF({AI Score} >= 30, "❄️ Cool",
      "💀 Cold"
    )
  )
)
```

---

## n8n Integration Notes

### Environment Variables
Set these in n8n:
```
AIRTABLE_BASE_ID=appXXXXXXXXXXXXXX
```

### Webhook URLs
Create Airtable Automations that call n8n webhooks:
- When Status = "New" → Trigger enrichment workflow
- When Status = "Ready" → Trigger outreach workflow

### Rate Limits
- Airtable API: 5 requests/second
- Add delays in n8n if processing many records
- Use batch operations when possible

---

## Airtable vs Supabase

### When to Stay on Airtable
- Under 1,000 leads/month
- Don't need complex queries
- Want visual interface for manual work
- Budget-conscious (free tier: 1,200 records)

### When to Migrate to Supabase
- Over 5,000 leads
- Need SQL queries for analytics
- Building custom frontend
- Need real-time features
- Want lower costs at scale

Migration path: Export Airtable as CSV → Import to Supabase → Update n8n workflows to use Supabase nodes
