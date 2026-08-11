# WealthWise — User Journey & Feature Map

**Document Version:** 1.0
**Status:** Product Discovery
**Related Documents:** Product Bible, Competitive Analysis, Problem & Value Proposition, Target Users & Personas

---

# 1. Purpose

This document defines how a user interacts with WealthWise from first entry through recurring financial analysis and decision-making.

It translates the product concept into:

* user journeys,
* major workflows,
* product areas,
* feature groups,
* navigation requirements,
* and the relationship between user actions and system intelligence.

The purpose is to ensure that WealthWise is designed around **user outcomes rather than isolated features**.

---

# 2. Core User Journey

The primary WealthWise journey is:

```text
DISCOVER
   ↓
SIGN UP
   ↓
ESTABLISH FINANCIAL CONTEXT
   ↓
IMPORT / ADD TRANSACTIONS
   ↓
UNDERSTAND FINANCIAL BASELINE
   ↓
SET GOALS
   ↓
BUILD PERSONAL MONEY MODEL
   ↓
RECEIVE PROACTIVE INSIGHTS
   ↓
EXPLORE IMPACT
   ↓
SIMULATE OPTIONS
   ↓
MAKE DECISION
   ↓
TRACK RESULT
   ↓
RE-ANALYZE FUTURE BEHAVIOUR
```

The user should gradually move from:

> **"I want to track my money."**

to:

> **"I understand my financial behaviour."**

and eventually:

> **"I use WealthWise to make better financial decisions."**

---

# 3. Experience Architecture

The overall experience can be divided into six stages.

| Stage            | User Objective                   | WealthWise Responsibility          |
| ---------------- | -------------------------------- | ---------------------------------- |
| 1. Entry         | Understand what the product does | Explain value clearly              |
| 2. Setup         | Establish financial context      | Collect minimum useful information |
| 3. Understanding | Know current financial state     | Analyze transactions               |
| 4. Awareness     | Understand behaviour             | Surface meaningful patterns        |
| 5. Decision      | Explore choices                  | Simulate consequences              |
| 6. Continuity    | Improve over time                | Track changes and re-analyze       |

---

# 4. Stage 1 — Entry

## Goal

The user should understand WealthWise before creating an account.

## First Impression

The product should communicate:

> **Understand your money. Improve your decisions.**

Supporting explanation:

> WealthWise analyzes your financial behaviour, connects it with your goals, and helps you understand what your financial choices could mean.

## Primary Actions

* Sign Up
* Log In
* Learn More

## Design Principle

Do not lead with a wall of financial charts.

Lead with the **outcome**.

The user should understand:

> "This product helps me understand and make decisions about my money."

---

# 5. Stage 2 — Account Creation

## Goal

Create a secure user account.

## Flow

```text
Sign Up
   ↓
Name / Email / Password
   ↓
Account Created
   ↓
Initial Setup
```

## Required Capabilities

* registration,
* login,
* password validation,
* authentication,
* session/token management,
* logout,
* protected routes.

## Security Principle

Financial information must remain isolated between users.

---

# 6. Stage 3 — Financial Context Setup

After authentication, WealthWise should establish enough context to make the first analysis meaningful.

The system should avoid asking for unnecessary information.

## Possible Context

### Basic Financial Context

* income range,
* income frequency,
* primary financial priority.

### Financial Goals

* goal type,
* target amount,
* target date.

### Data Source

The user can choose:

```text
Import transactions
        OR
Add transactions manually
        OR
Skip and explore setup
```

---

# 7. First-Time Setup Flow

```text
CREATE ACCOUNT
      ↓
WELCOME
      ↓
TELL WEALTHWISE ABOUT YOUR MONEY
      ↓
Income Context
      ↓
Financial Priority
      ↓
Import Transactions
      ↓
Transaction Processing
      ↓
Initial Analysis
      ↓
Optional Goal Setup
      ↓
FIRST WEALTHWISE MOMENT
      ↓
DASHBOARD
```

The setup should be progressive rather than forcing the user to complete a long questionnaire.

---

# 8. Stage 4 — Transaction Ingestion

Transactions form the foundation of the WealthWise intelligence system.

## Supported MVP Sources

### Source 1 — CSV Import

The user uploads a transaction file.

Possible fields:

