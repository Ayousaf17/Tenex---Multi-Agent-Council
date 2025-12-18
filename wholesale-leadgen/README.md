# Wholesale Real Estate Lead Generation System

Automated lead sourcing, enrichment, outreach, and qualification system for wholesale real estate acquisition.

## System Overview

```
+------------------+     +------------------+     +------------------+     +------------------+
|   ENGINE 1       |     |   ENGINE 2       |     |   ENGINE 3       |     |   ENGINE 4       |
|  LEAD SOURCING   | --> |   ENRICHMENT     | --> |    OUTREACH      | --> |  QUALIFICATION   |
+------------------+     +------------------+     +------------------+     +------------------+
| - Zillow FSBO    |     | - Skip Tracing   |     | - Email Sequence |     | - Response       |
| - Craigslist     |     | - Property Value |     | - SMS Campaigns  |     |   Analysis       |
| - FB Marketplace |     | - AI Analysis    |     | - Follow-ups     |     | - Lead Scoring   |
| - Expired MLS    |     | - Photo Analysis |     | - Personalization|     | - Hot Lead Alerts|
+------------------+     +------------------+     +------------------+     +------------------+
         |                       |                       |                       |
         v                       v                       v                       v
    +---------------------------------------------------------------------------+
    |                         AIRTABLE DATABASE                                  |
    |   Leads | Conversations | Templates | Analytics                           |
    +---------------------------------------------------------------------------+
```

## Quick Start

### Prerequisites

1. **n8n** - Self-hosted or n8n Cloud ($20/month)
2. **Airtable** - Free tier works initially
3. **OpenRouter API** - For AI analysis (~$10/month)
4. **Gmail Account** - For email outreach
5. **Twilio Account** - For SMS ($1/month + usage)
6. **Skip Tracing Service** - BatchSkipTracing or similar

### Setup Steps

1. Import Airtable base template (see `docs/AIRTABLE-SCHEMA.md`)
2. Configure credentials in n8n (see `docs/CREDENTIALS.md`)
3. Import workflows into n8n
4. Configure webhook URLs
5. Test with 10 manual leads

## Workflows

### Engine 1: Lead Sourcing

| Workflow | File | Trigger | Description |
|----------|------|---------|-------------|
| Zillow FSBO Scraper | `zillow-fsbo-scraper.json` | Daily 8 AM | Finds For Sale By Owner listings |
| Craigslist Monitor | `craigslist-monitor.json` | Every 4 hours | Monitors Craigslist real estate listings |
| FB Marketplace | `fb-marketplace-scraper.json` | Every 6 hours | Scrapes Facebook Marketplace listings |
| Expired Listings | `expired-listings-monitor.json` | Daily 6 AM | Finds recently expired MLS listings |

### Engine 2: Enrichment

| Workflow | File | Trigger | Description |
|----------|------|---------|-------------|
| Skip Trace & Enrich | `skip-trace-enrich.json` | New lead (Status=New) | Adds contact info + property data |
| Photo Analysis | `photo-analysis.json` | Lead has photos | AI condition assessment |
| Deal Scoring | `deal-scoring.json` | After enrichment | Calculates deal potential |

### Engine 3: Outreach

| Workflow | File | Trigger | Description |
|----------|------|---------|-------------|
| Initial Email | `initial-email-outreach.json` | Status=Ready | Personalized first contact |
| Email Follow-up | `email-followup-sequence.json` | Daily 9 AM | Day 3, 7, 14 follow-ups |
| SMS Outreach | `sms-outreach.json` | Manual/High Score | Text message for hot leads |

### Engine 4: Qualification

| Workflow | File | Trigger | Description |
|----------|------|---------|-------------|
| Email Response Monitor | `email-response-monitor.json` | Incoming email | Analyzes seller responses |
| SMS Response Monitor | `sms-response-monitor.json` | Incoming SMS | Processes text replies |
| Hot Lead Brief | `hot-lead-brief-generator.json` | Status=HOT | Creates call prep document |
| Daily Digest | `daily-digest.json` | Daily 8 PM | Summary of day's activity |

## Lead Flow

```
New Lead Found
     |
     v
+--------------------+
|  Status: NEW       |  <-- Just discovered
+--------------------+
     |
     | (Enrichment workflow runs)
     v
+--------------------+
|  Status: ENRICHING |  <-- Getting contact info
+--------------------+
     |
     | (Skip trace complete)
     v
+--------------------+
|  Status: READY     |  <-- Ready for outreach
+--------------------+
     |
     | (Email sent)
     v
+--------------------+
|  Status: CONTACTED |  <-- Waiting for response
+--------------------+
     |
     +-------+-------+-------+
     |       |       |       |
     v       v       v       v
  COLD    WARM    HOT    DEAD
   |       |       |       |
   |       |       |       +-> Archive
   |       |       |
   |       |       +-> Slack alert + Call brief
   |       |
   |       +-> Continue nurturing
   |
   +-> 6-month follow-up list
```

