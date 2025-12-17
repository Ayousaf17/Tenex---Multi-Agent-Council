# Aasani Real Estate Automation Workflows

A complete n8n-based automation system for real estate agents, designed to address the key pain points identified in the Aasani Systems research.

## System Overview

```
                    +------------------------+
                    |   Transaction          |
                    |   Orchestrator         |
                    |   (Central Hub)        |
                    +------------------------+
                              |
        +---------------------+---------------------+
        |                     |                     |
        v                     v                     v
+---------------+    +------------------+    +----------------+
| Lead          |    | Transaction      |    | Client         |
| Nurturing     |--->| Coordinator      |--->| Success        |
| Agent         |    | Agent            |    | Agent          |
+---------------+    +------------------+    +----------------+
        |                     |                     |
        v                     v                     v
+---------------+    +------------------+    +----------------+
| 100x conv.    |    | 30 hrs/tx saved  |    | 66% referral   |
| improvement   |    | 25% productivity |    | rate capture   |
+---------------+    +------------------+    +----------------+

                    +------------------------+
                    |   Marketing            |
                    |   Automation Agent     |
                    |   (Brand Visibility)   |
                    +------------------------+
```

## Workflow Files

| Workflow | Purpose | Key Metrics |
|----------|---------|-------------|
| `re_lead_nurturing_agent.json` | Instant response & drip campaigns | 5-min response, 100x conversion |
| `re_transaction_coordinator_agent.json` | Deadline tracking & task management | 30 hrs/tx automated |
| `re_client_success_agent.json` | Post-close nurturing & referrals | 66% referral capture |
| `re_marketing_automation_agent.json` | Listing promotion & brand visibility | 75% time savings |
| `re_transaction_orchestrator.json` | Central hub & cross-agent coordination | Unified dashboard |
| `aasani_doodle_factory_v2.json` | Creative content generation | Design automation |
| `aasani_doodle_reel_generator.json` | Video/reel content creation | Social media content |

## Quick Start

### 1. Prerequisites
- n8n instance (self-hosted or cloud)
- API credentials (see [Credentials Guide](./docs/CREDENTIALS.md))
- Google Sheets for data storage

### 2. Import Workflows
```bash
# In n8n, import each JSON file via:
# Settings > Import from File
```

### 3. Configure Credentials
See individual playbooks in `./docs/playbooks/` for each workflow.

### 4. Test Webhooks
```bash
# Test Lead Nurturing
curl -X POST https://your-n8n-instance/webhook/new-lead \
  -H "Content-Type: application/json" \
  -d '{"name": "Test Lead", "email": "test@example.com", "phone": "+15551234567"}'
```

## Webhook Endpoints

| Agent | Endpoint | Purpose |
|-------|----------|---------|
| Lead Nurturing | `POST /webhook/new-lead` | New lead intake |
| Transaction Coordinator | `POST /webhook/new-transaction` | New contract |
| Transaction Coordinator | `POST /webhook/update-task` | Task status update |
| Marketing | `POST /webhook/new-listing` | New listing |
| Marketing | `POST /webhook/listing-update` | Price/status change |
| Marketing | `POST /webhook/just-sold` | Sold notification |
| Client Success | `POST /webhook/new-client` | Post-close client |
| Orchestrator | `POST /webhook/orchestrator/lead-converted` | Lead → Transaction |
| Orchestrator | `POST /webhook/orchestrator/transaction-closed` | Tx → Client Success |

## Scheduled Automations

| Agent | Schedule | Action |
|-------|----------|--------|
| Lead Nurturing | Hourly | Drip campaign check |
| Lead Nurturing | Daily | Cold lead re-engagement |
| Transaction Coordinator | Every 6 hours | Deadline alerts |
| Transaction Coordinator | Daily 8am | Transaction summary |
| Client Success | Daily 9am | Anniversary/birthday check |
| Client Success | Daily 10am | Review request follow-up |
| Client Success | Weekly Monday | Sphere nurturing |
| Marketing | Weekly Monday | Market update |
| Orchestrator | Daily 7am | Dashboard generation |
| Orchestrator | Weekly Friday 4pm | Performance report |

## Integration Map

```
CRM (Follow Up Boss, kvCORE, etc.)
    ↓ Webhook
Lead Nurturing Agent
    ↓ Converts to Transaction
Transaction Coordinator Agent
    ↓ Transaction Closes
Client Success Agent
    ↓ Generates Referrals → New Leads

Listing Management System
    ↓ Webhook
Marketing Automation Agent
    ↓ Social Posts, Email Campaigns
```

## Service Package Alignment

These workflows support the Aasani Systems consulting packages:

### Entry Package ($497-997) - Workflow Foundation Audit
- Review existing CRM setup
- Map current workflow gaps
- Identify which agents to deploy

### Core Package ($2,997-4,997) - Workflow Foundation Build
- Deploy Lead Nurturing Agent
- Deploy Transaction Coordinator Agent
- Basic Google Sheets setup
- Training & documentation

### Premium Package ($7,997-12,000) - Systems Transformation
- Deploy all 5 agents
- Full CRM integration
- Custom automations
- 60-90 day implementation support

### Monthly Retainer ($497-997/month) - Systems Support
- Ongoing optimization
- New automation builds
- Performance monitoring

## Documentation

- [Credentials Guide](./docs/CREDENTIALS.md)
- [Lead Nurturing Playbook](./docs/playbooks/lead-nurturing.md)
- [Transaction Coordinator Playbook](./docs/playbooks/transaction-coordinator.md)
- [Client Success Playbook](./docs/playbooks/client-success.md)
- [Marketing Automation Playbook](./docs/playbooks/marketing-automation.md)
- [Orchestrator Playbook](./docs/playbooks/orchestrator.md)
- [Client Onboarding Guide](./docs/ONBOARDING.md)
