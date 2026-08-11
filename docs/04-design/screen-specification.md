# WealthWise — Screen Specification

**Document Version:** 1.0  
**Status:** Product Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Technology Direction:** MERN + Generative AI

---

# 1. Purpose

This document defines the interface-level specification for WealthWise.

It establishes:

- application screens,
- screen responsibilities,
- major UI sections,
- user actions,
- navigation,
- data displayed,
- loading states,
- empty states,
- error states,
- relationships between screens.

This document does **not** define the final visual design.

It defines **what each screen must accomplish**.

---

# 2. Screen Architecture

The WealthWise application is divided into:

```text
Public Screens
│
├── Landing
├── Login
└── Registration
│
Authenticated Screens
│
├── Onboarding
├── Dashboard
├── Transactions
├── Analytics
├── Budgets
├── Goals
├── Insights
├── Scenarios
├── AI Advisor
└── Settings

3. Screen Inventory
ID	Screen	Priority	MVP
S-001	Landing	High	Yes
S-002	Login	Critical	Yes
S-003	Registration	Critical	Yes
S-004	Onboarding	Critical	Yes
S-005	Dashboard	Critical	Yes
S-006	Transactions	Critical	Yes
S-007	Transaction Form	Critical	Yes
S-008	Import Transactions	Critical	Yes
S-009	Import Review	Critical	Yes
S-010	Analytics	Critical	Yes
S-011	Budget List	High	Yes
S-012	Budget Details	High	Yes
S-013	Goal List	Critical	Yes
S-014	Goal Details	Critical	Yes
S-015	Insights	Critical	Yes
S-016	Insight Details	Critical	Yes
S-017	Scenarios	Critical	Yes
S-018	Scenario Result	Critical	Yes
S-019	AI Advisor	Critical	Yes
S-020	Settings	Medium	Yes
4. Global Application Layout

Authenticated screens should share a consistent application shell.

Conceptually:

┌──────────────────────────────────────────────────────────────┐
│ WealthWise                         Search   AI   Profile     │
├───────────────┬──────────────────────────────────────────────┤
│               │                                              │
│ Dashboard     │                                              │
│ Transactions  │                                              │
│ Analytics     │                MAIN CONTENT                  │
│ Budgets       │                                              │
│ Goals         │                                              │
│ Insights      │                                              │
│ Scenarios     │                                              │
│ AI Advisor    │                                              │
│               │                                              │
│ Settings      │                                              │
│               │                                              │
└───────────────┴──────────────────────────────────────────────┘

The exact layout may evolve during UI design.

5. Global Navigation

Primary navigation:

Dashboard
Transactions
Analytics
Budgets
Goals
Insights
Scenarios
AI Advisor

Secondary navigation:

Settings
Profile
Logout
6. Global UI Principles

Every screen should follow these principles.

6.1 Clarity

Financial information should be understandable without requiring financial expertise.

6.2 Progressive Disclosure

Show the most important information first.

Summary
  ↓
Insight
  ↓
Details
  ↓
Evidence
6.3 Actionability

Important information should lead naturally to a useful action.

6.4 Consistency

The same financial concepts should look and behave consistently throughout the application.

6.5 Trust

Users should be able to understand where important numbers and insights came from.

7. S-001 — Landing Page
Purpose

Introduce WealthWise to unauthenticated users.

Primary Message

Understand your money. Improve your decisions.

Main Content
Hero
Product Explanation
Core Intelligence Model
Key Features
How It Works
AI Advisor Preview
CTA
Primary Actions
Get Started
Login
8. Landing Page Hero

The hero should communicate the difference between tracking and intelligence.

Example:

Your money already tells a story.
WealthWise helps you understand it.

Understand your spending.
See what is changing.
Explore what could happen.
Make better financial decisions.

CTA:

[Start with WealthWise]
9. S-002 — Login
Purpose

Authenticate an existing user.

Components
Email Input
Password Input
Login Button
Forgot Password
Register Link
States
Default
Loading
Invalid Credentials
Validation Error
Server Error
Success
10. Login Error Handling

Example:

Invalid credentials.
Please check your email and password.

Do not expose whether a particular email exists unnecessarily.

11. S-003 — Registration
Purpose

Create a WealthWise account.

Components
Name
Email
Password
Confirm Password
Register Button
Login Link
States
Default
Validation Error
Email Already Used
Loading
Server Error
Success
12. S-004 — Onboarding
Purpose

Help first-time users provide enough information for WealthWise to become useful.

Steps
Welcome
  ↓
Preferences
  ↓
Financial Data
  ↓
Optional Goal
  ↓
Optional Budget
  ↓
Initial Analysis
13. Onboarding — Welcome

Message:

Welcome to WealthWise.

Supporting message:

Let's turn your financial data into something you can actually understand.

CTA:

[Get Started]
14. Onboarding — Preferences

Initial fields:

Currency

Optional:

Timezone

The system should infer reasonable defaults where possible.

15. Onboarding — Financial Data

Primary choices:

[Add Transactions Manually]
[Import CSV]

The user may skip this step.

If skipped:

You can add your financial data anytime.

16. Onboarding — Goal

Optional prompt:

Is there something you're currently saving for?

Examples:

Travel
Education
Emergency Fund
Major Purchase
Other

CTA:

[Create Goal]
[Skip]
17. Onboarding — Budget

Optional prompt:

Want to set your first spending limit?

Example:

Food
₹5,000 / month

CTA:

[Create Budget]
[Skip]
18. Onboarding — Initial Analysis

After data is available:

Processing your financial data...

Conceptually:

Transactions
      ↓
Categorization
      ↓
Analytics
      ↓
Behaviour Analysis
      ↓
Initial Insights

The user is then taken to the dashboard.

19. S-005 — Dashboard
Purpose

Provide the user's financial state at a glance.

The dashboard is the central screen of WealthWise.

20. Dashboard Information Hierarchy

The dashboard should communicate:

1. Financial Snapshot
2. What Changed?
3. What Needs Attention?
4. Goals
5. Budgets
6. AI Assistance
21. Dashboard — Financial Snapshot

Required cards:

Income
Expenses
Savings
Savings Rate

Example:

Income
₹60,000

Expenses
₹42,000

Savings
₹18,000

Savings Rate
30%
22. Dashboard — Period Selector

Users should be able to change the financial period.

Options:

This Month
Last Month
Last 3 Months
Last 6 Months
This Year
Custom Range

Changing the period should update applicable dashboard metrics.

23. Dashboard — Spending Trend

Display:

Current Spending
Historical Spending
Trend

Primary goal:

Quickly show whether spending is increasing, decreasing, or stable.

24. Dashboard — Expense Distribution

Show major categories.

Example:

Food          ₹6,200
Transport     ₹4,000
Shopping      ₹3,500
Bills         ₹5,000
Entertainment ₹2,000

The user can select a category to drill down.

25. Dashboard — Important Insights

Display the most relevant insights.

Example:

Food spending is above your recent average.

Your travel goal is currently on track.

Shopping budget is approaching its limit.

Each insight should be clickable.

26. Dashboard — Goals

Show compact goal cards.

Example:

Travel Goal

₹30,000 / ₹50,000

60% complete

On Track

CTA:

[View Goal]
27. Dashboard — Budgets

Show important budgets.

Example:

Food
₹4,600 / ₹5,000

92% used

Approaching Limit
28. Dashboard — AI Entry

Provide a conversational entry point.

Example:

Ask WealthWise anything about your finances...

[ Why did my spending increase? ]

Suggested questions should be context-aware.

29. Dashboard — Primary Actions

Possible primary actions:

Add Transaction
Import CSV
Create Budget
Create Goal
Ask WealthWise
30. Dashboard Empty State

When no transactions exist:

Your financial picture starts here.

Add your first transaction or import your history
to start understanding your money.

Actions:

[Add Transaction]
[Import CSV]
31. S-006 — Transactions
Purpose

Provide complete visibility into raw financial records.

32. Transactions Layout
Header
  ↓
Search + Filters
  ↓
Summary
  ↓
Transaction Table
  ↓
Pagination
33. Transaction Table

Columns:

Date
Description
Merchant
Category
Type
Amount
Actions

Example:

08 Aug | Dinner | Restaurant | Food | Expense | ₹850
07 Aug | Salary | Company | Income | Income | ₹60,000
34. Transaction Actions

Each transaction may support:

View
Edit
Delete
35. Transaction Filters

Filters:

Date
Category
Type
Merchant
Amount
36. Transaction Search

Search should support common fields:

Description
Merchant
Category
37. Transaction Empty State
No transactions found.

Try changing your filters or add a new transaction.
38. S-007 — Transaction Form

The form is used for:

Create
Edit
39. Transaction Form Fields
Amount
Type
Date
Description
Merchant
Category
Notes
40. Transaction Form UX

Required fields should be clearly identified.

The form should provide:

Validation
Helpful Errors
Submit State
Success Feedback
41. S-008 — Import Transactions
Purpose

Allow users to upload financial data.

42. Import Screen

Main sections:

Upload Area
Supported Format
Instructions
File Selection

Example:

Drop your CSV here

or

[Choose File]
43. Import Instructions

Provide a downloadable/example format conceptually:

date
description
amount
type
category
merchant
44. Import Loading State
Uploading...
Parsing transactions...
Validating records...

The UI should communicate progress where practical.

45. S-009 — Import Review
Purpose

Allow users to verify imported data before persistence.

46. Import Review Summary

Display:

Total Rows
Valid Rows
Duplicates
Invalid Rows

Example:

1,000 total

950 valid
40 duplicates
10 invalid
47. Import Review Table

Show representative records:

Date
Description
Amount
Type
Category
Status

Status examples:

Valid
Duplicate
Invalid
48. Import Actions
[Cancel]
[Import Valid Transactions]
49. S-010 — Analytics
Purpose

Provide deeper financial analysis.

50. Analytics Sections
Overview
Expenses
Income
Savings
Categories
Trends
51. Analytics — Overview

Display:

Income
Expenses
Savings
Savings Rate
52. Analytics — Expense Analysis

Show:

Total Expenses
Category Distribution
Category Ranking
Historical Trend
53. Analytics — Income Analysis

Show:

Total Income
Income Trend
Income Sources
54. Analytics — Savings Analysis

Show:

Current Savings
Savings Rate
Savings Trend
Average Savings
55. Analytics — Category Drill-Down

Selecting a category should show:

Category Total
Historical Comparison
Merchant Breakdown
Relevant Transactions
56. Analytics — Time Comparison

Possible comparisons:

Current Month vs Previous Month
Current Month vs Recent Average
Current Period vs Previous Period
57. S-011 — Budget List
Purpose

Show all user budgets.

58. Budget Card

Each budget should show:

Category
Budget
Spent
Remaining
Usage %
Status

Example:

Food

₹4,600 / ₹5,000

92%

Approaching Limit
59. Budget List Actions
Create
View
Edit
Delete
60. Budget Empty State
No budgets yet.

Set a spending limit to start tracking where your money goes.

CTA:

[Create Budget]
61. S-012 — Budget Details

Display:

Budget Amount
Current Spending
Remaining
Usage %
Historical Trend
Relevant Transactions
Status
62. Budget Details — Scenario CTA

When appropriate:

[Explore What-If]

Example:

What if I reduce this category by 15%?

63. S-013 — Goal List
Purpose

Provide an overview of financial goals.

64. Goal Card

Display:

Goal Name
Current Amount
Target Amount
Progress %
Target Date
Status

Example:

Travel

₹30,000 / ₹50,000

60%

December 2026

On Track
65. Goal List Actions
Create
View
Edit
Delete
66. Goal Empty State
Your money works harder when it has a direction.

Create your first financial goal.

CTA:

[Create Goal]
67. S-014 — Goal Details

Display:

Goal Name
Target
Current Amount
Remaining Amount
Progress
Target Date
Required Contribution
Goal Status
68. Goal Details — Financial Context

Show relevant information:

Current Savings Capacity
Required Contribution
Recent Savings Trend

This helps explain whether the goal is realistic.

69. Goal Details — Scenario CTA

Possible actions:

[Explore What-If]
[Ask WealthWise]

Example:

What if I save ₹2,000 more each month?

70. S-015 — Insights
Purpose

Provide a dedicated view of detected financial signals.

71. Insight Categories

Filters:

All
Spending
Savings
Budget
Goals
Behaviour
72. Insight Card

Each card should contain:

Title
Short Explanation
Severity
Period
Action

Example:

Food spending increased

Your food spending is 59% above your recent average.

Medium

[View Details]
73. Insight Prioritization

The page should prioritize:

High relevance
High impact
Recent changes
Goal-related issues
Budget-related issues
74. S-016 — Insight Details
Purpose

Explain why an insight exists.

75. Insight Details Structure
Insight Title
      ↓
What Happened?
      ↓
Why It Matters
      ↓
Evidence
      ↓
Related Budget
      ↓
Related Goal
      ↓
Potential Actions
76. Insight Evidence

Example:

Current Food Spending
₹6,200

Recent Average
₹3,900

Difference
+₹2,300

Food Budget
₹5,000

Budget Difference
+₹1,200
77. Insight Actions

Possible actions:

View Transactions
View Analytics
Explore Scenario
Ask WealthWise
78. S-017 — Scenarios
Purpose

Allow users to explore hypothetical financial decisions.

79. Scenario Entry

The screen should support inputs such as:

Category
Change Type
Change Amount / Percentage
Time Period

Example:

Category: Food

Change:
Reduce by 20%
80. Scenario Presets

Useful presets:

Reduce by 10%
Reduce by 20%
Reduce by 25%
Add ₹1,000 savings
Add ₹2,000 savings

Users may also enter custom values.

81. Scenario Calculation State

Display:

Analyzing your scenario...

The actual calculation should be performed by the deterministic Scenario Engine.

82. S-018 — Scenario Result

Display:

Current State
Scenario
Projected State
Difference
Goal Impact

Example:

Food Spending

Current:
₹6,200

Scenario:
-20%

Projected:
₹4,960

Potential Difference:
₹1,240
83. Scenario Result — Goal Impact

If a relevant goal exists:

Travel Goal

Current required contribution:
₹4,500/month

With scenario:
₹3,260/month

Potential improvement:
₹1,240/month

The wording must communicate that this is a projection, not a guarantee.

84. Scenario Result Actions
[Ask WealthWise]
[Run Another Scenario]
[Back to Dashboard]

The scenario must not automatically change real budgets or transactions.

85. S-019 — AI Advisor
Purpose

Provide conversational access to WealthWise intelligence.

86. AI Advisor Layout

Conceptually:

┌──────────────────────────────────────────────┐
│ WealthWise Advisor                           │
├──────────────────────────────────────────────┤
│                                              │
│  Conversation                               │
│                                              │
│  AI message                                  │
│                                              │
│  User message                                │
│                                              │
│  AI message                                  │
│                                              │
├──────────────────────────────────────────────┤
│ Ask about your finances...            [Send] │
└──────────────────────────────────────────────┘
87. AI Advisor Empty State

When no conversation exists:

Ask WealthWise about your money.

Try:

"Why did my spending increase?"

"Am I on track for my goal?"

"What should I improve this month?"
88. AI Advisor Suggested Questions

Suggestions should be context-aware.

For example, if a budget is nearly exhausted:

Why am I close to my budget?

What if I reduce this category?

If a goal is at risk:

Why is my goal at risk?

How can I improve my progress?
89. AI Message Structure

Where useful, AI responses may contain:

Summary
Evidence
Explanation
Recommendation
Scenario CTA
90. AI Evidence Link

Where possible:

AI Explanation
      ↓
[View Data]
      ↓
Relevant Analytics / Transactions
91. AI Loading State

Use a clear but subtle state:

Analyzing your financial data...

Avoid implying human activity.

92. AI Error State
I couldn't reach the AI advisor right now.

Your financial analytics are still available.

CTA:

[Try Again]
93. AI Unsupported Request

Example:

User:
Transfer ₹5,000 to my savings account.

Response should clearly explain:

WealthWise can analyze and simulate financial decisions, but it cannot execute financial transactions.

94. S-020 — Settings
Purpose

Manage account and application preferences.

95. Settings Sections
Profile
Preferences
Security
Data
Account
96. Settings — Profile

Fields:

Name
Email
97. Settings — Preferences

Initial preferences:

Currency
Timezone
Theme

Theme may support:

Light
Dark
System
98. Settings — Data

Potential MVP actions:

Export Transactions
Delete Account

The exact export implementation may be finalized later.

99. Settings — Account Deletion

The UI must clearly communicate that deletion is consequential.

Flow:

Delete Account
      ↓
Warning
      ↓
Confirmation
      ↓
Re-authentication if required
      ↓
Delete
100. Global Loading States

Every data-driven screen should define loading behavior.

Examples:

Dashboard:
Loading financial summary...

Transactions:
Loading transactions...

Analytics:
Calculating analytics...

Insights:
Analyzing your financial patterns...

AI:
Analyzing your question...

Skeleton loaders may be preferred for visual continuity.

101. Global Error States

Errors should be categorized.

Network Error

We couldn't connect to WealthWise.

Server Error

Something went wrong on our side.

Validation Error

Please check the highlighted fields.

Authentication Error

Your session has expired. Please log in again.

AI Error

The AI advisor is temporarily unavailable.

102. Global Empty States

Empty states should explain:

What is missing?
Why does it matter?
What can the user do next?
103. Global Confirmation Pattern

Destructive operations should require confirmation.

Examples:

Delete Transaction
Delete Budget
Delete Goal
Delete Account
104. Responsive Behavior

WealthWise should be designed for:

Desktop
Tablet
Mobile

The primary development target may be desktop web, but core functionality should remain accessible on smaller screens.

105. Screen-to-Feature Mapping
Screen	Main Features
Landing	Product Introduction
Login	Authentication
Registration	Authentication
Onboarding	User Setup
Dashboard	Financial Intelligence
Transactions	Transaction Management
Transaction Form	CRUD
Import	CSV Import
Import Review	Validation
Analytics	Financial Analytics
Budgets	Budget Management
Budget Details	Budget Intelligence
Goals	Goal Management
Goal Details	Goal Intelligence
Insights	Insight Engine
Insight Details	Explainability
Scenarios	Scenario Engine
Scenario Result	Scenario Intelligence
AI Advisor	Generative AI
Settings	Account Management
106. Cross-Screen Navigation

Important navigation paths:

Dashboard
   ↓
Insight
   ↓
Insight Details
   ↓
Scenario
   ↓
Scenario Result
   ↓
AI Advisor

and:

Dashboard
   ↓
Category
   ↓
Analytics
   ↓
Transactions

and:

Dashboard
   ↓
Goal
   ↓
Goal Details
   ↓
Scenario
107. Core CTA Hierarchy

The application should distinguish:

Primary

Actions that move the user toward their current objective.

Examples:

Add Transaction
Create Goal
Explore Scenario
Ask WealthWise
Secondary

Supporting actions.

Examples:

View Details
Edit
Filter
Compare
Destructive

Examples:

Delete
Remove
Reset
108. Screen State Matrix

Every major screen should support:

State	Required
Loading	Yes
Success	Yes
Empty	Yes
Error	Yes
Partial Data	Where applicable
Unauthorized	Yes
Offline / Network Failure	Where applicable
109. Intelligence Screen Pattern

Screens involving financial intelligence should generally follow:

SUMMARY
   ↓
CHANGE
   ↓
EXPLANATION
   ↓
EVIDENCE
   ↓
ACTION

Example:

Food spending ↑
      ↓
59% above average
      ↓
Budget exceeded
      ↓
Relevant transactions
      ↓
Explore What-If
110. AI Screen Pattern

The AI Advisor should generally follow:

QUESTION
   ↓
UNDERSTAND
   ↓
RETRIEVE
   ↓
CALCULATE
   ↓
EXPLAIN
   ↓
RECOMMEND

The AI should not be responsible for authoritative financial calculations.

111. Screen Design Boundary

This document defines:

What appears
Where it belongs conceptually
What the user can do
What state exists
How screens connect

It does not yet define:

Exact colors
Exact typography
Exact spacing
Final animations
Final component styling

Those belong in the UI/UX design specification.

112. Screen Completion Criteria

Before implementing a screen, we should know:

Screen Purpose
↓
Required Data
↓
User Actions
↓
API Dependencies
↓
Loading State
↓
Empty State
↓
Error State
↓
Navigation
113. Product Screen Hierarchy

The overall hierarchy is:

                    WEALTHWISE
                        │
              ┌─────────┴─────────┐
              │                   │
           PUBLIC            AUTHENTICATED
              │                   │
       ┌──────┴──────┐      ┌─────┴───────────┐
       │             │      │                 │
     Login       Register Dashboard       Application
                                             │
                           ┌─────────────────┼──────────────────┐
                           │        │        │       │          │
                      Transactions Analytics Budgets Goals   Insights
                           │                                  │
                           └──────────────┬───────────────────┘
                                          │
                                      Scenarios
                                          │
                                     AI Advisor
114. Flagship Screen Journey

The most important screen sequence is:

Dashboard
    ↓
Insight Details
    ↓
Scenario
    ↓
Scenario Result
    ↓
AI Advisor

This should receive special attention during implementation because it demonstrates the unique WealthWise experience.

115. Final Screen Principle

Every important screen should answer one question.

Dashboard
→ What's happening?

Analytics
→ Where is my money going?

Transactions
→ What exactly happened?

Budgets
→ Am I staying within limits?

Goals
→ Am I moving toward what I want?

Insights
→ What deserves my attention?

Scenarios
→ What could happen if I change something?

AI Advisor
→ Help me understand and decide.