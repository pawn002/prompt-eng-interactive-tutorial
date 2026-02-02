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

Use this space for any additional thoughts, questions, or insights as you work through this exercise.

[Your notes here]

---

**Ready to review?** When you've completed this exercise, we can discuss your solution and identify any gaps or areas for improvement.