## Cost Breakdown

### Fixed Costs (Monthly)
| Service | Cost |
|---------|------|
| n8n Cloud | $20 |
| Twilio number | $1 |
| **Total** | **$21** |

### Variable Costs (Per Lead)
| Service | Cost |
|---------|------|
| Skip tracing | $0.10 |
| LLM analysis | $0.03 |
| Photo analysis | $0.02 |
| Email outreach | $0.01 |
| SMS (if used) | $0.01 |
| **Total per lead** | **~$0.17** |

### Example: 100 leads/month
- Fixed: $21
- Variable: $17
- **Total: $38/month**

## Directory Structure

```
wholesale-leadgen/
├── README.md                    # This file
├── workflows/                   # n8n workflow JSON files
│   ├── engine1-sourcing/
│   │   ├── zillow-fsbo-scraper.json
│   │   ├── craigslist-monitor.json
│   │   ├── fb-marketplace-scraper.json
│   │   └── expired-listings-monitor.json
│   ├── engine2-enrichment/
│   │   ├── skip-trace-enrich.json
│   │   ├── photo-analysis.json
│   │   └── deal-scoring.json
│   ├── engine3-outreach/
│   │   ├── initial-email-outreach.json
│   │   ├── email-followup-sequence.json
│   │   └── sms-outreach.json
│   └── engine4-qualification/
│       ├── email-response-monitor.json
│       ├── sms-response-monitor.json
│       ├── hot-lead-brief-generator.json
│       └── daily-digest.json
├── docs/
│   ├── AIRTABLE-SCHEMA.md       # Database structure
│   ├── CREDENTIALS.md           # API setup guide
│   ├── PROMPTS.md              # LLM prompt templates
│   └── TROUBLESHOOTING.md      # Common issues
├── prompts/                     # OpenRouter prompt templates
│   ├── lead-analysis.md
│   ├── email-generation.md
│   ├── response-analysis.md
│   └── deal-scoring.md
└── templates/
    ├── email-templates/         # Email script variants
    └── sms-templates/           # SMS message variants
```

## Target Lead Sources

### Primary (Highest Conversion)
1. **FSBO** - For Sale By Owner (70% eventually list with agent)
2. **Expired Listings** - Proven motivated, contract ended
3. **Withdrawn Listings** - Urgent situation triggered removal

### Secondary
4. **High Equity Properties** - Can negotiate creative deals
5. **Tired Landlords** - Rental management fatigue
6. **Absentee Owners** - Out-of-state, want to liquidate

### Tertiary (Long-term)
7. **Pre-Foreclosure** - Need quick cash sale
8. **Tax Delinquent** - Financial distress signals

## Integration Points

### Incoming Webhooks (n8n receives)
- Gmail webhook - New email received
- Twilio webhook - New SMS received
- Airtable webhook - Record status changes

### Outgoing API Calls
- OpenRouter API - LLM analysis
- Skip tracing API - Contact enrichment
- Zillow/Redfin API - Property valuation
- Gmail API - Send emails
- Twilio API - Send SMS
- Slack API - Hot lead alerts

## Performance Metrics

Track these in Airtable/Dashboard:

| Metric | Target |
|--------|--------|
| Leads sourced/day | 20-50 |
| Enrichment success rate | >80% |
| Email open rate | >40% |
| Response rate | >5% |
| Warm lead conversion | >10% |
| Hot leads/week | 5-10 |
| Deals closed/month | 1-3 |

## Compliance Notes

### Email (CAN-SPAM)
- Include physical address
- Provide unsubscribe option
- Honor opt-out requests within 10 days
- Don't use misleading headers

### SMS (TCPA)
- Get consent before texting (or use exemption for business inquiry)
- Include opt-out instructions
- Don't text before 8am or after 9pm local time
- Maintain do-not-contact list

### Real Estate Specific
- Don't misrepresent your role (investor vs agent)
- Disclose if you're a licensed agent
- Follow state wholesaling regulations

## Scaling Guide

### 0-50 leads/month
- Airtable free tier
- n8n Cloud starter
- Manual skip tracing

### 50-200 leads/month
- Airtable Pro ($20/month)
- n8n Cloud Pro
- Automated skip tracing API

### 200+ leads/month
- Migrate to Supabase
- Self-hosted n8n
- Dedicated email domain
- Virtual assistant for hot leads

## Support

For issues or questions:
1. Check `docs/TROUBLESHOOTING.md`
2. Review n8n execution logs
3. Verify API credentials
4. Check Airtable webhook configurations
