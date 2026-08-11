# WealthWise — MVP Scope & Development Boundaries

**Document Version:** 1.0  
**Status:** Product Scope Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Technology Direction:** MERN + Generative AI

---

# 1. Purpose

This document defines the exact scope of the WealthWise Minimum Viable Product (MVP).

The purpose is to establish:

- what will be built,
- what will not be built,
- the order in which features will be developed,
- what constitutes MVP completion,
- the boundaries that prevent unnecessary scope expansion.

The MVP should demonstrate the central WealthWise hypothesis:

> **Financial data becomes significantly more useful when it is transformed into personalized financial intelligence and actionable decision support.**

---

# 2. MVP Philosophy

The MVP is not intended to contain every possible personal-finance feature.

It is intended to prove the core product loop:

```text
Financial Data
      ↓
Understanding
      ↓
Behaviour
      ↓
Goal / Budget Impact
      ↓
Insight
      ↓
Scenario
      ↓
AI Explanation
      ↓
Recommendation
      ↓
User Decision

If this loop works reliably, WealthWise has demonstrated its core value.

3. MVP Definition

The WealthWise MVP is:

A web-based personal financial intelligence platform where users can securely manage or import financial transactions, understand their spending and savings behaviour, create budgets and financial goals, receive personalized insights, simulate financial scenarios, and interact with an AI advisor grounded in their financial data.

4. MVP Success Criteria

The MVP is successful if a new user can complete this journey:

Register
   ↓
Create / Import Transactions
   ↓
View Financial Dashboard
   ↓
Understand Spending
   ↓
Create Budget
   ↓
Create Goal
   ↓
Receive Financial Insight
   ↓
Ask AI Advisor
   ↓
Run Scenario
   ↓
Understand Potential Action
5. MVP Core Modules

The MVP contains the following modules:

1. Authentication
2. User Profile
3. Transaction Management
4. Transaction Import
5. Categorization
6. Financial Analytics
7. Dashboard
8. Budget Management
9. Goal Management
10. Behaviour Intelligence
11. Insight Engine
12. Scenario Engine
13. AI Advisor
6. MVP Module Dependency
                    AUTHENTICATION
                          │
                          ▼
                     USER PROFILE
                          │
                          ▼
                TRANSACTION MANAGEMENT
                    │             │
                    │             ▼
                    │       CATEGORIZATION
                    │             │
                    └──────┬──────┘
                           ▼
                    FINANCIAL ANALYTICS
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
        BEHAVIOUR       BUDGETS        GOALS
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                    INSIGHT ENGINE
                           │
                  ┌────────┴────────┐
                  ▼                 ▼
             SCENARIO ENGINE    AI ADVISOR
                  │                 │
                  └────────┬────────┘
                           ▼
                     USER DECISION
7. MVP-001 — Authentication
Included
User registration
User login
User logout
Password hashing
Authentication state
Protected routes
User-scoped resources
Excluded
Social login
Two-factor authentication
Passkeys
Enterprise SSO

These may be considered later.

8. MVP-002 — User Profile
Included
Name
Email
Preferred currency
Basic preferences
Excluded
Detailed financial profile
Employment information
Tax profile
Investment profile
Credit profile
9. MVP-003 — Manual Transaction Management
Included

Users can:

create transactions,
edit transactions,
delete transactions,
view transactions,
search transactions,
filter transactions,
sort transactions.
Transaction Types
Income
Expense
10. MVP Transaction Schema

The initial conceptual transaction structure is:

Transaction
├── id
├── userId
├── amount
├── type
├── date
├── description
├── merchant
├── category
├── notes
├── source
├── createdAt
└── updatedAt

The final database schema will be defined separately.

11. MVP-004 — CSV Import

Users can import transaction data from CSV.

Supported Flow
Upload
  ↓
Validate
  ↓
Parse
  ↓
Normalize
  ↓
Categorize
  ↓
Detect Duplicates
  ↓
Import
MVP Requirements

The import system must:

validate file format,
validate required columns,
validate transaction values,
reject malformed records,
identify duplicates,
provide import results.
12. CSV MVP Limitation

The MVP will support a standard WealthWise CSV format.

Example:

date,description,amount,type,category,merchant
2026-08-01,Salary,60000,income,Income,Company
2026-08-02,Lunch,350,expense,Food,Restaurant
2026-08-03,Metro,80,expense,Transport,Metro

Bank-specific CSV formats may be supported later.

13. MVP-005 — Transaction Categorization

The MVP will categorize transactions using a controlled category system.

Initial categories:

Food
Housing
Transport
Shopping
Entertainment
Healthcare
Education
Bills & Utilities
Travel
Personal Care
Subscriptions
Other
14. Categorization Strategy

The MVP should prioritize deterministic rules.

Example:

"Netflix"
    ↓
Subscriptions
"Uber"
    ↓
Transport

If confidence is insufficient, AI-assisted categorization may be used.

However:

AI must choose from the WealthWise category vocabulary rather than inventing arbitrary categories.

15. MVP-006 — Financial Dashboard

The dashboard is the primary financial overview.

Required Metrics
Total Income
Total Expenses
Total Savings
Savings Rate
Required Visualizations
Expense Distribution
Spending Trend
Income vs Expense
Additional Sections
Budget Status
Goal Progress
Important Insights
16. MVP-007 — Expense Analytics

The MVP must allow users to understand:

total spending,
spending by category,
top spending categories,
spending over time,
merchant-level spending.
17. MVP-008 — Savings Analytics

The system calculates:

Savings = Income - Expenses

and:

Savings Rate =
(Savings / Income) × 100

The user should be able to view savings trends over the selected period.

18. MVP-009 — Financial Periods

The MVP should support:

This Month
Last Month
Last 3 Months
Last 6 Months
This Year
Custom Range

All dashboard and analytics calculations should respect the selected period.

19. MVP-010 — Budget Management

Users can:

create a budget,
edit a budget,
delete a budget,
view budget status.

Initial budget model:

Category
Amount
Period

Example:

Food
₹5,000
Monthly
20. MVP Budget Intelligence

For each budget:

Budget
Actual Spending
Remaining
Usage %
Status

Status:

Within Budget
Approaching Limit
Over Budget
21. MVP-011 — Financial Goals

Users can create financial goals.

Required fields:

Goal Name
Target Amount
Current Amount
Target Date
Purpose
22. MVP Goal Metrics

The system calculates:

Remaining Amount
Progress %
Required Contribution
Goal Status

Formula:

Progress % =
(Current Amount / Target Amount) × 100
23. MVP Goal Status

Initial statuses:

On Track
At Risk
Behind
Insufficient Data

The classification should be deterministic and explainable.

24. MVP-012 — Behaviour Intelligence

This is one of the most important MVP modules.

WealthWise should detect meaningful changes in spending behaviour.

Initial signals:

Category Increase
Category Decrease
Spending Spike
Savings Decline
Budget Risk
Goal Risk
Unusual Expense
Recurring Pattern
25. Behaviour Intelligence Example

Suppose:

Historical food average = ₹3,900
Current food spending = ₹6,200

The system calculates:

Increase ≈ 59%

This can produce:

Behaviour Signal:
FOOD_SPENDING_INCREASE
26. Personal Baseline

Where enough historical data exists:

Current Behaviour
        ↓
User Historical Baseline
        ↓
Deviation

The MVP should avoid relying entirely on universal thresholds.

27. New User Behaviour Handling

A new user may not have enough history.

In that case:

Insufficient History
        ↓
No Strong Behaviour Claim

Instead, WealthWise should communicate:

We need more transaction history to identify reliable spending patterns.

This prevents false personalization.

28. MVP-013 — Insight Engine

The Insight Engine converts meaningful financial signals into user-facing insights.

Pipeline:

Transactions
     ↓
Analytics
     ↓
Behaviour Signals
     ↓
Significance Check
     ↓
Insight
29. Insight Examples
Spending

Food spending is significantly higher than your recent average.

Budget

You have already used 88% of your food budget.

Savings

Your savings this month are lower than your recent average.

Goal

Your current savings pace may put your travel goal at risk.

30. Insight Generation Rule

The AI should not independently decide whether something is important.

The system should first determine:

Is this financially significant?

Then AI can determine:

How should this be explained?
31. MVP-014 — Scenario Engine

Scenario analysis is a core MVP differentiator.

Users should be able to simulate financial changes.

Examples:

Reduce food spending by 20%

Save ₹5,000 more per month

Reduce shopping by ₹2,000

Increase monthly savings

Income decreases by ₹10,000
32. Scenario Engine Requirements

The Scenario Engine must:

use current financial state,
apply hypothetical changes,
calculate projected values,
compare against baseline,
calculate goal impact where relevant,
avoid modifying actual financial data.
33. Scenario Example

Current:

Food spending = ₹6,200

Scenario:

Reduce by 20%

Calculation:

Reduction = ₹1,240

Projected spending = ₹4,960

Potential effect:

Savings improvement = ₹1,240
34. Scenario Output

A scenario should return structured results:

Baseline
Scenario Change
Projected Result
Difference
Percentage Difference
Goal Impact

The AI may then explain these results.

35. MVP-015 — AI Financial Advisor

The AI Advisor is the conversational intelligence layer.

Users can ask:

Why did my spending increase?

Where am I spending the most?

Why are my savings lower?

Am I on track for my goal?

What can I improve?

What happens if I reduce shopping?
36. AI Advisor Architecture
User Question
      ↓
Question Classification
      ↓
Required Context
      ↓
Financial Services
      ↓
Structured Context
      ↓
Prompt Builder
      ↓
AI Model
      ↓
Response Validation
      ↓
User
37. AI Context Rule

The AI should receive only relevant context.

Example:

User asks:

Why did food spending increase?

Context:

Food spending
Historical food spending
Food budget
Relevant trends
Relevant goal information

Not:

Every piece of data stored for the user.
38. MVP AI Capabilities

The MVP AI Advisor should support:

Financial Summary

Summarize my finances this month.

Spending Explanation

Why am I spending more?

Category Analysis

Where am I spending the most?

Savings Analysis

Why did my savings fall?

Goal Analysis

Am I on track for my goal?

Recommendation

What should I improve?

Scenario Assistance

What if I reduce food spending?

39. MVP AI Limitations

The AI will NOT:

Transfer money
Delete transactions
Modify goals automatically
Modify budgets automatically
Execute payments
Trade investments
Access bank credentials
Execute arbitrary database queries
40. AI Recommendation Model

The recommendation pipeline is:

Financial Facts
      ↓
Behaviour
      ↓
Goal / Budget Context
      ↓
Potential Action
      ↓
Estimated Impact
      ↓
AI Explanation
41. MVP Recommendation Example

Input:

Food spending = ₹6,200
Food budget = ₹5,000
Travel goal = ₹50,000

Potential recommendation:

Your food spending is ₹1,200 above budget. Reducing discretionary dining could free up some additional money for your travel goal.

The recommendation remains optional.

42. Fact vs Interpretation

The MVP should clearly distinguish:

Fact
Food spending = ₹6,200
Interpretation
This is above your recent average.
Recommendation
Consider reducing discretionary dining.

This distinction improves trust.

43. MVP-016 — Dashboard Insight Integration

The dashboard should surface the most relevant insights.

Example:

┌───────────────────────────────────────┐
│ Your Financial Snapshot              │
│                                       │
│ Income       ₹60,000                  │
│ Expenses     ₹42,000                  │
│ Savings      ₹18,000                  │
│                                       │
│ ⚠ Food spending is above your norm   │
│ ✓ Travel goal is on track             │
│ ⚠ Shopping budget nearing its limit   │
└───────────────────────────────────────┘
44. MVP-017 — Insight Drill-Down

Clicking an insight should provide evidence.

Example:

Food spending increased
        ↓
Insight Details
        ↓
Current spending
Historical average
Budget
Trend
Relevant transactions

The user should be able to understand why the insight exists.

45. MVP-018 — Analytics Drill-Down

Users should be able to navigate:

Total Expenses
      ↓
Category
      ↓
Merchant
      ↓
Transaction

This creates a path from summary to raw evidence.

46. MVP-019 — Data Freshness

When financial state changes:

Transaction Added
Transaction Edited
Transaction Deleted
Budget Updated
Goal Updated

dependent calculations must reflect the change.

47. MVP-020 — Empty States

Every major module must handle missing data.

Examples:

No Transactions

Add your first transaction to start understanding your spending.

No Goals

Create a financial goal to connect your spending with something you're working toward.

Insufficient History

Keep adding transactions so WealthWise can learn your spending patterns.

No Insights

No significant changes detected yet.

48. MVP-021 — Error Handling

The application must handle:

Invalid transaction
Invalid CSV
Duplicate transaction
Failed AI request
Database error
Network error
Unauthorized request
Expired authentication

Errors should be understandable to users.

49. MVP-022 — Security

The MVP must implement:

Authentication
Authorization
User ownership
Password hashing
Input validation
Rate limiting for sensitive endpoints
Secure API communication
Environment secrets
Protected database access
AI context isolation
50. MVP-023 — AI Failure Handling

If the AI provider fails:

AI Request
    ↓
Provider Error
    ↓
Graceful Fallback

The application should communicate:

The AI advisor is temporarily unavailable. Your financial dashboard and analytics are still available.

Core financial functionality must continue working.

51. MVP-024 — AI Grounding

The AI should never invent authoritative financial numbers.

If the system does not know something:

Unknown

should be communicated instead of fabricated.

52. MVP-025 — AI Uncertainty

Forecasts and hypothetical outcomes should use uncertainty-aware language.

Avoid:

You will definitely save ₹5,000.

Prefer:

If your spending remains similar, this change could increase your monthly savings by approximately ₹5,000.

53. MVP Exclusions

The following are explicitly outside the MVP.

Banking
Direct bank connection
Automatic bank synchronization
Open banking
Payments
UPI
Card payments
Money transfer
Bill payments
Investments
Stock trading
Mutual fund trading
Portfolio execution
Automated investment
Lending
Loans
Credit decisions
Loan applications
Advanced AI Actions
Autonomous financial actions
Automatic budget modification
Automatic transaction deletion
Financial transaction execution
54. Post-MVP Features

After MVP completion, potential next-stage features include:

Recurring Expense Detection
Subscription Intelligence
Advanced Anomaly Detection
Predictive Spending
Cash-Flow Forecasting
Notifications
Monthly Reports
Natural Language Transaction Search
AI Tool Calling
Multi-Account Support
55. Future Features

Longer-term possibilities:

Bank Integration
Open Banking
Multi-Currency
Financial Health Score
Advanced Forecasting
Personalized Financial Coaching
AI-Assisted Financial Planning

These should not affect the MVP implementation.

56. MVP Development Principle

When a new feature idea appears, ask:

Does this help prove the core WealthWise hypothesis?

If the answer is no:

Move to Post-MVP

rather than expanding the MVP.

57. MVP Scope Test

A proposed feature belongs in the MVP only if it satisfies at least one of these:

1. Required for core financial data management.

2. Required for financial intelligence.

3. Required for personalized insights.

4. Required for scenario analysis.

5. Required for the AI Advisor.

6. Required for demonstrating the product's uniqueness.

Otherwise it should probably wait.

58. MVP Milestone Structure

Development should proceed in milestones rather than attempting every feature simultaneously.

Milestone 1
Foundation

Milestone 2
Authentication + Users

Milestone 3
Transactions

Milestone 4
Financial Analytics

Milestone 5
Budgets + Goals

Milestone 6
Behaviour Intelligence

Milestone 7
Insight Engine

Milestone 8
Scenario Engine

Milestone 9
AI Advisor

Milestone 10
Integration + Testing
59. Milestone 1 — Foundation
Objective

Establish the project infrastructure.

Deliverables
Repository
Frontend
Backend
Database Connection
Environment Configuration
Basic Routing
API Structure
Development Tooling
Completion Criteria
Frontend runs
Backend runs
Database connects
Basic API responds
Git workflow established
60. Milestone 2 — Authentication
Objective

Establish secure user identity.

Deliverables
Registration
Login
Logout
Protected Routes
User Model
Authentication Middleware
Completion Criteria
User can register
User can login
Protected APIs reject unauthenticated requests
User data is scoped correctly
61. Milestone 3 — Transactions
Objective

Create the financial data foundation.

Deliverables
Transaction Model
Create
Read
Update
Delete
Search
Filter
CSV Import
Categorization
Completion Criteria

A user can build a usable transaction history.

62. Milestone 4 — Financial Analytics
Objective

Transform transactions into financial metrics.

Deliverables
Income
Expenses
Savings
Savings Rate
Category Analytics
Trends
Period Filtering
Dashboard
Completion Criteria

The dashboard accurately reflects transaction data.

63. Milestone 5 — Budgets + Goals
Objective

Introduce financial intent.

Deliverables
Budget CRUD
Budget Tracking
Goal CRUD
Goal Progress
Goal Feasibility
Completion Criteria

Users can connect their financial data with objectives.

64. Milestone 6 — Behaviour Intelligence
Objective

Move beyond basic analytics.

Deliverables
Personal Baselines
Spending Change Detection
Category Signals
Savings Signals
Budget Signals
Goal Signals
Completion Criteria

The system can detect meaningful behavioural changes.

65. Milestone 7 — Insight Engine
Objective

Turn signals into useful financial observations.

Deliverables
Insight Rules
Significance Logic
Insight Storage
Insight API
Dashboard Integration
Insight Drill-Down
Completion Criteria

Users receive meaningful and explainable insights.

66. Milestone 8 — Scenario Engine
Objective

Allow users to explore hypothetical financial decisions.

Deliverables
Scenario Model
Scenario Calculation Engine
Baseline Comparison
Goal Impact
Scenario API
Completion Criteria

Users can run hypothetical changes without modifying real data.

67. Milestone 9 — AI Advisor
Objective

Add the generative intelligence layer.

Deliverables
AI Provider Interface
Context Builder
Prompt System
Advisor API
Conversation UI
Financial Question Answering
Insight Explanation
Recommendations
Scenario Explanation
Completion Criteria

AI can answer financial questions using verified WealthWise context.

68. Milestone 10 — Integration & Testing
Objective

Ensure the complete product works as one system.

Deliverables
End-to-End Flows
Security Testing
AI Evaluation
Performance Testing
Error Handling
UI Polish
Documentation
Deployment
69. MVP Completion Checklist
Product
[ ] Core user journey works
[ ] Financial data can be added
[ ] Financial data can be imported
[ ] Dashboard works
[ ] Analytics work
[ ] Budgets work
[ ] Goals work
[ ] Behaviour analysis works
[ ] Insights work
[ ] Scenarios work
[ ] AI Advisor works
Engineering
[ ] Frontend complete
[ ] Backend complete
[ ] Database complete
[ ] APIs documented
[ ] Validation implemented
[ ] Error handling implemented
[ ] Security implemented
AI
[ ] AI provider integrated
[ ] Context builder implemented
[ ] Prompt versions tracked
[ ] Financial facts grounded
[ ] Prompt injection defenses implemented
[ ] AI failure handled
[ ] AI evaluation performed
70. MVP Demo Scenario

The final MVP demonstration should use a realistic user journey.

Example:

User earns ₹60,000/month.

Transactions are imported.

WealthWise identifies:

Income:
₹60,000

Expenses:
₹42,000

Savings:
₹18,000

Savings Rate:
30%

Then:

Food spending:
₹6,200

Recent average:
₹3,900

Food budget:
₹5,000

WealthWise detects:

Food spending increase
+
Budget overrun

The Insight Engine produces:

Food spending is significantly above your recent average and is currently above your budget.

The user then asks:

What if I reduce food spending by 20%?

The Scenario Engine calculates:

Potential reduction:
₹1,240

Projected food spending:
₹4,960

The user asks:

Would that help my travel goal?

The system retrieves:

Travel Goal
+
Projected Savings Improvement

AI explains:

Reducing food spending by approximately ₹1,240 could increase the amount available toward your travel goal, assuming the saved amount is actually set aside.

This single flow demonstrates the core WealthWise concept.

71. The MVP "Wow" Moment

The MVP should create a moment where the user realizes:

"This isn't just showing me my expenses. It actually understands what is happening with my money."

That moment should come from the chain:

My Transactions
      ↓
My Behaviour
      ↓
My Goal
      ↓
My Problem
      ↓
What-If Scenario
      ↓
Potential Solution

This is more important than adding dozens of unrelated features.

72. MVP Quality Bar

The MVP should prioritize:

Correctness
>
Clarity
>
Reliability
>
Explainability
>
Feature Count

A smaller system that correctly understands a user's finances is preferable to a large system filled with unreliable AI features.

73. Scope Freeze Rule

Once Milestone 1 begins:

New feature ideas do not automatically enter the MVP.

Each new feature must be classified as:

MVP
Post-MVP
Future
Rejected

The Product Bible and MVP Scope document should be updated accordingly.

74. Definition of Done

A feature is considered Done only when:

Requirement
    ↓
Implementation
    ↓
API
    ↓
Database
    ↓
Validation
    ↓
Security
    ↓
Error Handling
    ↓
UI
    ↓
Testing
    ↓
Documentation

has been completed to the required level.

75. MVP Architecture Boundary

The MVP architecture is intentionally:

React
   ↓
REST API
   ↓
Node.js / Express
   ↓
Domain Services
   ↓
MongoDB

                 +
                 
           AI Provider
                 ↑
          Context Builder
                 ↑
        Financial Intelligence
76. MVP Product Loop

The final MVP loop is:

             ┌──────────────────┐
             │ TRANSACTION DATA │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │    ANALYTICS     │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │    BEHAVIOUR     │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │ GOAL / BUDGET    │
             │     CONTEXT       │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │     INSIGHT      │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │     SCENARIO     │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │    AI ADVISOR    │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │ USER DECISION    │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │ FUTURE BEHAVIOUR │
             └────────┬─────────┘
                      │
                      └──────────────→ NEW DATA
77. Final MVP Principle

The MVP should answer one question convincingly:

Can WealthWise turn personal financial records into understandable, personalized and actionable financial intelligence?

If the answer is yes, the MVP is successful.