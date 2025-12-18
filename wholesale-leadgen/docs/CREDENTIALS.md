# Credentials Setup Guide

Complete guide for setting up all API credentials needed for the wholesale lead gen system.

---

## Required Credentials

| Service | Purpose | Cost | Priority |
|---------|---------|------|----------|
| **Apify** | Web scraping | ~$49/month | Required |
| Airtable | Database | Free tier | Required |
| OpenRouter | AI analysis | Pay-as-you-go | Required |
| Gmail | Email outreach | Free | Required |
| Twilio | SMS (optional) | ~$0.01/SMS | Optional |
| Slack | Notifications | Free | Recommended |
| Skip Tracing | Contact data | ~$0.10/lookup | Required |

---

## 0. Apify Setup (Web Scraping Platform)

Apify handles all the complex web scraping - proxy rotation, anti-bot bypass, and data parsing. This replaces DIY scraping and saves 60+ hours of development.

### Create Account

1. Go to [apify.com](https://apify.com)
2. Sign up (free tier available for testing)
3. Choose a plan based on your needs:
   - **Free**: 5 Actor runs/month (for testing)
   - **Personal ($49/month)**: 100 Actor runs (good for solo operator)
   - **Team ($499/month)**: Unlimited (for high volume)

### Get API Token

1. Log in to Apify Console
2. Go to Settings > Integrations
3. Find your **Personal API Token**
4. Copy the token (format: `apify_api_XXXXXXXXXXXXX`)

### n8n Configuration

Create HTTP Header Auth credential:
- **Credential Type**: Header Auth
- **Name**: "Apify API Token"
- **Header Name**: `Authorization`
- **Header Value**: `Bearer apify_api_XXXXXXXXXXXXX`

### Recommended Actors

These are the pre-built scrapers we use:

| Actor | Purpose | Cost/Run |
|-------|---------|----------|
| `apify/zillow-scraper` | Zillow FSBO/listings | ~$0.25-1.00 |
| `maxcopell/realtor-scraper` | Realtor.com listings | ~$0.25-1.00 |
| `apify/facebook-marketplace-scraper` | FB Marketplace | ~$0.50-2.00 |

### Actor Configuration Tips

**Zillow Scraper:**
```json
{
  "search": "fsbo",
  "location": "Austin, TX",
  "maxItems": 100,
  "type": "sale"
}
```

**Realtor Scraper:**
```json
{
  "location": "Austin, TX",
  "maxItems": 100,
  "status": "off_market",
  "daysOnMarket": { "min": 60, "max": 180 }
}
```

**FB Marketplace:**
```json
{
  "searchQuery": "house for sale by owner",
  "location": "Austin, TX",
  "maxItems": 50,
  "category": "propertyrentals"
}
```

### Cost Estimation

| Volume | Runs/Month | Apify Cost | Notes |
|--------|------------|------------|-------|
| Low | 30 | ~$15 | Testing/learning |
| Medium | 100 | ~$49 | Solo operator |
| High | 300+ | ~$100+ | Scaling up |

### Why Apify vs DIY Scraping?

| Aspect | DIY Scraping | Apify |
|--------|--------------|-------|
| Setup time | 40-60 hours | 1-2 hours |
| Maintenance | 2-4 hrs/month | 0 hours |
| Success rate | 70-85% | 95%+ |
| Proxy costs | $49+/month | Included |
| Anti-bot bypass | Manual | Automatic |
| Data quality | Variable | Consistent |

**Bottom line:** The extra $49/month pays for itself in the first hour of saved time.

---

## 1. Airtable Setup

### Get Personal Access Token

1. Go to [airtable.com/create/tokens](https://airtable.com/create/tokens)
2. Click "Create new token"
3. Name: "Wholesale Lead Gen"
4. Add scopes:
   - `data.records:read`
   - `data.records:write`
   - `schema.bases:read`
5. Under "Access", add your Wholesale Lead Gen base
6. Click "Create token"
7. **Copy and save the token immediately** (you won't see it again)

### Get Base ID

1. Open your Airtable base in browser
2. Look at the URL: `https://airtable.com/appXXXXXXXXXXXXXX/...`
3. Copy the part starting with `app` - this is your Base ID

### n8n Configuration

In n8n, create Airtable credentials:
- **Credential Type**: Airtable Personal Access Token
- **Access Token**: Your token from step 1

Set environment variable:
```
AIRTABLE_BASE_ID=appXXXXXXXXXXXXXX
```

---

## 2. OpenRouter Setup

### Get API Key

1. Go to [openrouter.ai](https://openrouter.ai)
2. Create account or sign in
3. Go to [openrouter.ai/keys](https://openrouter.ai/keys)
4. Click "Create Key"
5. Name: "Wholesale Lead Gen"
6. Copy the API key

### Add Credits

1. Go to [openrouter.ai/credits](https://openrouter.ai/credits)
2. Add $10-20 to start (will last ~500+ AI analyses)

### n8n Configuration

Create HTTP Header Auth credential:
- **Credential Type**: Header Auth
- **Name**: "OpenRouter API"
- **Header Name**: `Authorization`
- **Header Value**: `Bearer sk-or-v1-XXXXXXXXXXXXX` (your API key)

### Recommended Models

| Model | Use Case | Cost | Speed |
|-------|----------|------|-------|
| `anthropic/claude-3.5-sonnet` | Best quality analysis | ~$3/1M tokens | Fast |
| `anthropic/claude-3-haiku` | Budget option | ~$0.25/1M tokens | Fastest |
| `openai/gpt-4o-mini` | Good balance | ~$0.15/1M tokens | Fast |
| `google/gemini-flash-1.5` | Very cheap | ~$0.075/1M tokens | Fast |

For this system, use Claude 3.5 Sonnet for best results. Cost: ~$0.01-0.03 per lead analysis.

---

## 3. Gmail Setup

### Enable Gmail API

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create new project: "Wholesale Lead Gen"
3. Enable Gmail API:
   - Go to APIs & Services > Enable APIs
   - Search "Gmail API"
   - Click Enable

### Create OAuth Credentials

1. Go to APIs & Services > Credentials
2. Click "Create Credentials" > "OAuth client ID"
3. Application type: "Web application"
4. Name: "n8n Integration"
5. Authorized redirect URIs: Add your n8n OAuth callback URL
   - For n8n Cloud: `https://YOUR-INSTANCE.app.n8n.cloud/rest/oauth2-credential/callback`
   - For self-hosted: `https://YOUR-DOMAIN/rest/oauth2-credential/callback`
6. Click Create and save Client ID & Secret

### n8n Configuration

Create Gmail OAuth2 credential:
- **Client ID**: From Google Cloud
- **Client Secret**: From Google Cloud
- Click "Connect" and authorize with your Gmail account

### Gmail Sending Limits

- Personal Gmail: 500 emails/day
- Google Workspace: 2,000 emails/day
- Recommendation: Stay under 100 emails/day to avoid spam flags

### Best Practices

1. Warm up your email (send manually for 2 weeks first)
2. Use professional signature
3. Don't use spam trigger words
4. Include physical address (CAN-SPAM)
5. Honor unsubscribes

---

## 4. Twilio Setup (Optional - for SMS)

### Create Account

1. Go to [twilio.com](https://www.twilio.com)
2. Sign up for free trial ($15 credit)
3. Verify your phone number

### Get Phone Number

1. In Twilio Console, go to Phone Numbers
2. Buy a number (~$1/month)
3. Choose number with SMS capability

### Get API Credentials

1. Go to Twilio Console dashboard
2. Find Account SID and Auth Token
3. Copy both values

### n8n Configuration

Create Twilio API credentials:
- **Account SID**: `ACXXXXXXXXXXXXXXXXXX`
- **Auth Token**: Your auth token

### SMS Costs

- Outbound SMS: ~$0.0075/message
- Inbound SMS: ~$0.0075/message
- Monthly number: ~$1.00

### TCPA Compliance

**Important legal requirements:**
- Get consent before texting (or use business inquiry exemption)
- Include opt-out in every message: "Reply STOP to unsubscribe"
- Don't text before 8am or after 9pm recipient's local time
- Maintain do-not-text list
- Include your business name

Example compliant message:
```
Hi [Name], this is [Your Name] from [Company]. I'm reaching out about
your property on [Street]. Would you be open to a quick chat about
selling? Reply STOP to opt out.
```

---

## 5. Slack Setup (Recommended)

### Create Slack App

1. Go to [api.slack.com/apps](https://api.slack.com/apps)
2. Click "Create New App" > "From scratch"
3. Name: "Wholesale Lead Gen Bot"
4. Choose your workspace

### Add Permissions

1. Go to OAuth & Permissions
2. Add Bot Token Scopes:
   - `chat:write`
   - `channels:read`
3. Install app to workspace
4. Copy Bot User OAuth Token

### Create Channels

Create these channels for notifications:
- `#leads` - New leads found
- `#hot-leads` - HOT lead alerts (high priority)
- `#daily-digest` - Daily summaries

### n8n Configuration

Create Slack API credentials:
- **Access Token**: Your Bot User OAuth Token (`xoxb-...`)

---

## 6. Skip Tracing Setup

### Option A: BatchSkipTracing (Recommended)

1. Go to [batchskiptracing.com](https://www.batchskiptracing.com)
2. Create account
3. Add credits ($50 = ~500 lookups)
4. Get API key from dashboard

**Cost:** ~$0.10 per lookup

### Option B: PropStream

1. Go to [propstream.com](https://www.propstream.com)
2. Subscribe ($99/month includes skip tracing)
3. Use their API or manual export

### Option C: BeenVerified API

1. Go to [beenverified.com/api](https://www.beenverified.com/api)
2. Contact for API access
3. Pricing varies

### n8n Configuration (BatchSkipTracing)

Create HTTP Header Auth credential:
- **Name**: "BatchSkipTracing API"
- **Header Name**: `Authorization`
- **Header Value**: `Bearer YOUR_API_KEY`

### API Usage Example

```json
POST https://api.batchskiptracing.com/api/v1/skip-trace

{
  "address": "123 Main St",
  "city": "Austin",
  "state": "TX",
  "zip": "78701"
}

Response:
{
  "owner_name": "John Smith",
  "phones": ["+15125551234", "+15125555678"],
  "emails": ["john@email.com"],
  "mailing_address": "456 Oak Ave, Dallas, TX 75201"
}
```

---

## Environment Variables Summary

Set these in n8n Settings > Variables:

```bash
# Airtable
AIRTABLE_BASE_ID=appXXXXXXXXXXXXXX

# Target Market (for scraping)
TARGET_CITY=Austin
TARGET_STATE=TX
TARGET_ZIP_CODES=78701,78702,78703,78704

# Your Info (for email signatures)
YOUR_NAME=John Doe
YOUR_PHONE=512-555-1234
YOUR_COMPANY=ABC Investments
YOUR_ADDRESS=123 Business St, Austin, TX 78701
```

---

## Testing Your Setup

### Quick Test Checklist

1. **Airtable**:
   - Can you create a record via n8n?
   - Can you read records?

2. **OpenRouter**:
   - Send test prompt, get response?
   - Check credits balance

3. **Gmail**:
   - Can you send a test email to yourself?
   - Does OAuth stay connected?

4. **Slack**:
   - Can you post to #leads channel?
   - Does bot show up in channel?

5. **Skip Tracing**:
   - Test with known address
   - Verify you get phone/email back

### Test Workflow

Import and run `test-credentials.json` (in workflows folder) to verify all connections work.

---

## Security Best Practices

1. **Never commit credentials to git** - Use environment variables
2. **Rotate API keys quarterly**
3. **Use minimum required permissions**
4. **Monitor for unusual API usage**
5. **Keep n8n updated** for security patches
6. **Use HTTPS for all webhooks**
7. **Back up your Airtable base regularly**

---

## Troubleshooting

### Airtable "Invalid permissions"
- Check token has correct scopes
- Verify base is added to token access list
- Regenerate token if needed

### OpenRouter "401 Unauthorized"
- Check API key starts with `sk-or-v1-`
- Verify credits balance > $0
- Check rate limits

### Gmail "OAuth token expired"
- Reconnect credentials in n8n
- May need to re-authorize app

### Slack "channel_not_found"
- Invite bot to channel first
- Check channel name is correct (lowercase, no #)

### Skip tracing "No results"
- Verify address format
- Some properties have no public records
- Try alternative skip tracing service

### Apify "Run failed" or "Timeout"
- Check Actor input parameters match expected format
- Increase timeout in n8n HTTP Request node (600000ms = 10 min)
- Verify API token is correct (starts with `apify_api_`)
- Check Apify Console for detailed error logs
- Some sites may block during peak hours - try different time

### Apify "Insufficient credits"
- Add more credits at apify.com/billing
- Consider upgrading plan for consistent usage
- Monitor usage in Apify Console dashboard
