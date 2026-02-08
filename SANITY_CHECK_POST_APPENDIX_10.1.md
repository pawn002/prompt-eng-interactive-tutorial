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
Input Ticket (raw JSON)
    │
    ├──────────────────────────────────┐
    │                                  │
    ▼                                  │
[Step 1: Extract & Classify]  🤖 LLM  │
    │                                  │
    │ → { category, severity,          │
    │     urgency, entities,           │
    │     core_issue,                  │
    │     has_financial_urgency,       │
    │     has_time_constraint }        │
    │                                  │
    ├─────────────────┐                │
    │                 │                │
    ▼                 │                │
[Step 2: Priority     │                │
 Scoring]  ⚙️ Code    │                │
    │                 │                │
    │ → { priority_   │                │
    │     score: 165 }│                │
    │                 │                │
    ▼                 │                │
[Step 3: Routing      │                │
 Rules]  ⚙️ Code      │                │
    │                 │                │
    │ → { routing_    │                │
    │     destination, │               │
    │     cc }         │               │
    │                  │               │
    ▼                  ▼               ▼
[Step 4: Response Generation]  🤖 LLM
    │
    │  ← consumes: original ticket
    │               + Step 1 output
    │               + Step 3 output
    │
    ▼
Output: <draft_response>
```

**Key data flow patterns:**
- **Step 4 reaches back** to both the original input and Step 1, skipping Step 2
- **Step 2's score is fully consumed by Step 3** — no downstream value beyond determining the route
- **Original ticket bypasses the middle pipeline** — empathy requires the customer's raw language, not processed data
- **Two LLM calls bookend two deterministic steps** — hybrid architecture minimizes API costs

---

## Cost Analysis

### Distribution Assumption

The original template assumed a bell curve for priority scores. **This was challenged** — the priority scoring formula uses discrete additive jumps (30, 40, 25, etc.) with a 1.5x multiplier, producing clustered scores, not a smooth normal distribution. Support tickets in practice are **right-skewed** — routine tickets vastly outnumber urgent ones.

**Estimated distribution:**

| Tier | Priority | % of tickets | Model assigned |
|---|---|---|---|
| General Queue | < 60 | ~65% | Haiku ($0.25/$1.25 per M in/out) |
| Standard Support | 60-99 | ~25% | Sonnet ($3/$15 per M in/out) |
| Tier-1 Immediate | >= 100 | ~10% | Opus ($15/$75 per M in/out) |

**Rationale for tiered models:** Highest-priority customers justify the best models — they represent the highest business value and risk.

### 1. Hybrid approach (2 LLM calls + 2 deterministic):

- LLM calls per ticket: **2** (Step 1: Extract & Classify, Step 4: Response Generation)
- Total tokens per ticket: **~1,400 input / ~350 output**

| Tier | Input cost | Output cost | Per ticket |
|---|---|---|---|
| Haiku | $0.00035 | $0.00044 | **$0.0008** |
| Sonnet | $0.0042 | $0.0053 | **$0.0095** |
| Opus | $0.021 | $0.026 | **$0.047** |

### 2. All-LLM approach (5 calls, one per step):

- LLM calls per ticket: **5** (extract, classify, prioritize, route, generate)
- Total tokens per ticket: **~2,650 input / ~650 output**

| Tier | Input cost | Output cost | Per ticket |
|---|---|---|---|
| Haiku | $0.00066 | $0.00081 | **$0.0015** |
| Sonnet | $0.0080 | $0.0098 | **$0.018** |
| Opus | $0.040 | $0.049 | **$0.089** |

### 3. Savings:

**Weighted average per ticket (across distribution):**

| Approach | Weighted cost/ticket | At 100K tickets/month |
|---|---|---|
| Hybrid | **$0.0076** | **$760** |
| All-LLM | **$0.014** | **$1,440** |
| **Savings** | **~47%** | **$680/month** |

**Why:** Deterministic code for Steps 2-3 eliminates 3 LLM calls per ticket. But the savings are not evenly distributed — **Tier-1 tickets drive most of the savings despite being only 10% of volume**. At Opus pricing, the hybrid saves $0.042 per ticket ($0.047 vs $0.089). The most expensive model is exactly where eliminating unnecessary LLM calls matters most.

**Beyond cost — non-monetary savings:**
- **Latency**: 2 API round-trips vs 5
- **Determinism**: Steps 2-3 produce identical results every time — no risk of LLM arithmetic errors or invented routing rules
- **Debuggability**: When routing is wrong, you check the code, not guess what the LLM was thinking

---

## Reflection Questions

### Question 1: When to Use LLM vs Code?
Based on this exercise, what heuristic would you use to decide "LLM or code?" for a given step?

**Your answer:**

**Determinism is the heuristic.** Do I need a specific, computable result (deterministic → code), or do I need a qualitative result that requires human-like reasoning and judgment (non-deterministic → LLM)?

- **Deterministic** (one correct answer, computable): Steps 2-3 → code
- **Non-deterministic** (requires interpretation, judgment, generation): Steps 1, 4 → LLM

The key qualifier is "qualitative" — it's not just "varied output" (a random number generator does that). It's that the task requires **human-like judgment** to produce a good result, and "good" can't be reduced to a formula.

---

### Question 2: Chaining Tradeoffs
What are the tradeoffs of breaking this into multiple steps versus trying to do it all in one large prompt?

**Your answer:**

**For chaining (multiple steps):**
- **Debuggability**: A large prompt has many interacting elements creating a vast number of costly permutations to test. Isolating failures in a single-responsibility step is straightforward.
- **Human comprehension**: Large prompts fail the golden guidance of "can a human understand it?" — humans understand short sets of guidance and struggle with long ones. LLMs behave similarly.
- **Testability**: Each step can be tested independently with manageable permutations.

**Against chaining (single prompt):**
- **Token efficiency**: Single prompt avoids repeating context across multiple calls.
- **Developer experience**: Similar to splitting code across multiple files — all the "code" for an operation isn't in front of you at once.
- **Error propagation**: This is the critical tradeoff. In a chain, each step's mistakes feed into the next silently. If Step 1 misclassifies a ticket, Steps 2-4 all operate on wrong data. In a single prompt, the LLM has full context and might self-correct — while generating routing info it could "notice" an inconsistency with its earlier classification.

**Latency** is also a real cost of chaining. Drawing from accessibility experience with real-time communications: high latency is the primary factor in compromised RTC UX. The same principle applies here — each sequential API call is dead air for whoever is waiting. The person waiting doesn't care about your architecture, they care that nothing is happening.

**Mitigation applied in this design:** Passing the original ticket body directly to Step 4 (bypassing the middle pipeline) partially addresses error propagation — if Step 1 missed a nuance, Step 4 still has the raw source.

| | Chaining | Single prompt |
|---|---|---|
| Debuggability | Easy — isolate the failing step | Hard — which part went wrong? |
| Testing | Manageable permutations per step | Combinatorial explosion |
| Error propagation | Compounds through steps | Self-correcting (full context) |
| Token cost | Higher (repeated context) | Lower |
| Latency | Multiple round-trips | Single round-trip |

---

### Question 3: Prompt Technique Synthesis
Which techniques from Chapters 1-9 were most critical for this task, and why?

**Your answer:**

**#1 most critical: Anti-hallucination (Ch 8)** — specifically the context-adapted modification. The other techniques (role, data separation, output formatting) improve response quality. Anti-hallucination **prevents catastrophic failure**. There's a severity hierarchy: "this response could be warmer" vs "we just promised a customer something we can't deliver."

Critically, Ch 8 was not applied out of the box — it was adapted. "If you don't know, say I don't know" became "If you don't know, escalate to a specialist." This required understanding *why* the technique exists (prevent confident fabrication), not just *how* to apply it (add an escape hatch). The modification preserves customer momentum toward resolution instead of leaving them stranded.

**Also critical:**
- **Ch 3 (Role Prompting)**: Defined the CSR persona's tone and investment level
- **Ch 4 (Data Separation)**: XML tags prevented prompt injection and cleanly separated three data sources in Step 4
- **Ch 5 (Output Formatting + Prefilling)**: Forced structured JSON in Step 1 and `<draft_response>` format in Step 4

All techniques from Chapters 1-9 were useful, but the severity hierarchy matters: some improve quality, one prevents disaster.

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

**Observability first:** Before deployment, work with the observability team to ensure the system can catch errors and capture data for identifying edge cases specific to this pipeline. Cost estimates from the analysis need to be validated in production — they are theoretical until real traffic hits the system.

**Latency management:** Each sequential API call is latency the customer feels. The hybrid approach (2 LLM calls vs 5) already mitigates this. Further optimization: deterministic Steps 2-3 execute so fast they're negligible, and could potentially be parallelized with Step 4 prompt assembly.

**Data privacy — the critical gap:**

Support tickets contain PII: customer names, transaction amounts, account history, lifetime value. Every LLM call sends this data to an external API. This intersects with GDPR, SOC2, HIPAA, PCI-DSS, and internal data governance policies.

**Decision framework for model hosting:**
- **API (preferred)**: Higher performance for lower cost. Viable if an API provider is accredited for the compliance framework the organization is beholden to.
- **Self-hosted**: Required only in austere IT environments where data security is the utmost concern. High cost relative to performance — requires stakeholder alignment on the investment.
- **Middle ground — PII scrubbing**: Strip identifiable data before LLM calls (e.g., "Jane Smith" → `[CUSTOMER_1]`), process anonymized text, re-hydrate after. Keeps API cost/performance while reducing PII exposure. But adds engineering complexity, and customer language patterns + situational details can still be identifying even without explicit PII.

**Approach:** Document the risk, technical options, and tradeoffs. Let compliance make the decision — they own the risk tolerance for the organization. Provide them the information to make an informed decision that meets organizational goals. An engineer should not unilaterally decide what's "secure enough."

---

## Success Criteria

You've successfully demonstrated mastery if your solution:

- [x] Uses a hybrid approach (both LLM and deterministic code)
- [x] Clearly justifies each LLM vs code decision
- [x] Applies multiple techniques from Chapters 1-9 appropriately
- [x] Demonstrates cost-conscious architecture
- [x] Handles the business rules deterministically (not via LLM)
- [x] Uses LLM for tasks requiring understanding/reasoning/generation
- [x] Shows understanding of chaining tradeoffs
- [x] Considers production concerns

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

**Status:** Step 1 complete.

---

### Session 2026-02-03 - Step 2 Revisited

**Step 1 Schema Revision:**
- Added `has_financial_urgency` (boolean) and `has_time_constraint` (boolean) to Step 1's output schema
- Reasoning: Step 1's `entities` field is open-ended (LLM decides keys), making deterministic code in Step 2 fragile if it depends on entity keys
- The two flags push urgency/time reasoning into Step 1 (where the LLM is already interpreting the ticket) and give Step 2 a reliable contract to work with
- Updated extraction instructions, good example, and schema in the prompt accordingly

**Step 2: Priority Scoring (Deterministic Code) - COMPLETED**

**Method:** ✅ Deterministic Code

**Justification:**
- Priority scoring formula is fully defined in business rules — no interpretation needed
- Pure arithmetic with threshold checks
- LLM adds cost, latency, and non-determinism for a task that is 100% rule-based

**Implementation (Pseudocode):**
```
score = 0
# Tier points
if customer_tier == "premium": score += 30
elif customer_tier == "standard": score += 20
elif customer_tier == "basic": score += 10