```text
Date
Description
Amount
Type
Category
```

Category may be absent if WealthWise is expected to classify it.

### Source 2 — Manual Entry

The user manually adds:

* amount,
* date,
* description,
* type,
* category.

### Future Source

* bank integrations,
* financial account synchronization,
* additional statement formats.

---

# 9. Transaction Processing Pipeline

When a file is uploaded:

```text
CSV / Statement
      ↓
File Validation
      ↓
Parsing
      ↓
Data Normalization
      ↓
Duplicate Detection
      ↓
Transaction Classification
      ↓
Category Assignment
      ↓
Merchant Extraction
      ↓
Storage
      ↓
Analytics
```

The system should provide processing feedback.

Example:

> **312 transactions imported**

```text
287 successfully processed
19 automatically categorized
6 require review
```

---

# 10. Transaction Intelligence

The transaction layer should extract useful structure from raw financial records.

## Capabilities

### Classification

* income,
* expense,
* transfer,
* refund.

### Categorization

Examples:

* Food
* Groceries
* Transport
* Shopping
* Entertainment
* Bills
* Healthcare
* Education
* Travel
* Rent
* Salary
* Other

### Merchant Recognition

Examples:

```text
SWIGGY
→ Swiggy

AMAZON PAY
→ Amazon

UBER INDIA
→ Uber
```

### Recurring Transaction Detection

Identify transactions that appear regularly.

Examples:

* rent,
* subscriptions,
* EMI,
* recurring bills.

---

# 11. Stage 5 — Financial Baseline

After sufficient transaction data is available, WealthWise creates an initial financial baseline.

The baseline may include:

```text
Monthly Income
Monthly Expenses
Monthly Savings
Savings Rate
Fixed Expenses
Discretionary Expenses
Top Categories
Top Merchants
Recurring Commitments
```

The system should also calculate a historical baseline where sufficient data exists.

---

# 12. The First WealthWise Moment

The first meaningful analysis should not simply be:

> "Here is your dashboard."

Instead, WealthWise should identify one useful observation.

Example:

> ### Your first insight

> Your weekend spending is approximately 34% higher than your weekday average.

Then:

> ### Why it matters

> Dining and entertainment account for most of the difference.

Then:

> ### What you could do

> Reducing weekend discretionary spending by approximately ₹800 per month could increase your average monthly savings.

The user should immediately understand:

**This application does more than record transactions.**

---

# 13. Dashboard

The dashboard is not the product itself.

It is the user's **financial command center**.

## Primary Dashboard Areas

### Financial Snapshot

```text
Income
Expenses
Savings
Savings Rate
```

### Spending Overview

```text
Category distribution
Monthly trend
Top categories
```

### Financial Goals

```text
Goal progress
Required contribution
Projected completion
```

### Important Insights

```text
Recent behavioural changes
Warnings
Positive progress
```

### Quick Actions

```text
Add Transaction
Import Transactions
Create Goal
View Insights
Run Scenario
Ask Advisor
```

---

# 14. Dashboard Design Principle

The dashboard should follow:

> **Summary first → Explanation second → Exploration third.**

Do not expose every available metric at once.

A user should be able to answer within seconds:

1. How am I doing?
2. What changed?
3. What needs attention?
4. What can I do?

---

# 15. Stage 6 — Behaviour Intelligence

Once enough historical data exists, WealthWise analyzes behavioural patterns.

## Behaviour Signals

Examples:

### Spending Drift

A category gradually increases over time.

### Spending Spike

A category suddenly increases.

### Recurring Commitment

A repeated financial obligation is detected.

### Behaviour Change

A user's normal spending pattern changes.

### Category Concentration

A large proportion of discretionary spending is concentrated in one category.

### Savings Drift

Savings rate declines over multiple periods.

---

# 16. Financial Events

WealthWise should convert raw analytical findings into meaningful financial events.

Examples:

### Spending Drift

> Your dining spending has increased for three consecutive months.

### Savings Risk

> Your current savings rate is below the level required for your emergency-fund goal.

### Positive Behaviour

> Your discretionary spending has decreased by 18% compared with your previous three-month average.

### Goal Acceleration

> You're currently saving faster than required for your travel goal.

### Recurring Expense

> A monthly payment of approximately ₹999 has appeared consistently for the last five months.

---

