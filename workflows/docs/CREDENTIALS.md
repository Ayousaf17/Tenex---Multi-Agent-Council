# Credentials Configuration Guide

This guide covers all API credentials needed for the Aasani Real Estate Automation System.

## Required Credentials

### 1. OpenRouter API (AI Content Generation)
**Used by:** All agents for personalized content generation

```
Provider: OpenRouter
API Key: Get from https://openrouter.ai/keys
Model: openai/gpt-4o-mini (recommended for cost efficiency)
```

**n8n Setup:**
1. Go to Credentials → New → HTTP Header Auth
2. Name: `OpenRouter API`
3. Header Name: `Authorization`
4. Header Value: `Bearer YOUR_API_KEY`

### 2. Gmail OAuth2 (Email Sending)
**Used by:** All agents for email outreach

```
Provider: Google Cloud Console
Scopes: https://www.googleapis.com/auth/gmail.send
```

**n8n Setup:**
1. Go to Credentials → New → Gmail OAuth2
2. Follow OAuth flow to authorize

**Google Cloud Setup:**
1. Create project at https://console.cloud.google.com
2. Enable Gmail API
3. Create OAuth 2.0 credentials
4. Add authorized redirect URI from n8n

### 3. Twilio (SMS Messaging)
**Used by:** Lead Nurturing, Transaction Coordinator

```
Account SID: From Twilio console
Auth Token: From Twilio console
Phone Number: Your Twilio number
```

**n8n Setup:**
1. Go to Credentials → New → HTTP Basic Auth
2. Name: `Twilio`
3. Username: Account SID
4. Password: Auth Token

**Twilio Setup:**
1. Sign up at https://www.twilio.com
2. Get a phone number
3. Note Account SID and Auth Token

### 4. Google Sheets OAuth2 (Data Storage)
**Used by:** All agents for lead/transaction/client tracking

```
Provider: Google Cloud Console
Scopes: https://www.googleapis.com/auth/spreadsheets
```

**n8n Setup:**
1. Go to Credentials → New → Google Sheets OAuth2
2. Follow OAuth flow

**Sheet Structure:**

**Leads Sheet:**
| Column | Type | Description |
|--------|------|-------------|
| Lead ID | String | Unique identifier |
| Name | String | Full name |
| Email | String | Email address |
| Phone | String | Phone number |
| Source | String | Lead source |
| Type | String | Buyer/Seller |
| Property Interest | String | Address or listing |
| Received At | DateTime | When lead came in |
| Drip Stage | Number | Current nurture stage |
| Last Contact | DateTime | Last touchpoint |
| Next Touchpoint | DateTime | Scheduled next contact |
| Status | String | New/Active/Converted/Lost |

**Transactions Sheet:**
| Column | Type | Description |
|--------|------|-------------|
| Transaction ID | String | Unique identifier |
| Property Address | String | Property address |
| Client Name | String | Client name |
| Client Email | String | Client email |
| Contract Date | Date | Contract execution date |
| Option Period End | Date | Option deadline |
| Financing Deadline | Date | Loan approval deadline |
| Closing Date | Date | Scheduled close |
| Status | String | Active/Closed/Cancelled |

**Clients Sheet:**
| Column | Type | Description |
|--------|------|-------------|
| Client ID | String | Unique identifier |
| Name | String | Full name |
| Email | String | Email address |
| Property Address | String | Their home |
| Close Date | Date | When they closed |
| Birthday | Date | For birthday emails |
| Review Status | String | pending/requested/received |
| Referral Count | Number | Referrals given |
| Last Contact | DateTime | Last touchpoint |

### 5. Google Drive (Document Storage) - Optional
**Used by:** Transaction Coordinator for document storage

```
Scopes: https://www.googleapis.com/auth/drive.file
```

## Credential IDs to Update

After creating credentials in n8n, update these placeholder IDs in each workflow:

| Placeholder | Description |
|-------------|-------------|
| `OPENROUTER_CREDENTIAL_ID` | OpenRouter API credential |
| `GMAIL_CREDENTIAL_ID` | Gmail OAuth2 credential |
| `TWILIO_CREDENTIAL_ID` | Twilio HTTP Basic Auth |
| `GOOGLE_SHEETS_CREDENTIAL_ID` | Google Sheets OAuth2 |
| `GOOGLE_DRIVE_CREDENTIAL_ID` | Google Drive OAuth2 |

## Variables to Configure

In each workflow, update these variables:

| Variable | Example | Where |
|----------|---------|-------|
| `YOUR_AGENT_NAME` | "John Smith" | All agents |
| `YOUR_AGENT_PHONE` | "+15125551234" | All agents |
| `YOUR_AGENT_EMAIL` | "john@realty.com" | All agents |
| `YOUR_TWILIO_NUMBER` | "+15125559999" | Lead Nurturing, TC |
| `YOUR_ACCOUNT_SID` | "ACxxxx" | Twilio HTTP nodes |
| `YOUR_GOOGLE_SHEET_ID` | "1abc..." | All sheet references |
| `YOUR_TRANSACTIONS_SHEET_ID` | "1xyz..." | Transaction Coordinator |
| `YOUR_CLIENTS_SHEET_ID` | "1def..." | Client Success |
| `YOUR_GOOGLE_REVIEW_LINK` | Full URL | Client Success |
| `YOUR_ZILLOW_REVIEW_LINK` | Full URL | Client Success |

## Security Best Practices

1. **Never commit credentials** - Use n8n's credential storage
2. **Use separate sheets per client** - Isolate data
3. **Limit OAuth scopes** - Only request needed permissions
4. **Rotate API keys** - Regular rotation schedule
5. **Monitor usage** - Track API costs

## Cost Estimates

| Service | Tier | Est. Monthly Cost |
|---------|------|-------------------|
| OpenRouter (GPT-4o-mini) | Pay-per-use | $10-30 |
| Twilio SMS | Pay-per-use | $20-50 |
| Gmail | Free tier | $0 |
| Google Sheets | Free tier | $0 |
| n8n Cloud | Starter | $20/mo |

**Total estimated cost:** $50-100/month per agent client