# From Step 1 output (boolean flags)
if has_financial_urgency: score += 40
if has_time_constraint: score += 25

# From customer history
if satisfaction_score >= 4.5: score += 15

# Multiplier applies last (after all additive points)
if lifetime_value > 100000: score *= 1.5

Why not LLM: Formula is fixed, deterministic, and unambiguous. Using an LLM would add cost and introduce risk of arithmetic errors.
```

**Input:** Original ticket data (`customer_tier`, `satisfaction_score`, `lifetime_value`) + Step 1 output (`has_financial_urgency`, `has_time_constraint`)
**Output:** `{ "priority_score": <number> }`

**Sample Walkthrough:** 30 (premium) + 40 (financial) + 25 (time) + 15 (satisfaction ≥ 4.5) = 110, then ×1.5 (lifetime value > 100k) = **165**

**Meta-Learning:** See "Trap: Treating Earlier Steps as Locked" in PROGRESS_LOG.md — initially tried to make Step 2 work with Step 1's open-ended `entities` field instead of revising Step 1's schema.

---

### Session 2026-02-06 - Steps 3 & 4

**Step 3: Routing Rules (Deterministic Code) - COMPLETED**

**Method:** ✅ Deterministic Code

**Justification:**
- Routing is pure if/else logic with no ambiguity
- Inputs are structured data (priority score from Step 2, category/severity from Step 1)
- No interpretation or reasoning required

**Implementation (Pseudocode):**
```
# Determine routing destination
if priority_score >= 100: destination = "Tier-1 Immediate Response"
elif priority_score >= 60: destination = "Standard Support"
else: destination = "General Queue"