# 17. Proactive Insight System

WealthWise should not require the user to manually inspect every chart.

The system should evaluate financial events and decide which ones deserve attention.

Conceptually:

```text
Financial Data
      ↓
Pattern Detection
      ↓
Significance Evaluation
      ↓
Goal Context
      ↓
Insight Generation
      ↓
User Presentation
```

Not every detected pattern should become a notification.

The system should prioritize:

* significance,
* relevance,
* confidence,
* user goals.

---

# 18. Insight Structure

Every major insight should attempt to contain four components.

## 1. What happened?

> Dining spending increased 31%.

## 2. Why?

> Weekend dining increased substantially.

## 3. Why does it matter?

> It is reducing the monthly savings amount required for your goal.

## 4. What can I do?

> Reducing dining by approximately ₹1,200–₹1,500 could restore your current trajectory.

This structure is central to the WealthWise UX.

---

# 19. Stage 7 — Financial Goals

Goals transform financial analysis from generic advice into personal context.

## Goal Creation

The user provides:

* goal name,
* target amount,
* target date,
* current amount, if applicable.

Example:

```text
Goal:
Emergency Fund

Target:
₹60,000

Current:
₹20,000

Deadline:
January 2027
```

---

# 20. Goal Intelligence

WealthWise calculates:

```text
Target Amount
      ↓
Current Progress
      ↓
Remaining Amount
      ↓
Time Remaining
      ↓
Required Contribution
      ↓
Current Saving Capacity
      ↓
Goal Feasibility
```

Example:

```text
Required monthly saving: ₹8,500
Current average saving: ₹7,200

Monthly gap: ₹1,300
```

The system can then explore possible ways of closing that gap.

---

# 21. Goal-Aware Insights

A financial event should be interpreted differently depending on the user's goals.

For example:

```text
Dining spending increased by ₹2,000
```

Without context:

> Moderate spending increase.

With a goal:

```text
Emergency Fund Goal
Monthly requirement: ₹8,500

Current projected saving: ₹7,400
```

The same behaviour becomes:

> Your recent spending increase may cause you to fall below the monthly contribution required for your emergency-fund goal.

This relationship between:

**Behaviour + Goal**

is central to WealthWise.

---

# 22. Stage 8 — Decision Simulation

This is where the user moves from understanding to exploration.

The user can ask:

> What if I reduce dining by 30%?

or:

> What if I save ₹2,000 more every month?

or:

> Can I afford this ₹15,000 purchase?

---

# 23. What-If Workflow

```text
User chooses scenario
        ↓
Current financial state
        ↓
Modify selected variable
        ↓
Recalculate financial model
        ↓
Compare against baseline
        ↓
Evaluate goal impact
        ↓
Display outcome
        ↓
AI explains difference
```

---

# 24. Scenario Example

### Current State

```text
Monthly income:       ₹50,000
Monthly expenses:     ₹36,000
Monthly savings:      ₹14,000
```

### Goal

```text
Target: ₹60,000
Required monthly saving: ₹15,000
```

### Scenario

> Reduce dining expenditure by ₹1,500.

### New Projection

```text
Monthly expenses:     ₹34,500
Monthly savings:      ₹15,500
```

### Result

> You could exceed the required monthly contribution by approximately ₹500.

The application performs the calculation.

The AI explains it.

---

# 25. Scenario Types

The MVP should support a limited set of scenarios.

### Spending Scenario

> What if I reduce category X by Y%?

### Savings Scenario

> What if I save ₹X more per month?

### Income Scenario

> What if monthly income changes by ₹X?

### Goal Timeline Scenario

> What if I extend the deadline?

### Purchase Scenario

> What if I spend ₹X on this purchase?

More advanced scenarios can be introduced later.

---

# 26. Scenario Comparison

Users should be able to compare alternatives.

Example:

| Scenario               | Monthly Saving | Goal Completion |
| ---------------------- | -------------: | --------------: |
| Current behaviour      |         ₹7,200 |        9 months |
| Reduce dining          |         ₹8,700 |        7 months |
| Reduce shopping        |         ₹8,400 |      7.5 months |
| Save additional ₹2,000 |         ₹9,200 |      6.5 months |

This turns financial advice into a decision-support experience.

---

# 27. Stage 9 — AI Advisor

