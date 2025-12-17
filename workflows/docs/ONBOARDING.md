# Client Onboarding Guide

A step-by-step guide for deploying the Aasani Real Estate Automation System for new clients.

## Pre-Onboarding Checklist

### Client Requirements
- [ ] n8n instance (cloud or self-hosted)
- [ ] Google Workspace account
- [ ] Gmail access for sending
- [ ] Twilio account (for SMS)
- [ ] OpenRouter account (for AI)
- [ ] Current CRM identified

### Information to Collect
```
Agent Information:
- Full Name: _______________
- Email: _______________
- Phone: _______________
- Brokerage: _______________

CRM Information:
- Platform: _______________
- API Access: Yes / No
- Webhook Support: Yes / No

Review Links:
- Google Business: _______________
- Zillow Profile: _______________
- Realtor.com: _______________

Branding:
- Logo URL: _______________
- Brand Colors: _______________
- Tagline: _______________
```

## Onboarding Phases

### Phase 1: Environment Setup (Day 1)
**Duration:** 2-3 hours

1. **Create Google Sheets**
   - Copy template sheets (links below)
   - Share with client's Google account
   - Note all Sheet IDs

2. **Configure n8n Instance**
   - Access client's n8n
   - Import all workflow JSON files
   - Keep workflows inactive initially

3. **Set Up Credentials**
   - OpenRouter API
   - Gmail OAuth2
   - Twilio
   - Google Sheets OAuth2

### Phase 2: Agent Configuration (Day 1-2)
**Duration:** 2-3 hours

1. **Update Variables in Each Workflow**

   Lead Nurturing Agent:
   - `YOUR_AGENT_NAME` → Client name
   - `YOUR_AGENT_PHONE` → Client phone
   - `YOUR_GOOGLE_SHEET_ID` → Leads sheet ID
   - Twilio numbers and account SID

   Transaction Coordinator:
   - `YOUR_AGENT_NAME`
   - `YOUR_AGENT_EMAIL`
   - `YOUR_TRANSACTIONS_SHEET_ID`
   - Twilio details

   Client Success:
   - `YOUR_AGENT_NAME`
   - `YOUR_CLIENTS_SHEET_ID`
   - `YOUR_GOOGLE_REVIEW_LINK`
   - `YOUR_ZILLOW_REVIEW_LINK`

   Marketing Automation:
   - `YOUR_AGENT_NAME`
   - `YOUR_BRAND_HASHTAGS`
   - Listings sheet ID

   Orchestrator:
   - All agent webhook URLs
   - All sheet IDs

2. **Update Credential References**
   - Replace all `*_CREDENTIAL_ID` placeholders
   - Test each credential connection

### Phase 3: Integration Setup (Day 2-3)
**Duration:** 3-4 hours

1. **CRM Integration**

   For Follow Up Boss:
   ```
   Webhooks → Add Webhook
   Event: New Lead
   URL: https://client-n8n.com/webhook/new-lead
   ```

   For kvCORE:
   ```
   Settings → API → Webhooks
   Add endpoint for lead events
   ```

   For other CRMs:
   - Check API documentation
   - Set up Zapier bridge if needed

2. **Listing Sync** (if available)
   - MLS API connection
   - Manual webhook for now if no API

3. **Email Deliverability**
   - Configure SPF record
   - Set up DKIM
   - Test with mail-tester.com

### Phase 4: Testing (Day 3-4)
**Duration:** 2-3 hours

1. **Test Each Webhook**
   ```bash
   # Lead Nurturing
   curl -X POST [webhook-url]/new-lead \
     -H "Content-Type: application/json" \
     -d '{"name":"Test Lead","email":"test@test.com","phone":"+15551234567"}'
   ```

2. **Verify Outputs**
   - Check Google Sheets for data
   - Verify emails sent (use test email)
   - Confirm SMS delivery
   - Review AI-generated content quality

3. **Test Scheduled Triggers**
   - Manually trigger each schedule
   - Verify expected outputs
   - Check error logs

### Phase 5: Training (Day 4-5)
**Duration:** 2-3 hours

1. **Agent Dashboard Training**
   - How to read daily dashboard
   - Understanding metrics
   - When to intervene manually

2. **Manual Overrides**
   - How to update lead status
   - How to mark tasks complete
   - How to log client feedback

3. **Common Scenarios**
   - Lead converts to transaction
   - Transaction closes
   - Client leaves review
   - Lead goes cold

4. **Troubleshooting Basics**
   - Checking workflow logs
   - Common error meanings
   - When to contact support

### Phase 6: Go-Live (Day 5)
**Duration:** 1-2 hours

1. **Activate Workflows**
   - Enable Lead Nurturing first
   - Then Transaction Coordinator
   - Then Client Success
   - Then Marketing
   - Finally Orchestrator

2. **Monitor First 24 Hours**
   - Watch for errors
   - Verify real leads processed
   - Check email delivery

3. **Handoff Documentation**
   - Provide all credentials summary
   - Emergency contact info
   - Troubleshooting guide

## Post-Onboarding Support

### Week 1 Check-in
- Review any errors
- Adjust AI prompts if needed
- Fine-tune timing

### Week 2 Check-in
- Review metrics
- Identify optimization opportunities
- Address client questions

### Monthly Review
- Performance metrics review
- ROI calculation
- Identify new automation opportunities

## Template Resources

### Google Sheet Templates
- [Leads Sheet Template](TEMPLATE_LINK)
- [Transactions Sheet Template](TEMPLATE_LINK)
- [Clients Sheet Template](TEMPLATE_LINK)
- [Content Calendar Template](TEMPLATE_LINK)

### Onboarding Documents
- [Client Intake Form](TEMPLATE_LINK)
- [Credentials Worksheet](TEMPLATE_LINK)
- [Training Checklist](TEMPLATE_LINK)
- [Go-Live Checklist](TEMPLATE_LINK)

## Pricing Tiers Quick Reference

### Entry Package Deployment
- Lead Nurturing Agent only
- Basic Google Sheets setup
- 1 CRM integration
- **Onboarding time:** 4-6 hours

### Core Package Deployment
- Lead Nurturing + Transaction Coordinator
- Full Google Sheets setup
- CRM integration + email setup
- Basic training
- **Onboarding time:** 8-12 hours

### Premium Package Deployment
- All 5 agents
- Complete integration suite
- Custom automations
- Comprehensive training
- 30-day support included
- **Onboarding time:** 16-24 hours

## Troubleshooting During Onboarding

### Credential Issues
| Problem | Solution |
|---------|----------|
| OAuth redirect error | Check authorized URIs in Google Console |
| Gmail send failing | Re-authenticate, check app passwords |
| Twilio 401 error | Verify Account SID and Auth Token |
| Sheets permission denied | Share sheet with service account |

### Data Issues
| Problem | Solution |
|---------|----------|
| Leads not appearing | Check webhook URL, verify POST format |
| Duplicate records | Add unique ID check in workflow |
| Date parsing errors | Standardize date format (ISO 8601) |
| AI content empty | Check OpenRouter balance and API key |

### Integration Issues
| Problem | Solution |
|---------|----------|
| CRM webhook not firing | Check CRM webhook settings, test manually |
| Email going to spam | Set up SPF/DKIM, warm up sending |
| SMS not delivering | Check Twilio phone number validity |
| Sheets sync slow | Reduce polling frequency |
