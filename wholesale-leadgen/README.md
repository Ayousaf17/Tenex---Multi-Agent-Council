# Wholesale Real Estate Lead Generation System

Automated lead sourcing, enrichment, outreach, and qualification system for wholesale real estate acquisition.

**Powered by [Apify](https://apify.com)** for reliable, maintenance-free web scraping.

## System Overview

```
+------------------+     +------------------+     +------------------+     +------------------+
|   ENGINE 1       |     |   ENGINE 2       |     |   ENGINE 3       |     |   ENGINE 4       |
|  LEAD SOURCING   | --> |   ENRICHMENT     | --> |    OUTREACH      | --> |  QUALIFICATION   |
+------------------+     +------------------+     +------------------+     +------------------+
| - Zillow FSBO    |     | - Skip Tracing   |     | - Email Sequence |     | - Response       |
| - Realtor.com    |     | - Property Value |     | - SMS Campaigns  |     |   Analysis       |
| - FB Marketplace |     | - AI Analysis    |     | - Follow-ups     |     | - Lead Scoring   |
| - Craigslist     |     | - Photo Analysis |     | - Personalization|     | - Hot Lead Alerts|
+------------------+     +------------------+     +------------------+     +------------------+
         |                       |                       |                       |
         | (via Apify)           |                       |                       |
         v                       v                       v                       v
    +---------------------------------------------------------------------------+
    |                         AIRTABLE DATABASE                                  |
    |   Leads | Conversations | Templates | Analytics                           |
    +---------------------------------------------------------------------------+
```

## Why Apify for Scraping?

| Aspect | DIY Scraping | With Apify |
|--------|--------------|------------|
| Setup time | 40-60 hours | 1-2 hours |
| Maintenance | 2-4 hrs/month | 0 hours |
| Success rate | 70-85% | 95%+ |
| Proxy costs | $49+/month | Included |
| Anti-bot bypass | Manual | Automatic |

**Bottom line:** Apify costs ~$49/month but saves 60+ hours of development and maintenance.

## Quick Start

### Prerequisites

1. **Apify** - Web scraping platform (~$49/month)
2. **n8n** - Self-hosted or n8n Cloud ($20/month)
3. **Airtable** - Free tier works initially
4. **OpenRouter API** - For AI analysis (~$10/month)
5. **Gmail Account** - For email outreach
6. **Twilio Account** - For SMS ($1/month + usage)
7. **Skip Tracing Service** - BatchSkipTracing or similar

### Setup Steps

1. Import Airtable base template (see `docs/AIRTABLE-SCHEMA.md`)
2. Configure credentials in n8n (see `docs/CREDENTIALS.md`)
3. Import workflows into n8n
4. Configure webhook URLs
5. Test with 10 manual leads

## Workflows

### Engine 1: Lead Sourcing (via Apify)

| Workflow | File | Trigger | Description |
|----------|------|---------|-------------|
| Zillow FSBO Scraper | `zillow-fsbo-scraper.json` | Daily 8 AM | Finds For Sale By Owner listings (Apify) |
| Realtor.com Expired | `realtor-expired-scraper.json` | Daily 6 AM | Finds expired/off-market listings (Apify) |
| FB Marketplace | `fb-marketplace-scraper.json` | Every 6 hours | Scrapes Facebook Marketplace (Apify + AI filter) |
| Craigslist Monitor | `craigslist-monitor.json` | Every 4 hours | Monitors Craigslist via RSS (DIY - simple) |

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
| Apify (scraping) | $49 |
| n8n Cloud | $20 |
| Twilio number | $1 |
| **Total** | **$70** |

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
- Fixed: $70
- Variable: $17
- **Total: $87/month**

### ROI Example
- Cost: $87/month
- Close 1 wholesale deal: $5,000-50,000 profit
- **ROI: 5,700% - 57,000%**

## Directory Structure

```
wholesale-leadgen/
├── README.md                    # This file
├── workflows/                   # n8n workflow JSON files
│   ├── engine1-sourcing/        # Lead sourcing (Apify-powered)
│   │   ├── zillow-fsbo-scraper.json      # Apify
│   │   ├── realtor-expired-scraper.json  # Apify
│   │   ├── fb-marketplace-scraper.json   # Apify
│   │   └── craigslist-monitor.json       # DIY (RSS)
│   ├── engine2-enrichment/
│   │   └── skip-trace-enrich.json
│   ├── engine3-outreach/
│   │   └── initial-email-outreach.json
│   └── engine4-qualification/
│       └── email-response-monitor.json
├── docs/
│   ├── AIRTABLE-SCHEMA.md       # Database structure
│   ├── CREDENTIALS.md           # API setup guide (includes Apify)
│   └── PROMPTS.md              # LLM prompt templates
├── prompts/                     # OpenRouter prompt templates
│   └── lead-analysis.md
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
- **Apify API** - Web scraping (Zillow, Realtor, FB Marketplace)
- OpenRouter API - LLM analysis
- Skip tracing API - Contact enrichment
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