The AI Advisor is not simply a chatbot.

It is the natural-language interface over WealthWise's structured financial context.

## The Advisor should answer questions such as:

> Where did most of my money go this month?

> Why did my savings decrease?

> What category increased the most?

> Am I on track for my emergency fund?

> What happens if I reduce food spending?

> Can I afford this purchase?

> How can I reach my goal faster?

---

# 28. AI Advisor Context

The Advisor should receive relevant structured context such as:

```text
Financial baseline
+
Behaviour patterns
+
Current goals
+
Recent insights
+
Scenario results
+
User-selected context
```

The AI should not be expected to independently calculate financial metrics from thousands of raw transactions.

---

# 29. AI Response Structure

Where appropriate, the Advisor should communicate:

### Observation

What happened?

### Explanation

Why does the system think it happened?

### Impact

What could it mean?

### Recommendation

What could the user consider doing?

### Uncertainty

What assumptions or limitations apply?

---

# 30. Stage 10 — User Action

WealthWise should allow the user to act on insights.

Possible actions:

```text
Adjust Budget
Create Goal
Modify Goal
Run Scenario
View Transactions
Dismiss Insight
Save Insight
Ask Advisor
```

The user remains responsible for the final decision.

---

# 31. Stage 11 — Continuous Learning Loop

WealthWise should not be treated as a static monthly report.

The system continuously updates its understanding as new transactions arrive.

```text
New Transactions
      ↓
Updated Analytics
      ↓
Updated Behaviour
      ↓
Updated Goals
      ↓
New Financial Events
      ↓
New Insights
      ↓
User Actions
      ↓
Future Financial Behaviour
      ↓
Re-analysis
```

The term "learning" here refers primarily to **updating the user's financial context and behavioural history**.

It does not necessarily imply that the system continuously retrains a machine-learning model.

---

# 32. Complete End-to-End Journey

The complete experience can therefore be represented as:

```text
                         WEALTHWISE
                             │
                             ↓
                         ONBOARDING
                             │
                             ↓
                    FINANCIAL CONTEXT
                             │
                             ↓
                    TRANSACTION IMPORT
                             │
                             ↓
                  TRANSACTION INTELLIGENCE
                             │
                             ↓
                    FINANCIAL BASELINE
                             │
                             ↓
                   PERSONAL MONEY MODEL
                             │
              ┌──────────────┼──────────────┐
              ↓              ↓              ↓
         DASHBOARD       GOALS          INSIGHTS
              │              │              │
              └──────────────┼──────────────┘
                             ↓
                    BEHAVIOUR ANALYSIS
                             │
                             ↓
                    FINANCIAL EVENTS
                             │
                             ↓
                  IMPACT / CONSEQUENCE
                             │
                             ↓
                    WHAT-IF SIMULATION
                             │
                             ↓
                       AI ADVISOR
                             │
                             ↓
                   RECOMMENDED OPTIONS
                             │
                             ↓
                      USER DECISION
                             │
                             ↓
                      FUTURE DATA
                             │
                             └────────────→ LOOP
```

---

# 33. Primary Navigation

The initial desktop/web application can use the following information architecture:

```text
WEALTHWISE
│
├── Dashboard
├── Transactions
├── Analytics
├── Goals
├── Scenarios
├── Insights
├── AI Advisor
└── Settings
```

---

# 34. Navigation Responsibilities

## Dashboard

> What is happening right now?

## Transactions

> What actually happened?

## Analytics

> What patterns exist?

## Goals

> What am I trying to achieve?

## Scenarios

> What happens if I change something?

## Insights

> What deserves my attention?

## AI Advisor

> Help me understand or explore my finances.

## Settings

> Manage my account and preferences.

---

# 35. Feature Map

## A. Authentication

* Registration
* Login
* Logout
* Session management
* Protected routes
* Profile

---

## B. Transaction Management

* CSV import
* Manual entry
* Transaction list
* Search
* Filters
* Categorization
* Merchant recognition
* Editing
* Deletion
* Duplicate handling

---

## C. Financial Analytics

* Income
* Expenses
* Savings
* Savings rate
* Category analysis
* Merchant analysis
* Monthly trends
* Fixed vs discretionary spending

---

## D. Behaviour Intelligence

