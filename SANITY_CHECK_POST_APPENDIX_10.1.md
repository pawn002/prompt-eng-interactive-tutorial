# Sanity Check: Post-Appendix 10.1 (Chaining Prompts)

**Date Created:** 2026-02-02
**Scope:** Chapters 1-9 + Appendix 10.1
**Focus:** Synthesis of techniques + cost-efficient chaining architecture

---

## Purpose

This exercise tests your ability to:
1. Synthesize prompt engineering techniques from all 9 chapters
2. Apply chaining patterns from Appendix 10.1
3. Demonstrate the hybrid approach (LLM + deterministic code) for cost efficiency
4. Make architectural decisions about when to use LLM vs traditional code

---

## Scenario: Customer Support Ticket Analysis & Routing System

You're building a system to process incoming customer support tickets. The system needs to:

1. **Analyze** each ticket to extract key information
2. **Classify** the ticket into categories
3. **Prioritize** tickets based on urgency and business rules
4. **Route** tickets to appropriate support teams
5. **Generate** a response template for the support agent

### Sample Input Data

```json
{
  "ticket_id": "T-2024-00542",
  "customer_name": "Jane Smith",
  "customer_tier": "premium",
  "subject": "Can't access my account - keep getting error 403",
  "message": "I've been trying to log in for the past 2 hours and keep getting a 403 error. This is urgent because I need to approve a $50k transaction before end of business today. I've tried resetting my password twice but still can't get in. My team is waiting on me!",
  "timestamp": "2024-01-15T14:30:00Z",
  "customer_history": {
    "account_age_days": 1247,
    "lifetime_value": 125000,
    "previous_tickets": 3,
    "satisfaction_score": 4.8
  }
}
```

### Business Rules (Deterministic)

- **Priority scoring formula:**
  - Base score = 0
  - Add 30 points if customer_tier = "premium"
  - Add 20 points if customer_tier = "standard"
  - Add 10 points if customer_tier = "basic"
  - Add 40 points if message contains financial urgency keywords
  - Add 25 points if message mentions time constraints
  - Add 15 points if customer_history.satisfaction_score >= 4.5
  - Multiply by 1.5 if customer_history.lifetime_value > 100000

- **Routing rules:**
  - Priority >= 100: Route to "Tier-1 Immediate Response"
  - Priority 60-99: Route to "Standard Support"
  - Priority < 60: Route to "General Queue"
  - If category = "billing": Also cc "Finance Team"
  - If category = "technical" AND severity = "critical": Also cc "Engineering Team"

---

## Your Task

Design a **cost-efficient multi-step solution** that processes these tickets. For each step:
1. **Specify** whether it should be an LLM call or deterministic code
2. **Justify** your choice (why LLM vs code?)
3. **Write** the prompt (if LLM) or describe the logic (if code)
4. **Identify** which techniques from Chapters 1-9 you're applying

---

## Solution Framework

### Step 1: [Name the step]

**Method:** [ ] LLM Call  OR  [ ] Deterministic Code

**Justification:**
[Why this method? What makes this task suitable/unsuitable for LLM?]

**Implementation:**

*If LLM:*
```
[Write your prompt here, noting which techniques you're using]

Techniques applied:
- Chapter X: [technique name]
- Chapter Y: [technique name]
```

*If Code:*
```
[Describe the deterministic logic or pseudocode]

Why not LLM: [Explain why code is more appropriate]
```

**Input:** [What data goes in?]
**Output:** [What comes out?]

---

### Step 2: [Name the step]

[Repeat the framework above]

---

### Step 3: [Name the step]

[Repeat the framework above]

---

### Step 4: [Name the step]

[Repeat the framework above]

---

### Step 5: [Name the step]

[Repeat the framework above]

---

## Architecture Diagram

```
[Draw or describe the flow of data through your system]

Example:
Input Ticket
    ↓
[Step 1: LLM - Extract & Classify]
    ↓
[Step 2: Code - Calculate Priority Score]
    ↓
[Step 3: Code - Apply Routing Rules]
    ↓
[Step 4: LLM - Generate Response Template]
    ↓
Output: Routed ticket + template
```

---

## Cost Analysis

**Estimate the cost savings of your hybrid approach:**

1. **Your approach:**
   - Number of LLM calls per ticket: ___
   - Estimated tokens per call: ___
   - Estimated cost per ticket: $___

2. **All-LLM approach (everything via prompts):**
   - Number of LLM calls per ticket: ___
   - Estimated tokens per call: ___
   - Estimated cost per ticket: $___

3. **Savings:**
   - Cost reduction: ___%
   - Why: [Explain what you avoided by using deterministic code]

---

## Reflection Questions

### Question 1: When to Use LLM vs Code?
Based on this exercise, what heuristic would you use to decide "LLM or code?" for a given step?

**Your answer:**
[Write your answer here when you do this exercise]

---

### Question 2: Chaining Tradeoffs
What are the tradeoffs of breaking this into multiple steps versus trying to do it all in one large prompt?

**Your answer:**
[Write your answer here when you do this exercise]

---

### Question 3: Prompt Technique Synthesis
Which techniques from Chapters 1-9 were most critical for this task, and why?

**Your answer:**
[Write your answer here when you do this exercise]

---

### Question 4: Production Considerations
What additional considerations would you need to address to make this production-ready?