# Determine cc targets
cc = []
if category == "billing": cc.append("Finance Team")
if category == "technical" and severity == "critical": cc.append("Engineering Team")

Why not LLM: Exact threshold comparisons and string matching. Zero ambiguity, zero need for reasoning.
```

**Input:** Step 2 output (`priority_score`) + Step 1 output (`category`, `severity`)
**Output:** `{ "routing_destination": "<string>", "cc": [<list of strings>] }`

**Sample Walkthrough:** Priority 165 → "Tier-1 Immediate Response". Category "technical" + severity "critical" → cc ["Engineering Team"].

---

**Step 4: Response Generation (LLM Call) - DESIGN COMPLETED**

**Method:** ✅ LLM Call

**Justification:**
- Generating empathetic, customer-appropriate responses requires natural language understanding and generation
- Must mirror the customer's specific language and emotional state — not templatable
- Deterministic templates cannot adapt to the infinite variety of customer situations

**Design Thinking — Human-Centered Approach:**

The key insight for this step was resisting the instinct to approach it from the data/technical side. Customer responses are about satisfying people, not transmitting data.

**Three core customer needs:**
1. **Feel heard** — response must reflect their specific situation, not generic acknowledgment
2. **Validation** — their frustration/urgency is legitimate and understood
3. **Feel in control** — they know what happens next and that progress is being made

Satisfying these three needs builds **trust**, which manifests as continued customer investment in the organization.

**Data's role is supportive** — it provides factual information (routing destination, what to expect) that empowers the customer to feel in control. But empathy comes first.

**Three input sources (and why each matters):**
1. **Original ticket body** — essential for empathy/mirroring. Step 1's structured extraction tells you *what* the issue is, but the original text tells you *how the customer feels about it*. "I've been trying for 2 hours" and "my team is waiting on me" are the language needed to make someone feel heard. A JSON field `"urgency": "immediate"` cannot do that.
2. **Step 1 output** — for accurate issue characterization (category, severity, core_issue)
3. **Step 3 output** — for routing facts and customer expectations (where their ticket is going, who's involved)

**Techniques Applied:**
- **Ch 3 (Role Prompting)**: Senior CSR with helpful, compassionate tone showing obvious investment in making the customer whole
- **Ch 4 (Data Separation)**: XML tags separating the three data sources (`<original_ticket>`, `<classification>`, `<routing>`)
- **Ch 2 (Clear Instructions)**: Structural direction — acknowledge → validate → inform → empower
- **Ch 7 (Few-Shot Examples)**: Good/bad examples with explicit annotations showing correct and incorrect behavior
- **Ch 5 (Output Formatting + Prefilling)**: `<draft_response>` prefill forces output format
- **Ch 8 (Hallucination Prevention)**: Context-adapted escape hatch (see below)

**Critical: Anti-Hallucination for Customer-Facing Output**

Standard Ch 8 technique: "If you don't know, say you don't know."
Problem: Telling a customer "I don't know" fails their need to feel in control. Leaves them stranded.

**Modified approach**: "If you are uncertain about resolution timelines, available remedies, or any commitment beyond what is explicitly stated in the routing data, do not guess. Instead, indicate that a specialist from [routing_destination] will follow up with specific details."

This constrains the LLM to only state what it can source from the XML-tagged data, and routes everything uncertain to humans. It preserves customer momentum toward resolution.

**Real-world precedent for why this matters:** Airline chatbot incident where bot promised invalid compensation — hallucinated a resolution the company couldn't honor. Exact failure mode this escape hatch prevents.

| Context | Ch 8 Escape Hatch | Why |
|---|---|---|
| Analytical/internal | "Say I don't know" | Accuracy > completeness |
| Customer-facing | "Escalate to human" | Preserves customer's sense of control |

**Few-Shot Example Decision:**
- Included despite risk of templated feel — without examples, more opportunity for inappropriate responses
- **Data flywheel insight**: Day one, ship hand-crafted examples. Over time, high-rated real customer responses replace them. Examples become a living quality benchmark, not a static template.

**Tiered Review Strategy (Production Insight):**
- Original design framed output as "draft for human agent review" — but blanket human review is costly
- Better approach: map review strategy to routing tier

| Route | Review Strategy | Why |
|---|---|---|
| Tier-1 Immediate Response | Human review before send | High-value customer, high stakes |
| Standard Support | Could go either way | Cost/risk tradeoff |
| General Queue | Send directly, audit for QA | Low risk, high volume |

- Prompt should NOT hardcode "will be reviewed by a human" — review strategy is the system's concern, not the LLM's
- Lower tiers use post-hoc auditing; pivot to human review if QA data shows degradation

**Implementation — Final Prompt:**

```
USER:
You are a senior customer service representative known for making
customers feel genuinely heard and supported. Your tone is warm,
professional, and shows clear investment in resolving the customer's
issue.

