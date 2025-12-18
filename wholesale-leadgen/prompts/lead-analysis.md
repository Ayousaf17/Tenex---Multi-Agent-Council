# Lead Analysis Prompt Templates

Prompts for OpenRouter API calls to analyze wholesale real estate leads.

---

## 1. Initial Lead Analysis (After Enrichment)

**Use when:** New lead with property data and contact info

```
Analyze this property for wholesale real estate deal potential:

Property Details:
- Address: {{address}}
- Asking Price: ${{asking_price}}
- Estimated Value (Zestimate): ${{estimated_value}}
- Beds/Baths: {{beds}}/{{baths}}
- Square Feet: {{sqft}}
- Year Built: {{year_built}}
- Property Type: {{property_type}}

Owner Info:
- Name: {{owner_name}}
- Phone: {{owner_phone}}
- Email: {{owner_email}}

Source: {{lead_source}}
Description: {{description}}

Analyze for wholesale potential:
1. Is the asking price below market value? By how much?
2. What's the potential wholesale margin?
3. What repair/rehab costs might be needed?
4. What's the estimated ARV (After Repair Value)?
5. What exit strategy makes sense? (wholesale, fix-flip, rental)
6. What motivation signals do you see?
7. Rate overall deal score (0-100)
8. What questions should we ask the seller?

Return ONLY valid JSON:
{
  "deal_score": 0-100,
  "wholesale_margin_estimate": number_or_null,
  "below_market_percent": number_or_null,
  "estimated_arv": number_or_null,
  "estimated_repairs": number_or_null,
  "exit_strategy": "wholesale|fix-flip|rental|hold",
  "motivation_signals": ["list"],
  "red_flags": ["list"],
  "questions_to_ask": ["list"],
  "summary": "2-3 sentence summary",
  "priority": "high|medium|low"
}
```

---

## 2. Craigslist/FSBO Listing Legitimacy Check

**Use when:** Scraping new listings to filter spam/scams

```
Analyze this Craigslist real estate listing for legitimacy and deal potential:

Title: {{title}}
Description: {{description}}
Price: ${{price}}
Location: {{location}}

Determine:
1. Is this a legitimate property listing? (not spam, scam, or agent listing)
2. Is the price reasonable for a real property?
3. Does the description indicate motivated seller?
4. Extract the property address if mentioned
5. Rate deal potential 1-100

Return ONLY valid JSON:
{
  "is_legitimate": true/false,
  "confidence": 0-100,
  "deal_score": 0-100,
  "extracted_address": "address or null",
  "motivation_signals": ["list", "of", "signals"],
  "red_flags": ["list", "of", "concerns"],
  "reasoning": "brief explanation"
}
```

---

## 3. Personalized Email Generation

**Use when:** Creating outreach emails for new leads

**System prompt:**
```
You are an expert real estate investor writing personalized emails to property owners. Your tone is friendly, professional, and NOT salesy. You write conversational emails that get responses. Never use phrases like "I hope this email finds you well" or generic openers. Be direct but warm.
```

**User prompt:**
```
Write a personalized email to a property owner:

Property Details:
- Address: {{address}}
- Listed Price: ${{asking_price}}
- Source: {{source}} (FSBO/Expired/etc)
- Property Type: {{property_type}}

Owner Name: {{owner_name}}

My Context:
- I'm a local real estate investor
- I buy properties for cash and can close quickly
- I'm open to creative deals (subject-to, seller financing)
- I never lowball - I make fair offers

AI Analysis of this property:
{{ai_analysis}}

Write an email that:
1. References the specific property naturally
2. Shows I did research (mention something specific)
3. Asks if they'd be open to a conversation about their situation
4. Is under 100 words
5. Ends with a simple yes/no question to prompt response
6. Doesn't mention specific dollar amounts or offers

Return ONLY valid JSON:
{
  "subject": "Subject line (max 6 words, no clickbait)",
  "body": "Email body text"
}
```

---

## 4. Follow-up Email Generation

**Use when:** Following up on leads that haven't responded

```
Write follow-up email #{{sequence_number}} in a sequence:

Previous email sent: {{previous_email}}
Days since: {{days_since}}
Property: {{address}}
Owner: {{owner_name}}

This follow-up should:
1. Reference previous email naturally
2. Add new angle (e.g., mention similar deal you closed)
3. Provide value (market insight, free property analysis)
4. Stay non-pushy
5. Make it easy to reply

Max 80 words.

Return ONLY valid JSON:
{
  "subject": "Subject line",
  "body": "Email body"
}
```

---

## 5. Response Analysis (Critical)

**Use when:** Analyzing seller responses to determine interest level

**System prompt:**
```
You are an expert at analyzing seller responses for wholesale real estate deals. Your job is to determine seller motivation, qualify leads, and recommend next actions. Be accurate and conservative in your assessments - only mark as HOT if there are clear buying signals.
```

