# Marketing Automation Agent Playbook

## Overview

**Purpose:** Maintain consistent market presence without manual effort. Schedule content, promote listings, and ensure the agent's brand stays visible during busy transaction periods.

**Key Metric:** 75% time savings on marketing activities with scheduling tools.

## Workflow Diagram

```
NEW LISTING:
+-------------------+
| New Listing       |
| Webhook           |
+---------+---------+
          |
          v
+-------------------+
| Normalize Data    |
| (address, price)  |
+---------+---------+
          |
          v
+-------------------+
| Generate Content  |
| (AI for each      |
| platform)         |
+---------+---------+
          |
    +-----+-----+-----+
    |     |     |     |
    v     v     v     v
[Insta] [FB] [Email] [LinkedIn]
    |     |     |     |
    +-----+-----+-----+
          |
          v
+-------------------+
| Log to Content    |
| Calendar          |
+-------------------+

LISTING UPDATE:
+-------------------+
| Update Webhook    |
| (price change,    |
| under contract)   |
+---------+---------+
          |
          v
+-------------------+
| Generate Update   |
| Announcement      |
+---------+---------+
          |
          v
+-------------------+
| Multi-Platform    |
| Distribution      |
+-------------------+

JUST SOLD:
+-------------------+
| Just Sold         |
| Webhook           |
+---------+---------+
          |
          v
+-------------------+
| Generate Success  |
| Story Content     |
+---------+---------+
          |
          v
+-------------------+
| Social + Email    |
| Campaign          |
+-------------------+

WEEKLY MARKET UPDATE (Monday):
+-------------------+
| Get Market Stats  |
| (via API or       |
| manual data)      |
+---------+---------+
          |
          v
+-------------------+
| Generate Market   |
| Update Email      |
+---------+---------+
          |
          v
+-------------------+
| Send to           |
| Subscriber List   |
+-------------------+
```

## Setup Steps

### 1. Import Workflow
- Import `re_marketing_automation_agent.json` into n8n
- Activate the workflow

### 2. Create Google Sheets

**Content Calendar Sheet:**
```
Content ID | Listing ID | Content Type | Platform | Scheduled Date |
Posted At | Content Text | Image URL | Engagement | Status
```

**Listings Sheet:**
```
Listing ID | Address | Price | Bedrooms | Bathrooms | Sqft |
Photo URLs | Status | List Date | Agent | Description
```

### 3. Configure Credentials
- `OPENROUTER_CREDENTIAL_ID` - AI content generation
- `GMAIL_CREDENTIAL_ID` - Email campaigns
- `GOOGLE_SHEETS_CREDENTIAL_ID` - Content tracking
- `OPENAI_CREDENTIAL_ID` - DALL-E for images (optional)
- Social media APIs (Buffer, Meta, LinkedIn)

### 4. Update Variables
- `YOUR_AGENT_NAME`
- `YOUR_BRAND_HASHTAGS`
- `YOUR_MLS_REGION`
- Content sheet IDs

### 5. Test Webhooks
```bash
# New Listing
curl -X POST https://your-n8n.com/webhook/new-listing \
  -H "Content-Type: application/json" \
  -d '{
    "address": "789 Pine St, Austin TX 78703",
    "price": 650000,
    "bedrooms": 4,
    "bathrooms": 3,
    "sqft": 2800,
    "description": "Stunning modern home in Tarrytown...",
    "photo_urls": ["url1", "url2", "url3"]
  }'

# Listing Update
curl -X POST https://your-n8n.com/webhook/listing-update \
  -H "Content-Type: application/json" \
  -d '{
    "listing_id": "L-123",
    "update_type": "price_change",
    "old_price": 650000,
    "new_price": 625000
  }'

# Just Sold
curl -X POST https://your-n8n.com/webhook/just-sold \
  -H "Content-Type: application/json" \
  -d '{
    "listing_id": "L-123",
    "sale_price": 620000,
    "days_on_market": 21
  }'
```

## Content Types by Platform

### Instagram
- Carousel posts for new listings (3-5 images)
- Stories for behind-the-scenes
- Reels for quick tours
- Hashtags: Local + RE specific

### Facebook
- Longer descriptions
- Virtual tour links
- Local community context
- Neighborhood highlights

### LinkedIn
- Professional market insights
- Investment angle for properties
- Success metrics
- Industry commentary

### Email
- Featured listings
- Market updates
- Just sold announcements
- Client success stories

## AI Prompts by Content Type

### New Listing Post
```
Generate an engaging social media post for:
- Property: [address]
- Price: [price]
- Beds/Baths: [beds]/[baths]
- Key features: [description]
- Platform: [Instagram/Facebook/LinkedIn]

Keep it [platform-appropriate length], highlight unique features,
include call-to-action, and maintain professional but warm tone.
```

### Price Change
```
Generate announcement for price change:
- Property: [address]
- Old price: [old_price]
- New price: [new_price]
- Reduction: [percentage]

Create urgency without desperation. Highlight value opportunity.
```

### Just Sold
```
Generate success story post:
- Property: [address]
- Sale price: [price]
- Days on market: [days]
- Over/under asking: [difference]

Celebrate success, thank clients (privacy-aware),
subtle expertise demonstration.
```

## Weekly Market Update

### Data Sources
- MLS statistics API
- Redfin/Zillow market data
- Local news headlines
- Interest rate updates

### Email Structure
1. **Headline stat** - Most impactful number
2. **Market summary** - 2-3 sentences
3. **What this means** - Buyer/seller implications
4. **Featured listing** - Current inventory highlight
5. **CTA** - Contact for personalized advice

## Integration Points

### Inbound Triggers
- MLS listing sync
- CRM listing status change
- Manual listing upload
- Social engagement notifications

### Outbound Distribution
- Buffer/Hootsuite for scheduling
- Direct Meta API for FB/IG
- LinkedIn API
- Mailchimp/ConvertKit for email

## Success Metrics

| Metric | Target | How to Track |
|--------|--------|--------------|
| Listing views | +50% vs manual | Platform analytics |
| Social engagement | > 3% rate | Native analytics |
| Email open rate | > 35% | ESP analytics |
| Time saved | > 10 hrs/week | Before/after logging |

## Troubleshooting

### Images Not Posting
1. Check image URL accessibility
2. Verify image format (JPG/PNG)
3. Check platform size requirements
4. Review API rate limits

### Content Not Generating
1. Check OpenRouter credits
2. Verify API key validity
3. Review prompt structure
4. Check response parsing

### Schedule Conflicts
1. Implement spacing rules (min 2 hrs)
2. Avoid weekend scheduling for B2B
3. Track optimal posting times
4. Adjust based on engagement data

## Customization Options

### Add Video Generation
- Integrate with Pictory/Synthesia
- Auto-generate property walkthrough
- Create market update videos

### Add Paid Ad Automation
- Facebook Ads API integration
- Auto-boost high-performing posts
- Retargeting pixel management

### Add Content A/B Testing
- Generate 2 versions of each post
- Track engagement per version
- Auto-select winning format