Your task is to draft a response to a customer support ticket.

<original_ticket>
{ORIGINAL_TICKET_BODY}
</original_ticket>

<classification>
{STEP_1_OUTPUT_JSON}
</classification>

<routing>
{STEP_3_OUTPUT_JSON}
</routing>

Structure your response following this flow:
1. Acknowledge — Reference the customer's specific situation using
   their own language. Show you understand what they're experiencing.
2. Validate — Affirm that their frustration or urgency is legitimate.
   Do not minimize.
3. Inform — Tell them where their ticket has been routed and who will
   be handling it, using only facts from the routing data above.
4. Empower — Give them a clear next step or expectation so they feel
   in control of the process.

<examples>
GOOD EXAMPLE:
Ticket: "I've been trying to log in for 2 hours, getting error 403.
I need to approve a $50k transaction before end of business today."
Routing: Tier-1 Immediate Response, cc: Engineering Team

Response: "I can see you've been locked out for the past two hours
with a 403 error, and I understand the urgency — you have a $50,000
transaction that needs approval today. That is absolutely a priority.
Your ticket has been escalated to our Tier-1 Immediate Response team,
and our Engineering Team has been notified about the 403 error. A
specialist will reach out to you shortly with next steps to restore
your access."

[Why good: References "two hours" and "$50k transaction" from
customer's own words. Validates urgency without minimizing. States
only routing facts. Ends with clear expectation.]

BAD EXAMPLE:
Ticket: Same as above
Response: "Sorry for the inconvenience. We'll have this fixed within
30 minutes. Your password has been reset and you should be able to
log in now."

[Why bad: "30 minutes" is a fabricated timeline. Claims password was
reset — hallucinated action. Generic "sorry for the inconvenience"
doesn't reference the customer's actual situation. No mention of
routing.]
</examples>

CRITICAL: Only state facts explicitly present in the data above. If
you are uncertain about resolution timelines, available remedies, or
any commitment beyond what is explicitly stated in the routing data,
do not guess. Instead, indicate that a specialist from the assigned
team will follow up with specific details.

ASSISTANT: <draft_response>
```

**Input:** Original ticket body + Step 1 output + Step 3 output
**Output:** Draft customer response

**Status:** Step 4 complete (design + prompt).

---

**Remaining Items:**
- [x] ~~Draft actual Step 4 prompt~~
- [x] ~~Architecture diagram~~
- [x] ~~Cost analysis (hybrid vs all-LLM)~~
- [x] ~~Reflection questions (4 questions)~~

**Sanity check complete.** All steps designed, all reflection questions answered, all success criteria met.