**User prompt:**
```
Analyze this seller's email response:

Context:
- Property: {{address}}
- Asking Price: ${{asking_price}}
- Our previous outreach: {{previous_email_subject}}
- Number of emails we've sent: {{emails_sent}}

Their Response:
Subject: {{response_subject}}
Body: {{response_body}}

Determine:
1. Interest Level: COLD (not interested/hostile), WARM (curious/open to talking), HOT (ready to discuss seriously/motivated)
2. Key Information Provided (price expectations, timeline, motivation, etc.)
3. Motivation Signals (urgency, financial stress, life changes, etc.)
4. Objections or Concerns raised
5. Recommended Next Action
6. Suggested Response (if WARM or HOT)

Return ONLY valid JSON:
{
  "interest_level": "COLD|WARM|HOT",
  "motivation_score": 0-100,
  "sentiment": "positive|neutral|negative",
  "key_info_extracted": {
    "price_expectation": "text or null",
    "timeline": "text or null",
    "reason_for_selling": "text or null",
    "mortgage_status": "text or null",
    "creative_finance_openness": "yes|no|maybe|unknown"
  },
  "motivation_signals": ["list of signals"],
  "objections": ["list of objections"],
  "red_flags": ["list of concerns"],
  "next_action": "specific action to take",
  "suggested_response": "email response text if WARM/HOT, null if COLD",
  "call_readiness": "ready_now|needs_nurturing|not_ready",
  "summary": "2-3 sentence summary for quick review"
}
```

---

## 6. Hot Lead Call Brief Generator

**Use when:** Preparing for a call with a HOT lead

```
Create a pre-call brief for this hot lead:

Property Details:
- Address: {{address}}
- Asking Price: ${{asking_price}}
- Estimated Value: ${{estimated_value}}
- Condition: {{condition}}

Owner Info:
- Name: {{owner_name}}
- Phone: {{owner_phone}}

Conversation History:
{{conversation_history}}

AI Analysis:
{{ai_analysis}}

Generate a brief covering:

1. **Why They're Selling** (based on conversations)
2. **What They Want** (price expectations, timeline)
3. **Why This Is a Deal** (equity, motivation, opportunity)
4. **Recommended Approach** (talking points for call)
5. **Questions to Ask** (to qualify further)
6. **Potential Objections** (and how to handle)
7. **Creative Finance Angles** (if applicable)

Format as a clean, scannable document you can review in 2 minutes before calling.
```

---

## 7. Photo Analysis (Vision Model)

**Use when:** Analyzing property photos for condition assessment

```
Analyze these property photos and rate:

1. Exterior condition (1-10)
2. Interior condition (1-10) if visible
3. Landscaping quality (1-10)
4. Visible issues (roof, foundation, windows, siding, etc.)
5. Curb appeal score (1-10)
6. Estimated renovation level needed (cosmetic/moderate/major/gut)
7. Estimated renovation budget needed

Return JSON with scores and list of visible issues:
{
  "exterior_score": 1-10,
  "interior_score": 1-10 or null,
  "landscaping_score": 1-10,
  "curb_appeal": 1-10,
  "overall_condition": "excellent|good|fair|poor",
  "renovation_level": "cosmetic|moderate|major|gut",
  "estimated_repairs": dollar_amount,
  "visible_issues": ["list", "of", "issues"],
  "notes": "Additional observations"
}
```

---

## 8. SMS Generation

**Use when:** Creating text messages for outreach

```
Write a text message (SMS) to property owner:

Property: {{address}}
Context: {{why_reaching_out}}
Owner Name: {{owner_name}}

SMS should:
1. Be under 160 characters
2. Introduce yourself briefly
3. Ask if they're open to discussing sale
4. Include your name and callback number

Tone: Friendly neighbor, not business

Return ONLY the SMS text, nothing else.
```

---

## Model Recommendations

| Prompt Type | Recommended Model | Reason |
|-------------|------------------|--------|
| Lead Analysis | claude-3.5-sonnet | Best reasoning |
| Legitimacy Check | claude-3-haiku | Fast, cheap, sufficient |
| Email Generation | claude-3.5-sonnet | Creative, natural writing |
| Response Analysis | claude-3.5-sonnet | Critical accuracy needed |
| Call Brief | claude-3.5-sonnet | Comprehensive analysis |
| Photo Analysis | claude-3.5-sonnet | Vision capability |
| SMS Generation | claude-3-haiku | Simple task |

---

## Cost Estimates (per operation)

| Operation | Tokens (approx) | Cost (Claude 3.5 Sonnet) |
|-----------|----------------|-------------------------|
| Lead Analysis | ~1,500 | ~$0.015 |
| Legitimacy Check | ~800 | ~$0.008 |
| Email Generation | ~800 | ~$0.008 |
| Response Analysis | ~1,200 | ~$0.012 |
| Call Brief | ~2,000 | ~$0.020 |
| Photo Analysis | ~1,000 + image | ~$0.025 |

**Total per lead (full cycle):** ~$0.03-0.05
