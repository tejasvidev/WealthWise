# WealthWise — User Flows

**Document Version:** 1.0  
**Status:** Product Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Technology Direction:** MERN + Generative AI

---

# 1. Purpose

This document defines the primary user journeys and interaction flows within WealthWise.

It describes:

- how users enter the application,
- how users provide financial data,
- how financial data becomes intelligence,
- how users interact with budgets and goals,
- how insights are surfaced,
- how scenarios are explored,
- how users interact with the AI Advisor.

The purpose is to establish the behavioral blueprint of the product before UI implementation begins.

---

# 2. User Flow Philosophy

WealthWise should feel like a continuous financial intelligence experience rather than a collection of disconnected pages.

The fundamental journey is:

```text
USER
 ↓
FINANCIAL DATA
 ↓
UNDERSTANDING
 ↓
INTELLIGENCE
 ↓
INSIGHT
 ↓
SCENARIO
 ↓
DECISION

3. Primary User Journey

The primary WealthWise journey is:

Landing Page
     ↓
Register / Login
     ↓
Onboarding
     ↓
Add or Import Transactions
     ↓
Transaction Review
     ↓
Dashboard
     ↓
Explore Financial Behaviour
     ↓
Create Budget
     ↓
Create Goal
     ↓
Receive Insight
     ↓
Explore Scenario
     ↓
Ask AI Advisor
     ↓
Make Financial Decision
4. Application Entry Flow
4.1 Unauthenticated User
                    OPEN WEALTHWISE
                           │
                           ▼
                     Landing Page
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                 Login         Register
4.2 Authenticated User
Open WealthWise
      ↓
Authentication Check
      ↓
Authenticated?
   /       \
 YES        NO
  ↓          ↓
Dashboard   Landing/Login
5. Flow F-001 — Registration
Goal

Allow a new user to create a WealthWise account.

Flow
Registration Page
       ↓
Enter Name
       ↓
Enter Email
       ↓
Enter Password
       ↓
Client Validation
       ↓
Submit
       ↓
Backend Validation
       ↓
Email Already Exists?
      /       \
    YES        NO
     ↓          ↓
 Show Error   Create User
                  ↓
           Authentication
                  ↓
              Onboarding
Success

User enters the WealthWise onboarding flow.

Failure

User remains on the registration screen with a meaningful error.

6. Flow F-002 — Login
Login Page
    ↓
Email + Password
    ↓
Validation
    ↓
Authentication API
    ↓
Credentials Valid?
   /        \
 NO          YES
 ↓            ↓
Error      Create Session
               ↓
           Dashboard
7. Flow F-003 — Logout
Dashboard
    ↓
Profile / Logout
    ↓
Authentication State Cleared
    ↓
Landing Page
8. Flow F-004 — First-Time Onboarding

The first-time user should not immediately be confronted with an empty dashboard.

The onboarding experience should explain:

WealthWise becomes more useful as it understands your financial activity.

Onboarding Flow
Account Created
      ↓
Welcome
      ↓
Select Currency
      ↓
Add Financial Data
      ↓
┌─────┴────────┐
▼              ▼
Manual       CSV Import
Entry
└─────┬────────┘
      ↓
Review Data
      ↓
Create Optional Goal
      ↓
Create Optional Budget
      ↓
Generate Initial Analysis
      ↓
Dashboard
9. Onboarding Principle

The onboarding flow should not force users to provide unnecessary information.

Required:

Account
Currency
Initial financial data

Optional:

Budget
Goal
Additional preferences
10. Flow F-005 — Add First Transaction
Dashboard / Transactions
        ↓
Add Transaction
        ↓
Transaction Form
        ↓
Enter:
Amount
Type
Date
Description
Category
        ↓
Submit
        ↓
Validation
        ↓
Transaction Created
        ↓
Analytics Updated
        ↓
Dashboard Updated
11. Flow F-006 — Import CSV
Transactions
     ↓
Import CSV
     ↓
Select File
     ↓
File Validation
     ↓
Parse CSV
     ↓
Column Mapping / Format Detection
     ↓
Row Validation
     ↓
Categorization
     ↓
Duplicate Detection
     ↓
Import Preview
     ↓
User Confirmation
     ↓
Persist Valid Transactions
     ↓
Import Summary
     ↓
Financial Intelligence Refresh
12. CSV Import Review

The user should see a preview before final import.

Example:

┌──────────────────────────────────────┐
│ Import Preview                       │
├──────────────────────────────────────┤
│ Total Rows        1000               │
│ Valid              950               │
│ Duplicates          40               │
│ Invalid             10               │
├──────────────────────────────────────┤
│              [Cancel] [Import]       │
└──────────────────────────────────────┘
13. Flow F-007 — Invalid CSV
Upload
  ↓
Validation
  ↓
Invalid File
  ↓
Show Problem
  ↓
User Corrects File
  ↓
Upload Again

The system should explain what went wrong.

Example:

The amount column contains invalid values in 10 rows.

14. Flow F-008 — Transaction Review

After importing data:

Import Complete
      ↓
Transaction List
      ↓
Review Categories
      ↓
User Corrects Categories if Necessary
      ↓
Save

This is especially important when AI-assisted categorization is used.

15. Flow F-009 — Transaction Search
Transactions
      ↓
Search
      ↓
Enter Keyword
      ↓
Backend Query
      ↓
Filtered Transactions

Example:

Search:
Uber

Results:
Uber
Uber Eats
Uber Ride
16. Flow F-010 — Transaction Filtering
Transactions
      ↓
Filter
      ↓
Select:
Category
Type
Date
Amount
Merchant
      ↓
Apply
      ↓
Filtered Results

Filters should be combinable.

Example:

Category = Food
+
Amount > ₹500
+
Current Month
17. Flow F-011 — Edit Transaction
Transaction
      ↓
Open Details
      ↓
Edit
      ↓
Modify Fields
      ↓
Validate
      ↓
Save
      ↓
Recalculate Affected Intelligence
      ↓
Updated Transaction
18. Flow F-012 — Delete Transaction
Transaction
      ↓
Delete
      ↓
Confirmation
      ↓
Delete API
      ↓
Transaction Removed
      ↓
Affected Analytics Updated

Confirmation should prevent accidental deletion.

19. Flow F-013 — Dashboard Exploration

The dashboard is the central navigation point.

Dashboard
   │
   ├── Financial Summary
   ├── Spending Analytics
   ├── Savings
   ├── Budgets
   ├── Goals
   ├── Insights
   └── AI Advisor
20. Dashboard First Impression

The dashboard should answer five questions quickly:

1. How much did I earn?

2. How much did I spend?

3. How much did I save?

4. What is changing?

5. What deserves my attention?
21. Flow F-014 — Explore Spending
Dashboard
    ↓
Expense Analytics
    ↓
Select Category
    ↓
Category Details
    ↓
Merchant Breakdown
    ↓
Underlying Transactions

Example:

Expenses
   ↓
Food
   ↓
Restaurants
   ↓
Individual Transactions
22. Flow F-015 — Explore Spending Trend
Dashboard
      ↓
Spending Trend
      ↓
Select Period
      ↓
Trend Visualization
      ↓
Select Category
      ↓
Category Trend
23. Flow F-016 — Category Drill-Down
Category Chart
      ↓
Click "Food"
      ↓
Food Spending
      ↓
Current vs Historical
      ↓
Relevant Transactions

The user should be able to move from an abstract chart to actual evidence.

24. Flow F-017 — Create Budget
Budget Page
    ↓
Create Budget
    ↓
Select Category
    ↓
Enter Amount
    ↓
Select Period
    ↓
Save
    ↓
Budget Created
    ↓
Current Spending Compared
25. Flow F-018 — Monitor Budget
Budget
   ↓
Current Spending
   ↓
Calculate Usage
   ↓
┌───────────────┐
│ Within Budget │
└───────────────┘

or

┌──────────────────┐
│ Approaching Limit│
└──────────────────┘

or

┌──────────────┐
│ Over Budget  │
└──────────────┘
26. Flow F-019 — Budget Alert / Insight
Budget
  ↓
Spending Changes
  ↓
Usage Threshold
  ↓
Significance Check
  ↓
Insight

Example:

Food Budget: ₹5,000
Current Spending: ₹4,600

Usage: 92%

Insight:
You're approaching your food budget.
27. Flow F-020 — Create Financial Goal
Goals
  ↓
Create Goal
  ↓
Goal Name
  ↓
Target Amount
  ↓
Current Amount
  ↓
Target Date
  ↓
Purpose
  ↓
Save
  ↓
Goal Created
  ↓
Calculate Progress
28. Flow F-021 — Goal Tracking
Goal
  ↓
Current Amount
  ↓
Target Amount
  ↓
Progress %
  ↓
Remaining Amount
  ↓
Required Contribution
  ↓
Goal Status
29. Flow F-022 — Goal Feasibility
Goal
  ↓
Remaining Amount
  ↓
Time Remaining
  ↓
Required Savings
  ↓
Historical Savings Capacity
  ↓
Feasibility Analysis
  ↓
On Track / At Risk / Behind
30. Flow F-023 — Goal-Aware Insight

Example:

Goal:
Travel — ₹50,000

Current:
₹30,000

Remaining:
₹20,000

Current savings pace:
₹4,000/month

System evaluates:

Goal
+
Savings Behaviour
+
Time Remaining

Then generates an appropriate signal.

31. Flow F-024 — Behaviour Detection

Behaviour analysis runs after relevant financial data changes or during an analysis refresh.

Transaction Data
      ↓
Aggregate Data
      ↓
Calculate Metrics
      ↓
Compare With Baseline
      ↓
Detect Changes
      ↓
Classify Signal
32. Behaviour Signal Example
Historical Average:
₹3,900

Current:
₹6,200

Deviation:
+59%

Signal:
FOOD_SPENDING_INCREASE
33. Flow F-025 — Insight Generation
Behaviour Signal
      ↓
Significance Evaluation
      ↓
Is It Meaningful?
     /      \
   NO        YES
   ↓          ↓
Ignore     Create Insight
               ↓
        Optional AI Explanation
               ↓
            Dashboard
34. Flow F-026 — View Insight
Dashboard
    ↓
Insight Card
    ↓
Open Insight
    ↓
Insight Details

The details should show:

What happened?
Why it matters?
Evidence
Relevant period
Relevant budget
Relevant goal
Possible action
35. Flow F-027 — Insight Evidence

Example:

Insight:
Food spending increased significantly.

        ↓

Evidence

Current:
₹6,200

Recent Average:
₹3,900

Difference:
₹2,300

Budget:
₹5,000

Over Budget:
₹1,200

This makes the insight explainable.

36. Flow F-028 — Scenario Entry

Scenarios may be started from:

Dashboard
Insight
Goal
Budget
AI Advisor
Scenario Page
37. Flow F-029 — Scenario From Insight

This is a particularly important WealthWise flow.

Insight
   ↓
"Food spending is above your normal level."
   ↓
Explore What-If
   ↓
Scenario Setup

Example:

What if I reduce food spending by 20%?

38. Flow F-030 — Scenario Calculation
Scenario Request
      ↓
Current Financial State
      ↓
Apply Hypothetical Change
      ↓
Calculate Projected State
      ↓
Compare With Baseline
      ↓
Calculate Goal Impact
      ↓
Return Result
39. Flow F-031 — Scenario Result

Example:

Current Food Spending
₹6,200

Scenario Reduction
20%

Projected Food Spending
₹4,960

Potential Reduction
₹1,240

Then:

Goal Impact
Potentially ₹1,240 additional monthly capacity

The system should distinguish potential capacity from guaranteed savings.

40. Flow F-032 — Scenario to AI
Scenario Result
      ↓
Ask AI to Explain
      ↓
Structured Scenario Data
      ↓
AI Context
      ↓
Explanation

Example:

How would this help my travel goal?

41. Flow F-033 — AI Advisor Entry

The AI Advisor can be accessed from:

Navigation
Dashboard
Insight
Goal
Budget
Scenario
42. Flow F-034 — Basic AI Question
AI Advisor
    ↓
User Question
    ↓
Intent Classification
    ↓
Context Selection
    ↓
Retrieve Financial Facts
    ↓
Build Context
    ↓
AI Request
    ↓
Response Validation
    ↓
Display Response
43. Flow F-035 — AI Financial Summary

User:

Give me a summary of my finances.

Flow:

Question
   ↓
Intent:
FINANCIAL_SUMMARY
   ↓
Retrieve:
Income
Expenses
Savings
Savings Rate
Top Categories
Budgets
Goals
Insights
   ↓
Structured Context
   ↓
AI
   ↓
Summary
44. Flow F-036 — AI Spending Question

User:

Where am I spending the most?

Question
   ↓
Intent:
CATEGORY_ANALYSIS
   ↓
Retrieve Category Metrics
   ↓
Rank Categories
   ↓
AI Explanation
   ↓
Response
45. Flow F-037 — AI Behaviour Question

User:

Why am I spending more this month?

Question
   ↓
Intent:
BEHAVIOUR_ANALYSIS
   ↓
Retrieve:
Current Spending
Historical Baseline
Category Changes
   ↓
Identify Significant Changes
   ↓
AI Explanation
46. Flow F-038 — AI Goal Question

User:

Am I on track for my travel goal?

Question
   ↓
Intent:
GOAL_ANALYSIS
   ↓
Retrieve Goal
   ↓
Retrieve Savings Behaviour
   ↓
Calculate Required Contribution
   ↓
Determine Goal Status
   ↓
AI Explanation
47. Flow F-039 — AI Recommendation Question

User:

What should I improve?

Question
   ↓
Retrieve Significant Signals
   ↓
Rank Relevant Issues
   ↓
Retrieve Goal/Budget Context
   ↓
AI
   ↓
Prioritized Recommendations
48. Flow F-040 — AI Scenario Question

User:

What if I reduce shopping by ₹2,000?

Question
   ↓
Identify Scenario Intent
   ↓
Extract:
Category = Shopping
Change = -₹2,000
   ↓
Scenario Engine
   ↓
Projected Result
   ↓
AI Explanation

The AI does not perform the arithmetic itself.

49. Flow F-041 — Conversational Follow-Up

Example:

User:
Why are my expenses higher?

AI:
Food and shopping increased.

User:
What if I reduce shopping?

        ↓

Conversation Context
        +
Current Financial Data
        ↓

Scenario Engine

The system should understand "shopping" from the conversation context where possible.

50. Flow F-042 — AI Insufficient Data

User:

Why has my spending increased compared with previous months?

If the user has only one month of history:

Question
   ↓
Context Check
   ↓
Insufficient History
   ↓
AI Response

Example:

I don't have enough historical data yet to identify a reliable spending trend. Keep adding transactions and I'll be able to compare your behaviour over time.

51. Flow F-043 — AI Provider Failure
User Question
      ↓
AI Provider
      ↓
Failure
      ↓
Fallback Response

Example:

The AI advisor is temporarily unavailable. Your financial data and analytics are still available.

No financial state is affected.

52. Flow F-044 — AI Unsupported Question

User:

Buy me ₹10,000 worth of stocks.

MVP response:

AI understands the request
      ↓
Recognizes unsupported action
      ↓
Does not execute
      ↓
Explains limitation

Example:

WealthWise can help you analyze your finances and explore scenarios, but it cannot execute investments or financial transactions.

53. Flow F-045 — Cross-Feature Journey

The most important product flow is:

Dashboard
   ↓
Insight
   ↓
Evidence
   ↓
Scenario
   ↓
Goal Impact
   ↓
AI Explanation
   ↓
Recommendation

This should be treated as a flagship user journey.

54. Flagship Journey Example
Step 1 — Dashboard

User sees:

Food spending ↑ 59%
Step 2 — Insight

User opens:

Food spending is significantly above your recent average.

Step 3 — Evidence
Current:
₹6,200

Average:
₹3,900

Budget:
₹5,000
Step 4 — Scenario

User selects:

Explore reducing food spending by 20%.

Step 5 — Scenario Result
Potential reduction:
₹1,240
Step 6 — Goal Impact

User has:

Travel Goal:
₹20,000 remaining

System calculates potential effect.

Step 7 — AI

User asks:

Would this help me reach my goal?

AI explains using the verified scenario result.

Step 8 — Decision

User decides whether the recommendation is realistic.

55. Flow F-046 — Dashboard to AI
Dashboard
    ↓
AI Advisor
    ↓
Suggested Questions

Suggested questions may include:

Why did my spending change?

What should I focus on?

Am I on track for my goals?

Where can I save more?

These should be generated from available context rather than being random prompts.

56. Flow F-047 — Goal to Scenario
Goal
 ↓
Goal Status
 ↓
At Risk
 ↓
Explore What Could Help
 ↓
Scenario

Example:

What if I save ₹2,000 more each month?

57. Flow F-048 — Budget to Scenario
Budget
 ↓
Over Budget
 ↓
Explore Reduction
 ↓
Scenario

Example:

What if I reduce this category by 15%?

58. Flow F-049 — Insight to AI
Insight
 ↓
Ask WealthWise
 ↓
AI receives insight context
 ↓
User asks follow-up

Example:

Why does this matter?

59. Flow F-050 — AI to Evidence

The AI should allow the user to move from explanation back to evidence where appropriate.

AI Response
    ↓
View Data
    ↓
Relevant Analytics
    ↓
Relevant Transactions

This strengthens trust.

60. Flow F-051 — Financial Data Update

Whenever a relevant financial mutation occurs:

Transaction Added
Transaction Edited
Transaction Deleted
Budget Changed
Goal Changed

the system should determine affected intelligence.

Conceptually:

Mutation
   ↓
Affected Domain
   ↓
Recalculate / Invalidate
   ↓
Updated Analytics
   ↓
Updated Behaviour
   ↓
Updated Insights
61. Flow F-052 — Transaction Added
Add Transaction
      ↓
Persist
      ↓
Update Analytics
      ↓
Update Budget
      ↓
Update Goal Context
      ↓
Recalculate Behaviour
      ↓
Refresh Relevant Insights

Not every downstream operation must necessarily happen synchronously; implementation may optimize this later.

62. Flow F-053 — Goal Updated
Update Goal
    ↓
Persist
    ↓
Recalculate Goal Progress
    ↓
Recalculate Goal Feasibility
    ↓
Update Goal-Related Insights
63. Flow F-054 — Budget Updated
Update Budget
    ↓
Persist
    ↓
Recalculate Budget Status
    ↓
Update Budget Insights
64. Flow F-055 — Empty Dashboard

For a new user:

Dashboard
    ↓
No Transactions

Display:

Your financial picture starts here.

Add your first transaction or import your history to begin.

Primary actions:

[Add Transaction]
[Import CSV]
65. Flow F-056 — No Goals
Dashboard
    ↓
No Goals

Display:

Give your money a direction by creating your first financial goal.

CTA:

[Create Goal]
66. Flow F-057 — No Budgets
Dashboard
    ↓
No Budgets

Display:

Set a spending limit for a category to start tracking your budget.

CTA:

[Create Budget]
67. Flow F-058 — No Insights

If no meaningful signals exist:

Insights
    ↓
No Significant Insights

Display:

Nothing important needs your attention right now.

This should not imply that the user's finances are perfect.

It only means:

No significant system-detected signal is currently available.

68. Flow F-059 — Insufficient Behaviour Data
Transactions
    ↓
Insufficient History

Display:

WealthWise needs more history before it can confidently identify changes in your spending behaviour.

69. Flow F-060 — User Deletes Account

Future MVP-compatible conceptual flow:

Settings
    ↓
Delete Account
    ↓
Confirmation
    ↓
Warning
    ↓
Re-authentication if required
    ↓
Delete User Data
    ↓
Logout
    ↓
Landing Page

The exact retention policy is defined in the security architecture.

70. Navigation Flow

The primary application navigation should conceptually be:

┌───────────────────────────────────────────┐
│                 WEALTHWISE                │
├───────────────────────────────────────────┤
│                                           │
│ Dashboard                                 │
│ Transactions                              │
│ Analytics                                 │
│ Budgets                                   │
│ Goals                                     │
│ Insights                                  │
│ Scenarios                                 │
│ AI Advisor                                │
│                                           │
│ Settings                                  │
│                                           │
└───────────────────────────────────────────┘

The exact UI structure will be defined separately.

71. Page Relationship
Dashboard
   │
   ├── Transactions
   │      └── Transaction Details
   │
   ├── Analytics
   │      └── Category Details
   │
   ├── Budgets
   │      └── Budget Details
   │
   ├── Goals
   │      └── Goal Details
   │
   ├── Insights
   │      └── Insight Details
   │
   ├── Scenarios
   │      └── Scenario Results
   │
   └── AI Advisor
          └── Conversations
72. Global User Flow

The complete system can be represented as:

                           USER
                            │
                            ▼
                       AUTHENTICATE
                            │
                            ▼
                         ONBOARD
                            │
                            ▼
                    PROVIDE FINANCIAL DATA
                            │
                ┌───────────┴───────────┐
                ▼                       ▼
             MANUAL                  CSV
              DATA                  IMPORT
                │                       │
                └───────────┬───────────┘
                            ▼
                      TRANSACTIONS
                            │
                            ▼
                       ANALYTICS
                            │
                ┌───────────┼───────────┐
                ▼           ▼           ▼
             BUDGET       GOALS      BEHAVIOUR
                │           │           │
                └───────────┼───────────┘
                            ▼
                         INSIGHTS
                            │
                 ┌──────────┴──────────┐
                 ▼                     ▼
              SCENARIO             AI ADVISOR
                 │                     │
                 └──────────┬──────────┘
                            ▼
                       RECOMMENDATION
                            │
                            ▼
                      USER DECISION
                            │
                            ▼
                    FUTURE BEHAVIOUR
                            │
                            ▼
                      NEW DATA
                            │
                            └────────────→ LOOP
73. Core Flow Priority

Not all user journeys have equal importance.

Tier 1 — Critical
Registration
Login
Add Transaction
Import CSV
Dashboard
Analytics
Create Goal
Create Budget
Insight
AI Advisor
Scenario
Tier 2 — Important
Search
Filtering
Drill-down
Goal Analysis
Budget Analysis
Insight Evidence
AI Follow-up
Tier 3 — Future
Bank Integration
Notifications
Advanced Forecasting
Natural Language Search
Autonomous Actions
74. UX Principle — Progressive Disclosure

WealthWise should not show every piece of financial information at once.

The user should move from:

Summary
   ↓
Insight
   ↓
Explanation
   ↓
Evidence
   ↓
Action

Example:

Food spending is up.
        ↓
Why?
        ↓
59% above recent average.
        ↓
Show evidence.
        ↓
What can I do?
        ↓
Explore scenario.
75. UX Principle — Data to Decision

The ideal WealthWise flow is:

DATA
 ↓
WHAT HAPPENED?
 ↓
WHY?
 ↓
WHY DOES IT MATTER?
 ↓
WHAT CAN I DO?
 ↓
WHAT IF I DO IT?

This sequence should influence the eventual UI design.

76. UX Principle — Never Force AI

AI should be available when useful but should not be required for basic financial operations.

Users should be able to:

View transactions
View analytics
Create budgets
Create goals
View insights
Run scenarios

without interacting with AI.

77. UX Principle — AI as a Layer

The AI Advisor should sit on top of the financial intelligence system.

             AI ADVISOR
                 ▲
                 │
        Financial Intelligence
                 ▲
                 │
             Analytics
                 ▲
                 │
           Transactions

Not:

Transactions
     ↓
AI
     ↓
Everything
78. UX Principle — Evidence Before Advice

When WealthWise gives a meaningful recommendation:

Evidence
   ↓
Interpretation
   ↓
Recommendation

rather than:

Recommendation
   ↓
Trust me
79. UX Principle — User Remains in Control

The product should support:

Understand
Explore
Compare
Decide

rather than:

AI decides
80. Flagship End-to-End Flow

The single most important flow in WealthWise is:

                DASHBOARD
                    │
                    ▼
             "Food spending ↑"
                    │
                    ▼
                 INSIGHT
                    │
                    ▼
              SHOW EVIDENCE
                    │
                    ▼
            "Explore What-If"
                    │
                    ▼
                SCENARIO
                    │
                    ▼
            PROJECTED IMPACT
                    │
                    ▼
                GOAL IMPACT
                    │
                    ▼
                ASK AI
                    │
                    ▼
              EXPLANATION
                    │
                    ▼
             RECOMMENDATION
                    │
                    ▼
              USER DECISION

This flow should become one of the primary demonstration flows of the major project.

81. User Flow Completion Criteria

A flow is considered complete when:

Entry Point
    ↓
User Action
    ↓
System Processing
    ↓
Success / Failure State
    ↓
Next Logical Action

has been defined.

For intelligence flows:

Input
 ↓
Financial Calculation
 ↓
Context
 ↓
Insight / Scenario / AI
 ↓
Result

must also be defined.

82. Summary

WealthWise is designed around a progression:

RECORD
  ↓
UNDERSTAND
  ↓
NOTICE
  ↓
EXPLAIN
  ↓
SIMULATE
  ↓
DECIDE

The strongest product flow is:

A financial event becomes an insight, the insight becomes a question, the question becomes a scenario, and the scenario helps the user make a better decision.

That is the interaction model that differentiates WealthWise from a conventional expense tracker.