* Spending trends
* Spending drift
* Spending spikes
* Recurring patterns
* Behaviour changes
* Anomalies
* Financial events

---

## E. Goals

* Create goal
* Edit goal
* Delete goal
* Track progress
* Required contribution
* Goal feasibility
* Goal projection

---

## F. Budgeting

* Create budget
* Category budgets
* Budget progress
* Budget comparison
* Budget alerts
* AI-assisted recommendations

---

## G. Decision Simulation

* Spending scenarios
* Savings scenarios
* Income scenarios
* Goal timeline scenarios
* Purchase scenarios
* Scenario comparison

---

## H. AI Advisor

* Financial summary
* Natural-language questions
* Insight explanations
* Goal explanations
* Scenario explanations
* Recommendations
* Context-aware financial conversation

---

## I. Insights

* Proactive insights
* Financial events
* Behaviour changes
* Goal risks
* Positive progress
* Saved insights
* Insight history

---

## J. User & Settings

* Profile
* Preferences
* Notification settings
* Data management
* Export data
* Security
* Logout

---

# 36. Feature Priority

Not every feature should have equal implementation priority.

## P0 — Essential MVP

```text
Authentication
Transaction Import
Manual Transactions
Transaction Categorization
Financial Analytics
Dashboard
Goals
Behaviour Insights
AI Insights
Basic Scenario Simulation
```

## P1 — Strong MVP Enhancement

```text
Budgeting
Recurring Expense Detection
Proactive Financial Events
AI Advisor
Scenario Comparison
Data Export
```

## P2 — Future

```text
Bank Integration
Automatic Synchronization
Advanced Forecasting
Advanced Anomaly Detection
Multi-account Aggregation
Mobile Application
Advanced Financial Health Score
Investment Awareness
```

---

# 37. The Core MVP Loop

The MVP should not attempt to implement the entire future vision.

It must successfully demonstrate one complete loop:

```text
IMPORT TRANSACTIONS
        ↓
UNDERSTAND FINANCIAL STATE
        ↓
DETECT BEHAVIOURAL PATTERN
        ↓
CONNECT WITH GOAL
        ↓
GENERATE INSIGHT
        ↓
SIMULATE AN ACTION
        ↓
SHOW PROJECTED OUTCOME
        ↓
AI EXPLAINS RESULT
```

If this loop works convincingly, the central WealthWise concept is demonstrated.

---

# 38. Product Experience Principle

WealthWise should progressively reveal complexity.

### Level 1 — Simple

> "Your spending is up 12%."

### Level 2 — Explanation

> "Dining and shopping caused most of the increase."

### Level 3 — Impact

> "Your savings rate is now below your goal requirement."

### Level 4 — Decision

> "Reducing dining by ₹1,200 could restore your target."

### Level 5 — Simulation

> "See what happens if you change other categories."

This prevents financial information overload while allowing advanced users to explore deeper.

Progressive disclosure is especially relevant in financial interfaces because dashboards can quickly become information-dense; recent UX work on personal-finance products similarly emphasizes clarity, quick access and avoiding unnecessary complexity.

---

# 39. Product Success From a UX Perspective

A user should be able to complete the following tasks without confusion:

### Task 1

Import financial transactions.

### Task 2

Understand their current financial position.

### Task 3

Find their largest spending category.

### Task 4

Understand a meaningful spending change.

### Task 5

Create a financial goal.

### Task 6

Determine whether their current behaviour supports the goal.

### Task 7

Run a basic what-if scenario.

### Task 8

Understand the scenario result.

### Task 9

Ask the AI Advisor a contextual financial question.

### Task 10

Take an action based on an insight.

These tasks will later become candidates for usability testing.

---

# 40. Final User Experience Statement

> **WealthWise should feel less like a spreadsheet that the user has to interpret and more like a financial intelligence system that continuously explains what matters.**

The user's journey should move naturally from:

**Data → Understanding → Awareness → Exploration → Decision → Action**

rather than:

**Data → Dashboard → User figures everything out.**

---

# 41. Status

This document defines the current user journey and feature architecture.

It will serve as an input for:

* Functional Requirements
* User Stories
* Use Cases
* Information Architecture
* UI/UX Design
* API Design
* Database Design
* Testing Strategy

The next stage is to convert these journeys and features into **formal functional requirements**.