Think about:
- Error handling
- Edge cases
- Monitoring
- Hallucination prevention
- Performance/latency
- Data privacy

**Your answer:**
[Write your answer here when you do this exercise]

---

## Success Criteria

You've successfully demonstrated mastery if your solution:

- [ ] Uses a hybrid approach (both LLM and deterministic code)
- [ ] Clearly justifies each LLM vs code decision
- [ ] Applies multiple techniques from Chapters 1-9 appropriately
- [ ] Demonstrates cost-conscious architecture
- [ ] Handles the business rules deterministically (not via LLM)
- [ ] Uses LLM for tasks requiring understanding/reasoning/generation
- [ ] Shows understanding of chaining tradeoffs
- [ ] Considers production concerns

---

## Notes

### Session 2026-02-02 - In Progress

**Architecture Decision Made:**
- Using hybrid approach: 2 LLM calls + deterministic code
- Batching extraction + classification into single LLM call for cost efficiency
- Plan to validate empirically and pivot if needed

**Step 1: Extract & Classify (LLM Call) - COMPLETED**

**Method:** ✅ LLM Call

**Justification:**
- Requires natural language understanding to extract key entities
- Requires reasoning to classify tickets into categories
- Must interpret urgency signals and severity from unstructured text
- Deterministic code cannot handle the variability of customer language

**Implementation:**

```
USER:
You are a senior customer service representative with expertise in ticket classification and triage.

Your task is to analyze a customer support ticket and extract key information. Think step by step through the ticket content.

<valid_categories>
- billing
- technical
- account
- shipping
- general
</valid_categories>

<valid_severity_levels>
- critical
- high
- medium
- low
</valid_severity_levels>

<valid_urgency_indicators>
- immediate (customer states explicit time constraint)
- urgent (financial impact or business blocking)
- normal (standard request)
</valid_urgency_indicators>

<user_ticket>
{TICKET_DATA_GOES_HERE}
</user_ticket>

Extract the following from the ticket:
1. Primary category (choose one from valid_categories)
2. Severity level (choose one from valid_severity_levels)
3. Urgency indicator (choose one from valid_urgency_indicators)
4. Key entities (customer name, error codes, transaction amounts, etc.)
5. Core issue (one sentence summary)
6. Whether the ticket contains financial urgency (e.g. pending transactions, payment issues, revenue impact)
7. Whether the ticket contains a time constraint (e.g. deadlines, end of day, time-sensitive requests)

If you cannot accurately classify the ticket or key information is missing, set "needs_manual_review" to true and explain why in "review_reason".

<examples>
GOOD EXAMPLE:
Ticket: "I can't log in, getting error 403. Need to approve $50k transaction by 5pm today!"
Output: {"category": "technical", "severity": "high", "urgency": "immediate", "entities": {"error_code": "403", "transaction_amount": 50000, "deadline": "5pm today"}, "core_issue": "Login blocked by 403 error preventing time-sensitive transaction", "has_financial_urgency": true, "has_time_constraint": true, "needs_manual_review": false}

BAD EXAMPLE:
Ticket: "I can't log in, getting error 403. Need to approve $50k transaction by 5pm today!"
Output: {"category": "urgent_billing_technical", "severity": "super urgent", ...}
[Why bad: Invalid category (not from list), invalid severity (not from list)]
</examples>

<json_schema>
{
  "category": "string (from valid_categories)",
  "severity": "string (from valid_severity_levels)",
  "urgency": "string (from valid_urgency_indicators)",
  "entities": "object (key-value pairs of extracted entities)",
  "core_issue": "string (one sentence)",
  "has_financial_urgency": "boolean (true if ticket involves financial urgency)",
  "has_time_constraint": "boolean (true if ticket contains a time constraint)",
  "needs_manual_review": "boolean",
  "review_reason": "string (only if needs_manual_review is true)"
}
</json_schema>

Return only valid JSON matching this schema.

ASSISTANT: {
```

**Techniques Applied:**
- **Ch 3 (Role Prompting)**: "senior customer service representative with expertise in ticket classification"
- **Ch 4 (Data Separation)**: XML tags `<user_ticket>`, `<valid_categories>`, etc. to prevent prompt injection
- **Ch 2 (Clear Instructions)**: Explicit numbered list of what to extract
- **Ch 6 (Chain of Thought)**: "Think step by step through the ticket content"
- **Ch 8 (Avoiding Hallucinations)**: "If you cannot accurately classify... set needs_manual_review to true" - giving Claude a way out
- **Ch 7 (Few-Shot)**: Good and bad examples demonstrating correct output format
- **Ch 5 (Output Formatting + Prefilling)**: JSON schema specification + prefilling with `{` to force JSON start

**Input:** Raw ticket data (JSON object)
**Output:** Structured extraction (JSON with category, severity, urgency, entities, core_issue)

**Key Insight - Positional Context & Implicit Referencing:**
- LLMs process entire prompt in context-loading phase before generation
- Proximity and structure create implicit references - no need for verbose connectors
- "Extract the following from the ticket:" works because ticket data was just shown
- Efficient prompts use structure over verbosity
- Explicit references only needed when: multiple data sources, complex multi-step tasks, ambiguous ordering

**Status:** Step 1 complete. Ready to continue with Steps 2-4 when resuming.

---

**Ready to review?** When you've completed this exercise, we can discuss your solution and identify any gaps or areas for improvement.
