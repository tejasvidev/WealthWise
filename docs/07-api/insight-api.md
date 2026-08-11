
---

# FILE 2 — `docs/07-api/insight-api.md`

```markdown
# WealthWise — Insight API

**Document Version:** 1.0  
**Status:** API Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**API Style:** REST  
**API Version:** v1  

---

# 1. Purpose

This document defines the API contract for financial insights in WealthWise.

Insights are one of the key differences between WealthWise and a conventional expense tracker.

They convert financial signals into understandable observations that can help the user recognize:

- spending changes,
- unusual behaviour,
- budget risks,
- goal risks,
- savings opportunities,
- recurring patterns,
- and other financially meaningful events.

---

# 2. Insight Principle

The core pipeline is:

```text
Raw Financial Data
        ↓
Analytics
        ↓
Behaviour Analysis
        ↓
Financial Signal
        ↓
Insight Generation
        ↓
Insight
        ↓
User

3. Insight Is Not Raw Data

Example:

Transaction:
Food → ₹1,200

is raw financial data.

Food spending increased 24% this month.

is an analytical observation.

Your food spending is significantly above your recent pattern.

is an insight.

4. Insight Endpoints

Initial endpoints:

Method	Endpoint	Purpose
GET	/api/v1/insights	List insights
GET	/api/v1/insights/:id	Retrieve insight
PATCH	/api/v1/insights/:id	Update insight state

Insight creation is generally an internal intelligence operation rather than a public CRUD endpoint.

5. Authentication

All insight endpoints require authentication.

Request
   ↓
Authentication
   ↓
Authenticated User
   ↓
Insight Service
6. Why Insight Creation Is Internal

The frontend should not normally create arbitrary financial insights.

Instead:

Financial Data
      ↓
Intelligence Engine
      ↓
Insight
      ↓
Insight Repository

This preserves consistency and trust in the insight system.

7. List Insights
Endpoint
GET /api/v1/insights

Returns insights belonging to the authenticated user.

8. Insight Filters

Supported filters may include:

status
severity
type
category
date range

Example:

GET /api/v1/insights?status=NEW
9. Insight Status

Initial statuses:

NEW
READ
DISMISSED
ACTIONED

These describe the user's interaction with the insight.

10. Insight Severity

Initial levels:

INFO
LOW
MEDIUM
HIGH

Severity represents how important the insight may be.

It does not mean the system is predicting a financial disaster.

11. Insight Types

Initial conceptual types:

SPENDING_INCREASE
SPENDING_DECREASE
UNUSUAL_EXPENSE
CATEGORY_SPIKE
RECURRING_EXPENSE
BUDGET_WARNING
BUDGET_EXCEEDED
GOAL_RISK
SAVINGS_OPPORTUNITY
SAVINGS_CHANGE

The taxonomy may expand as WealthWise intelligence develops.

12. Insight Categories

An insight may be associated with a financial category.

Example:

category = Food

or:

category = null

for broader financial insights.

13. Insight List Response

Example:

{
  "success": true,
  "data": [
    {
      "id": "insight-id",
      "type": "CATEGORY_SPIKE",
      "severity": "MEDIUM",
      "title": "Food spending increased",
      "message": "Your food spending is higher than your recent average.",
      "category": "Food",
      "status": "NEW",
      "createdAt": "2026-08-10T10:00:00Z"
    }
  ]
}
14. Insight Content

An insight may contain:

title
message
type
severity
category
supporting data
status
createdAt
expiresAt

The exact schema is defined by the Insight domain.

15. Human-Readable Message

The API may expose a human-readable message.

Example:

Your shopping spending increased by 32% compared with your recent average.

This can be generated through:

Rule-based templates

or:

AI-assisted generation

depending on the insight type.

16. Insight Evidence

Insights should ideally retain structured evidence.

Example:

{
  "baselineAmount": 4000,
  "currentAmount": 5280,
  "changePercentage": 32
}

This allows the system to explain why an insight exists.

17. Evidence Principle

The insight should be traceable to measurable financial data.

Conceptually:

Insight
   ↓
Evidence
   ↓
Analytics
   ↓
Transactions

This improves:

transparency,
debugging,
explainability,
AI grounding.
18. Get Single Insight
Endpoint
GET /api/v1/insights/:id

Returns one insight belonging to the authenticated user.

19. Insight Not Found

If the insight does not exist or belongs to another user:

HTTP 404 Not Found

Example:

{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Insight not found."
  }
}
20. Update Insight
Endpoint
PATCH /api/v1/insights/:id

This endpoint primarily manages user interaction with an insight.

21. Editable Insight Fields

The client may update state-related fields such as:

status

The client must not arbitrarily modify:

severity
type
evidence
createdAt

because these are intelligence-generated fields.

22. Mark as Read

Example:

{
  "status": "READ"
}

The backend validates the transition.

23. Dismiss Insight

Example:

{
  "status": "DISMISSED"
}

Dismissal indicates that the user does not want the insight to remain active in their main insight view.

It does not necessarily delete the historical record.

24. Actioned Insight

Example:

{
  "status": "ACTIONED"
}

This may be used when the user has acted on the recommendation or issue represented by the insight.

The exact semantics will be finalized in the product behaviour specification.

25. Insight Lifecycle
                 GENERATED
                     │
                     ▼
                    NEW
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
        READ      DISMISSED   ACTIONED
          │
          ▼
       ACTIONED
26. Insight Generation

Insight creation occurs through the Intelligence Engine.

Conceptually:

Transactions
      ↓
Analytics
      ↓
Behaviour Signals
      ↓
Insight Rules
      ↓
Insight Candidate
      ↓
Validation
      ↓
