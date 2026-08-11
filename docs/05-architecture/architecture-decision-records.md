# WealthWise — Architecture Decision Records

**Document Version:** 1.0  
**Status:** Architecture Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Technology Direction:** MERN + Generative AI

---

# 1. Purpose

This document records the major architectural decisions made during the design and development of WealthWise.

An Architecture Decision Record (ADR) captures:

- the problem being solved,
- the available alternatives,
- the selected approach,
- the reasoning behind the decision,
- the consequences of the decision.

The purpose is to prevent important architectural reasoning from being lost as the project evolves.

---

# 2. ADR Format

Each decision follows:

```text
Decision
Context
Alternatives
Chosen Approach
Reasoning
Consequences
Status

3. ADR-001 — Use MERN as the Primary Technology Stack
Decision

WealthWise will use the MERN stack as its primary application technology.

MongoDB
Express.js
React
Node.js

Generative AI services will be integrated through the backend.

Context

WealthWise requires:

an interactive web interface,
REST APIs,
authentication,
transaction processing,
analytics,
financial calculations,
database persistence,
AI integration.

The project is also a major engineering project where development speed and maintainability are important.

Alternatives
Alternative A — MERN
React
Node.js
Express
MongoDB
Alternative B — Spring Boot + React
React
Spring Boot
PostgreSQL
Alternative C — Django + React
React
Django
PostgreSQL
Alternative D — Next.js Full Stack
Next.js
PostgreSQL / MongoDB
Chosen Approach

MERN

Reasoning

MERN provides:

JavaScript/TypeScript across the application,
strong React ecosystem,
straightforward REST API development,
easy AI SDK integration,
rapid development,
good suitability for a student major project,
flexibility for future modularization.

The architecture also allows the backend to be separated cleanly from the frontend.

Consequences
Positive
Faster development.
Shared language across frontend and backend.
Strong ecosystem.
Easy integration with AI APIs.
Suitable for rapid prototyping.
Negative
Node.js requires careful handling of CPU-heavy workloads.
MongoDB requires disciplined schema design.
Financial calculations must be implemented carefully.
Status

Accepted

4. ADR-002 — Use MongoDB as the Primary Database
Decision

MongoDB will be used as the primary persistence layer.

Context

WealthWise stores several types of data:

Users
Transactions
Goals
Budgets
Insights
Conversations
Scenarios

The application also needs flexible metadata for AI-generated and analytical information.

Alternatives
Alternative A — MongoDB

Document-oriented database.

Alternative B — PostgreSQL

Relational database with strong transactional and analytical capabilities.

Alternative C — MySQL

Traditional relational database.

Chosen Approach

MongoDB

Reasoning

MongoDB provides:

flexible document structures,
natural compatibility with Node.js,
good support for nested analytical structures,
easy iteration during development,
strong indexing capabilities,
straightforward horizontal scalability.

The project does not require a highly relational banking ledger in the MVP.

Consequences
Positive
Flexible schemas.
Fast development.
Easy Node.js integration.
Suitable for evolving product requirements.
Negative
Relationships must be designed deliberately.
Financial consistency requires careful service-level validation.
Complex analytical queries may require aggregation pipelines.
Important Constraint

MongoDB does not mean that financial data can be treated casually.

Transactions and monetary calculations still require:

validation,
precise representation,
indexes,
consistency rules.
Status

Accepted

5. ADR-003 — Use REST APIs for Client-Server Communication
Decision

WealthWise will use REST APIs as the primary frontend-backend communication mechanism.

Context

The frontend requires operations such as:

Authentication
Transactions
Goals
Budgets
Analytics
Insights
Scenarios
AI Advisor
Alternatives
Alternative A — REST
Alternative B — GraphQL
Alternative C — WebSockets
Alternative D — Server Actions / RPC
Chosen Approach

REST

Reasoning

REST provides:

simple architecture,
clear endpoint boundaries,
easy debugging,
mature Node.js tooling,
straightforward authentication,
easy frontend integration.

Real-time communication is not a core MVP requirement.

Consequences
Positive
Simple API structure.
Easy testing.
Easy documentation.
Clear resource boundaries.
Negative
Some dashboard responses may require multiple requests.
Real-time functionality may require additional technology later.
Future Possibility

WebSockets may be introduced for:

AI streaming
Real-time notifications
Long-running import progress

without replacing the core REST API.

Status

Accepted

6. ADR-004 — Separate Deterministic Financial Intelligence From Generative AI
Decision

Financial calculations and authoritative financial state will be handled by deterministic application services.

Generative AI will primarily handle:

explanation,
interpretation,
conversational interaction,
recommendation generation.
Context

Financial information requires high accuracy.

LLMs can generate plausible but incorrect numerical information.

Therefore, allowing an LLM to independently calculate financial state would introduce unnecessary risk.

Alternatives
Alternative A

Send raw transactions to the LLM and let it analyze everything.

Alternative B

Use deterministic financial services and let AI interpret their results.

Alternative C

Use AI for all financial processing.

Chosen Approach

Alternative B

Financial Data
      ↓
Deterministic Engine
      ↓
Validated Financial Facts
      ↓
AI
      ↓
Explanation
Reasoning

This provides:

numerical reliability,
explainability,
easier testing,
reduced hallucination,
predictable calculations,
better control over AI behaviour.
Example

The backend calculates:

Food spending = ₹6,200
Recent average = ₹3,900
Increase = 58.97%

The AI explains:

Your food spending is significantly above your recent average.

The AI does not become the calculator.

Consequences
Positive
Higher financial reliability.
Easier testing.
Better AI grounding.
Clear architectural boundary.
Negative
More backend logic.
AI cannot independently answer arbitrary financial questions without context preparation.
Status

Accepted

7. ADR-005 — Introduce a Dedicated Financial Intelligence Layer
Decision

WealthWise will use a dedicated financial intelligence layer between raw transactions and user-facing insights.

Context

Raw transactions alone do not provide meaningful financial intelligence.

The product requires:

Analytics
Behaviour
Goals
Budgets
Insights
Scenarios
Chosen Architecture
Transactions
     ↓
Analytics
     ↓
Behaviour
     ↓
Goals / Budgets
     ↓
Insights / Scenarios
     ↓
AI
Reasoning

This architecture creates a reusable financial intelligence foundation.

The same calculated information can be used by:

dashboard,
insights,
AI Advisor,
scenarios,
goal analysis.
Consequences
Positive
Reusable intelligence.
Less duplicated calculation.
Clear architecture.
Easier future expansion.
Negative
Additional services.
More architecture than a basic expense tracker.
Status

Accepted

8. ADR-006 — Introduce a Scenario Engine
Decision

Scenario analysis will be implemented as a dedicated deterministic service.

Context

One of WealthWise's differentiating capabilities is helping users understand hypothetical financial decisions.

Examples:

What if I reduce food spending by 20%?

What if I save ₹5,000 more each month?

What if my income falls by ₹10,000?

What if I increase my goal contribution?
Alternatives
Alternative A

Let the LLM calculate scenarios.

Alternative B

Hard-code scenarios inside the frontend.

Alternative C

Create a backend Scenario Engine.

Chosen Approach

Dedicated Scenario Engine

Reasoning

The Scenario Engine can:

use the same financial calculation rules as the rest of the application,
produce reproducible results,
keep hypothetical calculations separate from real financial data,
allow AI to explain the result.
Architecture
Scenario Request
      ↓
Scenario Engine
      ↓
Projected Financial State
      ↓
Comparison With Baseline
      ↓
AI Explanation
Critical Rule

A scenario must never mutate actual financial data.

Status

Accepted

9. ADR-007 — Use User-Scoped Financial Data
Decision

All financial resources will be explicitly associated with an authenticated user.

Context

WealthWise stores highly personal financial information.

Cross-user data access would be a critical security failure.

Chosen Approach

Every protected resource follows:

Authenticated User
       ↓
User ID
       ↓
User-Scoped Query
       ↓
Resource
Example
Transaction.userId
Goal.userId
Budget.userId
Insight.userId
Conversation.userId
Reasoning

This provides a clear ownership model.

Authorization can then enforce:

resource.userId === authenticatedUserId
Consequences
Positive
Clear security boundary.
Easier authorization.
Easier data isolation.
Negative
Every service must consistently apply user scoping.
A missing ownership check can still create vulnerabilities.
Status

Accepted

10. ADR-008 — Keep AI Provider Behind an Abstraction
Decision

The backend will communicate with external AI providers through an internal AI provider interface.

Context

AI providers and models evolve rapidly.

The product should not tightly couple its core architecture to one provider.

Alternatives
Alternative A

Directly call one AI provider throughout the application.

Alternative B

Create an AI Provider abstraction.

Chosen Approach

AI Provider abstraction

Architecture
Advisor Service
      ↓
AIProvider Interface
      ↓
┌───────────────┐
│ AI Provider A │
└───────────────┘

or

┌───────────────┐
│ AI Provider B │
└───────────────┘
Reasoning

This allows:

provider replacement,
model experimentation,
easier testing,
reduced vendor lock-in.
Consequences
Positive
Flexible AI architecture.
Easier future experimentation.
Negative
Additional abstraction layer.
Providers may have different capabilities.
Status

Accepted

11. ADR-009 — AI Has Read-Oriented Access in the MVP
Decision

The AI Advisor will initially operate using read-oriented financial context.

The AI will not directly mutate financial state.

Context

AI-generated actions can have unintended consequences.

Examples:

Delete transaction
Change budget
Change goal
Transfer money

should never occur simply because an LLM generated a response.

Chosen Approach
AI
 ↓
Read Financial Context
 ↓
Generate Recommendation
 ↓
User Decision
Reasoning

This preserves:

AI recommends; the user decides.

Future Extension

Controlled actions may eventually use:

AI
 ↓
Proposed Action
 ↓
User Confirmation
 ↓
Backend Validation
 ↓
Execution
Status

Accepted

12. ADR-010 — Use Structured Financial Context for AI
Decision

AI prompts will receive a structured financial context instead of unrestricted raw database documents.

Context

Passing raw transaction collections to an LLM creates problems:

unnecessary token usage,
irrelevant context,
increased privacy exposure,
harder grounding,
higher latency.
Chosen Approach
Database
 ↓
Application Services
 ↓
Validated Metrics
 ↓
Context Builder
 ↓
Structured AI Context
Example

Instead of:

1,000 raw transactions

provide:

{
  "monthlyIncome": 60000,
  "monthlyExpenses": 42000,
  "monthlySavings": 18000,
  "foodSpending": 6200,
  "foodAverage": 3900,
  "foodBudget": 5000
}
Reasoning

This improves:

relevance,
privacy,
cost,
latency,
grounding.
Status

Accepted

13. ADR-011 — Use AI for Explanation Rather Than Financial Authority
Decision

AI-generated responses will be treated as interpretations and recommendations, not authoritative financial records.

Context

An AI response can be wrong even when it sounds confident.

Therefore, financial truth must remain within the deterministic system.

Chosen Model
Authoritative:
Transactions
Analytics
Goals
Budgets
Scenario Results

Interpretive:
AI Explanation
AI Recommendation
AI Summary
Reasoning

This creates a clear distinction between:

FACT

and:

INTERPRETATION
Status

Accepted

14. ADR-012 — Use an Explicit Insight Pipeline
Decision

Insights will be generated through a pipeline rather than directly by the LLM.

Context

If the AI were allowed to generate arbitrary insights, the application could produce:

noisy alerts,
irrelevant observations,
unsupported claims,
excessive notifications.
Chosen Architecture
Financial Data
      ↓
Analytics
      ↓
Behaviour Detection
      ↓
Significance Engine
      ↓
Important Signal
      ↓
AI Explanation
      ↓
Insight
Reasoning

This separates:

What is important?

from:

How should it be explained?

The first is primarily a product/business logic decision.

The second is where generative AI is useful.

Status

Accepted

15. ADR-013 — Separate Actual Financial State From Hypothetical State
Decision

Actual financial data and scenario-generated financial states will remain separate.

Context

Scenario analysis must not accidentally modify real financial information.

Architecture
ACTUAL STATE
Transactions
Goals
Budgets
Analytics

        │
        ▼

SCENARIO ENGINE

        │
        ▼

HYPOTHETICAL STATE
Reasoning

This makes scenarios:

safe,
reproducible,
reversible,
easy to compare.
Example
Actual Food Spending = ₹6,200

Scenario:
20% reduction

Projected Food Spending = ₹4,960

The actual value remains:

₹6,200
Status

Accepted

16. ADR-014 — Treat the Frontend as Untrusted
Decision

The frontend will never be treated as a trusted security boundary.

Context

A user can modify requests using:

browser developer tools,
API clients,
scripts,
intercepted requests.

Therefore frontend validation cannot provide security.

Chosen Approach
Frontend Validation
        ↓
User Experience

Backend Validation
        ↓
Security + Integrity
Example

The frontend may display:

amount >= 0

But the backend must independently validate the amount.

Status

Accepted

17. ADR-015 — Use Backend-Derived Financial Metrics
Decision

Important financial metrics will be calculated by backend services rather than trusted from the frontend.

Context

Metrics such as:

Savings
Savings Rate
Budget Usage
Goal Progress

can be manipulated if the frontend sends them as authoritative values.

Chosen Approach
Transactions
 ↓
Backend Analytics
 ↓
Financial Metrics
 ↓
API Response
Reasoning

This ensures:

consistent calculations,
data integrity,
AI grounding,
easier testing.
Status

Accepted

18. ADR-016 — Use Precise Monetary Representation
Decision

WealthWise will use a representation that avoids inappropriate floating-point precision for monetary calculations.

Context

JavaScript floating-point arithmetic can produce unexpected results.

Example:

0.1 + 0.2

may not produce an exact decimal representation.

Financial applications should avoid relying on binary floating-point arithmetic for authoritative monetary calculations.

Alternatives
Alternative A

JavaScript Number

Alternative B

Integer minor units

Example:

₹12.50 → 1250 paise
Alternative C

MongoDB Decimal128

Chosen Direction

The application will use an exact monetary representation.

The final implementation will select between:

Minor-unit integers

and:

Decimal128

based on the final database and calculation design.

Reasoning

The most important requirement is:

Financial calculations must be deterministic and precise.

Consequences

Additional conversion logic may be required when communicating between:

Database
Backend
Frontend
AI Context
Status

Accepted — Implementation Detail Pending

19. ADR-017 — Use Modular Backend Services
Decision

The backend will be organized into domain-oriented modules rather than one large controller/service.

Context

WealthWise contains multiple domains:

Auth
Users
Transactions
Analytics
Budgets
Goals
Insights
Scenarios
Advisor
Chosen Structure

Conceptually:

server/
└── src/
    ├── modules/
    │   ├── auth/
    │   ├── users/
    │   ├── transactions/
    │   ├── analytics/
    │   ├── budgets/
    │   ├── goals/
    │   ├── insights/
    │   ├── scenarios/
    │   └── advisor/
    │
    ├── middleware/
    ├── config/
    ├── utils/
    └── infrastructure/
Reasoning

Modularity improves:

maintainability,
testing,
ownership,
scalability,
feature isolation.
Status

Accepted

20. ADR-018 — Keep Core Financial Logic Independent From the AI Layer
Decision

Core financial services must not depend on the AI provider.

Context

AI providers can fail, become unavailable, or change.

Financial functionality must remain available.

Correct Architecture
Transactions
     ↓
Analytics
     ↓
Goals
     ↓
Budgets

These services operate independently.

Advisor
   ↓
Uses results from those services
Incorrect Architecture
Transactions
 ↓
AI
 ↓
Analytics
Reasoning

This ensures:

AI failure does not break the financial engine,
deterministic calculations remain testable,
provider changes do not affect core functionality.
Status

Accepted

21. ADR-019 — Prefer Context Selection Over Full Financial History
Decision

The AI Advisor will retrieve only the minimum sufficient financial context required for each question.

Context

Sending the user's complete financial history for every question would:

increase token cost,
increase latency,
increase privacy exposure,
reduce relevance.
Chosen Approach
Question
 ↓
Intent
 ↓
Required Context
 ↓
Retrieve Relevant Data
 ↓
AI
Example

Question:

Why did my food spending increase?

Required:

Food spending
Historical food spending
Food budget
Relevant goal impact

Not:

Every transaction ever recorded.
Status

Accepted

22. ADR-020 — Preserve Financial Data Lineage
Decision

Important financial insights should be traceable back to their underlying metrics and source data.

Context

Users need to trust the explanations WealthWise provides.

If the application says:

Your food spending increased by 59%.

the system should conceptually be able to identify:

Source Transactions
       ↓
Food Total
       ↓
Historical Baseline
       ↓
Percentage Change
       ↓
Insight
Reasoning

Data lineage improves:

explainability,
debugging,
testing,
user trust,
AI evaluation.
Status

Accepted

23. ADR-021 — Separate Product Truth From AI Language
Decision

The application will distinguish between structured financial facts and generated language.

Example

Structured truth:

{
  "foodSpending": 6200,
  "foodBudget": 5000,
  "budgetOverrun": 1200
}

Generated language:

You are currently ₹1,200 above your food budget.

The second is a representation of the first.

Reasoning

This makes it possible to:

change AI providers,
improve prompts,
regenerate explanations,
test facts independently.
Status

Accepted

24. ADR-022 — AI Recommendations Require User Agency
Decision

Recommendations are suggestions rather than commands.

Context

Personal financial decisions are subjective.

A recommendation that is mathematically reasonable may not fit the user's priorities.

Chosen Model
AI
 ↓
Recommendation
 ↓
Explanation
 ↓
User Decision
Example

AI:

You could reduce discretionary dining by approximately ₹1,000 this month.

User:

Accept
Modify
Ignore
Explore Scenario
Status

Accepted

25. ADR-023 — Keep AI Provider Credentials Backend-Only
Decision

AI provider API keys must never be exposed to the React application.

Architecture
React
 ↓
WealthWise Backend
 ↓
AI Provider

Not:

React
 ↓
AI Provider
Reasoning

Frontend-exposed API keys can be extracted and abused.

The backend provides:

authentication,
rate limiting,
context control,
cost control,
provider abstraction.
Status

Accepted

26. ADR-024 — Use Read-Only AI Tools Initially
Decision

If AI tool calling is implemented, MVP tools will be read-oriented.

Allowed examples:

getFinancialSummary()
getCategoryTrend()
getBudgetStatus()
getGoalStatus()
simulateScenario()
Not Allowed
deleteTransaction()
transferMoney()
changeBudget()
changeGoal()
Reasoning

This minimizes the impact of:

prompt injection,
model errors,
misunderstood requests,
hallucinations.
Status

Accepted

27. ADR-025 — Keep AI Failure Non-Critical
Decision

AI availability must not determine whether core WealthWise functionality is available.

Context

External AI services can experience:

outages,
rate limits,
network failures,
latency,
provider changes.
Chosen Architecture
                 WealthWise
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
    Financial Engine        AI Advisor
          │                     │
          ▼                     ▼
     Core Features          AI Features

If AI fails:

Transactions → Still Available
Analytics    → Still Available
Goals        → Still Available
Budgets      → Still Available
Status

Accepted

28. ADR-026 — Treat Imported Financial Data as Untrusted
Decision

Imported transaction files will be treated as untrusted input.

Context

Files may contain:

malformed data,
malicious text,
duplicate records,
invalid values,
spreadsheet formula payloads.
Chosen Pipeline
File
 ↓
Validation
 ↓
Parsing
 ↓
Sanitization
 ↓
Normalization
 ↓
Classification
 ↓
Persistence
Status

Accepted

29. ADR-027 — Use a Layered Architecture
Decision

WealthWise will use a layered architecture.

Layers
Presentation
      ↓
API
      ↓
Application / Services
      ↓
Domain Intelligence
      ↓
Persistence / Infrastructure
Reasoning

Each layer should have a clear responsibility.

This prevents:

React → MongoDB

or:

Controller → complex financial calculations → AI provider

from becoming the application's architecture.

Status

Accepted

30. ADR-028 — Use an Evolving Architecture Rather Than Premature Microservices
Decision

The initial WealthWise implementation will be a modular monolith rather than a microservice architecture.

Context

The project is initially a major-project/MVP system.

The domains are logically separate but do not yet require independent deployment.

Alternatives
Alternative A

Microservices.

Alternative B

Modular monolith.

Chosen Approach

Modular monolith

Architecture
                    WealthWise Backend
                           │
       ┌───────────────────┼───────────────────┐
       │                   │                   │
     Auth              Financial          Intelligence
                           │
                  ┌────────┼────────┐
                  │        │        │
             Transactions Goals  Budgets
                           │
                  ┌────────┼────────┐
                  │        │        │
              Analytics Behaviour Insights
                           │
                        Advisor
Reasoning

A modular monolith provides:

simple deployment,
low operational complexity,
clear domain boundaries,
easy local development,
straightforward debugging.

If scale later requires independent services, modules can be extracted.

Status

Accepted

31. ADR-029 — Design for Future Event-Driven Processing
Decision

The architecture will remain compatible with event-driven processing without making it mandatory for the MVP.

Context

Some future operations may become expensive:

Large imports
Historical analysis
Insight generation
Notifications
Recurring expense detection
Initial Approach

Synchronous service calls.

Future Approach
Transaction Created
      ↓
Event
      ↓
Background Processing
      ↓
Analytics / Behaviour / Insights
Reasoning

This avoids premature infrastructure complexity while preserving a path to scalability.

Status

Accepted

32. ADR-030 — Build the MVP Around Financial Intelligence, Not Banking Integration
Decision

The MVP will focus on user-provided/imported financial data rather than direct bank integrations.

Context

Bank integrations introduce:

authentication complexity,
financial institution APIs,
compliance concerns,
regional availability issues,
security requirements,
additional infrastructure.

These are not necessary to prove the core WealthWise product idea.

Chosen MVP
Manual Transactions
+
CSV Import
+
Financial Analytics
+
Behaviour Intelligence
+
Goals
+
Budgets
+
Scenarios
+
AI Advisor
Future

Bank/open-banking integrations may be considered after the core product is validated.

Reasoning

This allows the project to demonstrate its central differentiator:

Turning financial records into actionable financial intelligence.

without making banking connectivity the primary engineering challenge.

Status

Accepted

33. ADR-031 — Design the Product Around the Financial Intelligence Loop
Decision

The product architecture will support a continuous loop:

Data
 ↓
Understanding
 ↓
Insight
 ↓
Decision
 ↓
Action
 ↓
New Data
Context

A conventional expense tracker ends at:

Data → Dashboard

WealthWise aims to continue:

Data
 ↓
Analysis
 ↓
Behaviour
 ↓
Goal Impact
 ↓
Insight
 ↓
Recommendation
 ↓
User Decision
 ↓
Future Behaviour
Reasoning

This loop represents the core product differentiation.

The system becomes an evolving financial intelligence layer rather than a static expense ledger.

Status

Accepted

34. ADR-032 — Keep Product Scope Focused on Decision Support
Decision

WealthWise will focus its initial intelligence capabilities on:

Expense Intelligence
Budgeting
Savings
Financial Goals
Behaviour Analysis
Scenario Analysis
AI Decision Support
Explicitly Out of MVP Scope
Banking
Payment Processing
Money Transfers
Loan Origination
Autonomous Investment Trading
Automated Financial Transactions
Reasoning

This keeps the product technically achievable while preserving a strong differentiating concept.

Status

Accepted

35. ADR-033 — Use Goal Awareness as a Core Intelligence Dimension
Decision

Financial behaviour should be interpreted in relation to user-defined goals whenever relevant.

Context

A spending decision is not inherently good or bad.

Its importance depends partly on what the user is trying to achieve.

Example:

₹2,000 spent on travel

means something different for:

User A:
No active travel goal

User B:
Trying to save ₹50,000 for a trip
Chosen Model
Spending
   +
Goal Context
   ↓
Goal Impact
   ↓
More Relevant Insight
Reasoning

This creates more personalized intelligence than category-level expense tracking alone.

Status

Accepted

36. ADR-034 — Use Behaviour Baselines Instead of Universal Rules
Decision

Behaviour analysis should prefer user-specific baselines where sufficient historical data exists.

Context

₹5,000 of spending may be normal for one user and unusual for another.

Therefore universal thresholds can produce poor insights.

Preferred Model
User's Historical Behaviour
        ↓
Personal Baseline
        ↓
Current Behaviour
        ↓
Deviation
Fallback

When insufficient history exists, the system may use:

Budget
Generic threshold
Category-level rule

with appropriate uncertainty.

Status

Accepted

37. ADR-035 — AI Should Communicate Uncertainty
Decision

The AI should distinguish between:

Known
Estimated
Projected
Hypothetical
Unknown
Context

Financial forecasting is inherently uncertain.

The system should not present projections as guarantees.

Example

Bad:

You will definitely reach your goal in December.

Better:

At your current average savings rate, you appear to be on track to reach the goal by December, assuming your income and spending remain broadly similar.

Status

Accepted

38. ADR-036 — Separate Insight Detection From Insight Explanation
Decision

The system will separate:

Detection

from:

Explanation
Detection

Primarily deterministic:

Is spending unusually high?
Is the budget exceeded?
Is a goal at risk?
Explanation

AI-assisted:

Why does this matter?
How should this be communicated?
What actions could help?
Reasoning

This provides a clean boundary between:

Financial truth

and:

Natural-language interpretation
Status

Accepted

39. ADR-037 — Use Explicit Versioning for AI Prompts
Decision

Production AI prompts should be versioned.

Example
promptVersion = "1.0"

Later:

promptVersion = "1.1"
Reasoning

Prompt changes can significantly alter AI behaviour.

Versioning allows the team to determine:

which prompt generated an answer,
whether a change improved quality,
whether regressions were introduced.
Status

Accepted

40. ADR-038 — Evaluate AI Separately From Traditional Software Tests
Decision

The AI layer will have a dedicated evaluation framework.

Context

Traditional tests can verify:

5 + 5 = 10

but cannot fully determine whether:

"Your food spending appears to be affecting your goal"

is contextually useful.

AI Evaluation Areas
Grounding
Numerical fidelity
Relevance
Safety
Consistency
Recommendation quality
Hallucination
Status

Accepted

41. ADR-039 — Maintain a Clear Boundary Between Fact, Interpretation, and Recommendation
Decision

WealthWise will conceptually classify AI output into:

FACT
INTERPRETATION
RECOMMENDATION
Example
Fact

You spent ₹6,200 on food.

Interpretation

This is about 59% above your recent average.

Recommendation

You could explore reducing discretionary dining this month.

Reasoning

This improves transparency and trust.

Status

Accepted

42. ADR-040 — Build the Product for Explainability
Decision

Important insights should be explainable through their underlying data and calculations.

Example

Instead of:

Your spending is unhealthy.

WealthWise should be able to communicate:

Food spending
    ↓
₹6,200
    ↓
Budget ₹5,000
    ↓
₹1,200 over budget
    ↓
Relevant to active savings goal
Reasoning

Users should understand:

Why WealthWise is saying something.

not simply:

What WealthWise is saying.

Status

Accepted

43. Decision Dependency Map

The major architectural decisions connect as follows:

MERN
 │
 ├── React
 │
 ├── Node.js / Express
 │
 └── MongoDB
       │
       ▼
Modular Monolith
       │
       ▼
Domain Services
       │
 ┌─────┼──────────────┐
 ▼     ▼              ▼
Data Analytics   Goals/Budgets
 │       │              │
 └───────┼──────────────┘
         ▼
Financial Intelligence
         │
    ┌────┼────┐
    ▼    ▼    ▼
Insights Scenarios Advisor
              │
              ▼
        AI Provider
44. Architectural Principles Derived From ADRs

The decisions above establish the following principles.

Principle 1

Financial truth is deterministic.

Principle 2

AI interprets rather than owns financial state.

Principle 3

All financial data is user-scoped.

Principle 4

The frontend is untrusted.

Principle 5

Scenarios never modify actual financial state.

Principle 6

AI access is controlled and contextual.

Principle 7

Core financial functionality does not depend on AI availability.

Principle 8

Important insights should be explainable.

Principle 9

Architecture should be modular without premature complexity.

Principle 10

The product is built around a continuous financial intelligence loop.

45. Current Architecture Baseline

The current baseline architecture is:

Frontend:
React

Backend:
Node.js + Express

Database:
MongoDB

API:
REST

Architecture:
Modular Monolith

AI:
Generative AI through Provider Abstraction

Financial Intelligence:
Deterministic Services

AI Context:
Structured + User-Scoped

Scenarios:
Dedicated Scenario Engine

Security:
Defense in Depth

Authentication:
Token / Session based

Financial Model:
User-Scoped

MVP Data Input:
Manual + CSV Import
46. Decisions Still Pending

The following decisions intentionally remain open until implementation research:

Authentication:
JWT vs secure session

Money Representation:
Minor-unit integer vs Decimal128

AI Provider:
Provider selection

AI Model:
Model selection

File Storage:
Local vs object storage

Background Jobs:
Required infrastructure

Deployment:
Cloud provider

Caching:
Whether Redis is necessary

Notifications:
Email / browser / in-app

Bank Integration:
Future architecture

These should be resolved through additional ADRs rather than assumptions.

47. ADR Lifecycle

Architecture decisions are not permanent.

A decision may move through:

Proposed
   ↓
Accepted
   ↓
Implemented
   ↓
Superseded

A superseded decision should remain documented rather than silently deleted.

48. How to Add Future ADRs

Future decisions should follow:

ADR-XXX — Decision Title

## Decision

...

## Context

...

## Alternatives

...

## Chosen Approach

...

## Reasoning

...

## Consequences

...

## Status

Proposed / Accepted / Superseded
49. Summary

WealthWise is intentionally designed as:

                    WEALTHWISE
                        │
                        ▼
                Modular Monolith
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
      Transactions   Goals/Budgets  Users
          │             │
          └──────┬──────┘
                 ▼
        Financial Intelligence
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
   Analytics  Behaviour  Scenarios
       │         │         │
       └─────────┼─────────┘
                 ▼
              Insights
                 │
                 ▼
             AI Advisor
                 │
                 ▼
        Grounded Explanation
                 │
                 ▼
           Recommendation
                 │
                 ▼
            USER DECISION
                 │
                 ▼
          FUTURE BEHAVIOUR
                 │
                 ▼
           NEW FINANCIAL DATA
                 │
                 └──────────────→ LOOP

The architectural philosophy can be summarized as:

WealthWise calculates what is true, detects what matters, uses AI to explain what it means, and helps the user decide what to do next.