Insight Repository
27. AI-Assisted Insight Generation

Some insights may use AI for language generation.

The architecture is:

Structured Signal
       ↓
Insight Context
       ↓
AI Prompt
       ↓
Generated Explanation
       ↓
Validation
       ↓
Insight

The AI must not invent the underlying financial facts.

28. AI Grounding Rule

If structured evidence says:

Food spending:
₹5,280

Baseline:
₹4,000

the generated insight must remain grounded in those values.

The AI should not claim:

"You spent ₹8,000 on food"

when the evidence does not support it.

29. Insight Evidence and AI

The preferred architecture is:

Analytics
    ↓
Structured Evidence
    ↓
AI
    ↓
Natural Language

rather than:

Raw Transactions
    ↓
AI
    ↓
Financial Fact
30. Insight Deduplication

The system should avoid generating the same insight repeatedly for the same underlying event.

A conceptual deduplication key may contain:

userId
insightType
category
period
signal

The exact strategy will be defined during intelligence implementation.

31. Insight Expiration

Insights may contain:

expiresAt

This allows temporary insights to become inactive without necessarily being deleted.

Example:

Budget Warning

may no longer be relevant after the budget period ends.

32. Insight Freshness

An insight should remain useful.

The system should avoid repeatedly showing:

the same observation

without meaningful new evidence.

33. Insight Priority

Insight priority may depend on:

severity
financial impact
goal relevance
budget relevance
recency

The final ranking algorithm belongs to the Intelligence Engine.

34. Insight List Ordering

Default ordering should prioritize:

High relevance
        ↓
High severity
        ↓
Recent insights

The exact ranking formula will be defined separately.

35. Insight and Dashboard

The dashboard may display a small number of important insights.

Example:

Dashboard
   ↓
GET /api/v1/insights
   ↓
Important active insights

The dashboard should not display every historical insight by default.

36. Insight and Analytics

Analytics provide the measurable evidence.

Example:

Analytics:
Food spending +24%

The Insight Engine may convert it into:

Insight:
Food spending is significantly higher than usual.
37. Insight and Budgets

Budget signals may produce:

BUDGET_WARNING
BUDGET_EXCEEDED

Example:

Food Budget:
₹6,000

Current Spending:
₹5,700

Usage:
95%

Possible insight:

You are close to reaching your Food budget this month.

38. Insight and Goals

Goal signals may produce:

GOAL_RISK

Example:

Goal:
₹100,000

Required monthly saving:
₹10,000

Historical average:
₹6,000

Potential insight:

Your current savings pace may not be sufficient to reach this goal on schedule.

39. Insight and Savings

Savings behaviour may produce:

SAVINGS_CHANGE
SAVINGS_OPPORTUNITY

Example:

Savings decreased by 18% compared with the previous month.

The underlying evidence remains structured.

40. Insight and Unusual Spending

An unusual expense may be detected using:

Historical amount
Category baseline
Merchant behaviour
Frequency
Recent spending

The Insight Engine determines whether the anomaly is meaningful enough to surface.

41. Insight API Security

Every insight query must be scoped to:

authenticatedUserId

Correct:

{
  _id: insightId,
  userId: authenticatedUserId
}
42. Insight Error Codes

Initial insight-specific errors:

INSIGHT_NOT_FOUND
INVALID_INSIGHT_STATUS
INVALID_INSIGHT_UPDATE
43. Empty Insight State

If no insights are currently available:

{
  "success": true,
  "data": []
}

This is a valid state rather than an error.

44. Insight Privacy

Insights may contain sensitive financial observations.

Therefore they must not be:

public
shared by default
included in logs
exposed across users
45. Insight API and AI Advisor

The AI Advisor may use existing insights as context.

Example:

User:
"What should I worry about financially?"

Advisor Context:
- High Food spending
- Goal risk
- Budget warning

The AI can synthesize these signals into an explanation.

46. Insight API and Scenario Engine

Scenarios may generate hypothetical signals, but hypothetical results should not automatically become permanent insights.

Example:

Scenario:
Reduce shopping by 20%

Result:
Projected savings increase

This is a scenario result.

It is not an actual behavioural insight until the user's real behaviour supports it.

47. Insight Creation Boundary

The public API exposes:

READ
UPDATE USER STATE

The Intelligence Engine owns:

CREATE
CLASSIFY
SCORE
GENERATE
DEDUPLICATE
EXPIRE
48. Insight Architecture
                    FINANCIAL DATA
                          │
                          ▼
                     ANALYTICS
                          │
                          ▼
                 BEHAVIOUR ENGINE
                          │
                          ▼
                  SIGNAL DETECTION
                          │
                          ▼
                  INSIGHT ENGINE
                          │
               ┌──────────┴──────────┐
               ▼                     ▼
        Rule-Based Insight      AI Explanation
               │                     │
               └──────────┬──────────┘
                          ▼
                       INSIGHT
                          │
                          ▼
                   Insight API
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
          Dashboard      User      AI Advisor
49. Insight Trust Model

WealthWise follows:

Financial Fact
      ↓
Structured Evidence
      ↓
Interpretation
      ↓
Natural Language

The closer information is to raw financial data, the more deterministic it should be.

50. Insight API Endpoint Summary
GET /api/v1/insights
    → List insights

GET /api/v1/insights/:id
    → Retrieve one insight

PATCH /api/v1/insights/:id
    → Update user-facing insight state

Insight creation remains an internal intelligence operation.

51. Final Principle

The Insight API represents:

What WealthWise has learned from the user's financial behaviour.

It should bridge:

Financial Data
      ↓
Understanding
      ↓
User Awareness

without pretending that generated interpretations are raw financial facts.