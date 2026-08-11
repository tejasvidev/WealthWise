# WealthWise — Database Architecture

**Document Version:** 1.0  
**Status:** Architecture Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Database:** MongoDB  
**Architecture Style:** Modular Monolith + Document Database

---

# 1. Purpose

This document defines the database architecture of WealthWise.

It translates the system and component architecture into a concrete persistence model.

The document defines:

- collections,
- document structures,
- ownership relationships,
- embedded versus referenced data,
- indexes,
- data lifecycle,
- financial data integrity,
- analytical data,
- behavioural signals,
- goals,
- budgets,
- insights,
- scenarios,
- Personal Money Model,
- AI conversations,
- and database security boundaries.

The database must support the core WealthWise transformation:

```text
Transactions
     ↓
Financial Analytics
     ↓
Behaviour Signals
     ↓
Financial Context
     ↓
Goals / Budgets
     ↓
Insights / Scenarios
     ↓
AI Context

2. Database Philosophy

MongoDB is selected because WealthWise contains a combination of:

user-owned transactional data,
semi-structured imported data,
derived financial context,
behavioural signals,
generated insights,
scenario objects,
AI conversation data.

However, MongoDB flexibility shall not mean that the database becomes unstructured.

The application shall maintain explicit schemas, validation rules, ownership rules, and indexes.

3. Database Principles
3.1 User Ownership

Every user-owned financial document shall contain an explicit owner reference.

Conceptually:

userId

This is the primary security boundary.

3.2 Source Data vs Derived Data

WealthWise shall distinguish between:

Authoritative data

Examples:

transactions,
goals,
budgets.
Derived data

Examples:

analytics,
behavioural signals,
financial context,
insight metadata,
projections.
Generated content

Examples:

AI explanations,
AI recommendations,
conversation messages.

This distinction prevents generated or derived information from being mistaken for raw financial truth.

4. Collection Overview

The initial database will contain the following major collections:

users
transactions
goals
budgets
insights
scenarios
financialContexts
aiConversations

Optional/future collections may include:

imports
notifications
behaviourSignals
auditLogs

The MVP should avoid creating collections unless there is a clear persistence requirement.

5. High-Level Data Model
                         ┌──────────────┐
                         │    USERS     │
                         └──────┬───────┘
                                │
          ┌─────────────┬───────┼────────┬──────────────┐
          │             │       │        │              │
          ▼             ▼       ▼        ▼              ▼
    TRANSACTIONS      GOALS   BUDGETS  INSIGHTS     SCENARIOS
          │             │       │        │              │
          └─────────────┴───────┴────────┴──────────────┘
                                │
                                ▼
                      FINANCIAL CONTEXT
                                │
                                ▼
                       AI CONVERSATIONS
6. Users Collection

Collection:

users

The Users collection stores authentication and basic user profile information.

6.1 User Document

Conceptual structure:

{
  "_id": "ObjectId",
  "name": "Ricky",
  "email": "user@example.com",
  "passwordHash": "hashed-password",
  "currency": "INR",
  "timezone": "Asia/Kolkata",
  "preferences": {
    "notifications": true,
    "insights": true
  },
  "createdAt": "Date",
  "updatedAt": "Date"
}
6.2 User Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Unique user identifier
name	String	Yes	Display name
email	String	Yes	Unique login identifier
passwordHash	String	Yes	Secure password hash
currency	String	Yes	Primary currency
timezone	String	Yes	User timezone
preferences	Object	No	User preferences
createdAt	Date	Yes	Creation timestamp
updatedAt	Date	Yes	Last update timestamp
7. User Indexes

The following indexes should be created:

email → UNIQUE

Example:

{
  email: 1
}

with a unique constraint.

8. Transactions Collection

Collection:

transactions

Transactions are the primary financial source data for WealthWise.

A transaction represents a single financial event.

9. Transaction Document

Conceptual structure:

{
  "_id": "ObjectId",
  "userId": "ObjectId",

  "amount": 1250.50,
  "currency": "INR",

  "type": "expense",

  "date": "Date",

  "description": "Swiggy Order",

  "merchant": "Swiggy",

  "category": "Food",

  "subcategory": "Dining",

  "source": "import",

  "classification": {
    "method": "rule",
    "confidence": 0.98
  },

  "recurring": {
    "isRecurring": false,
    "patternId": null
  },

  "importMetadata": {
    "importId": "ObjectId",
    "sourceFile": "transactions.csv",
    "originalRow": 42
  },

  "createdAt": "Date",
  "updatedAt": "Date"
}
10. Transaction Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Transaction identifier
userId	ObjectId	Yes	Owning user
amount	Decimal128 / appropriate monetary type	Yes	Transaction amount
currency	String	Yes	Currency
type	Enum	Yes	income / expense / transfer / refund
date	Date	Yes	Transaction date
description	String	Yes	Original or normalized description
merchant	String	No	Identified merchant
category	String	No	Financial category
subcategory	String	No	Optional subcategory
source	Enum	Yes	manual / import
classification	Object	No	Classification metadata
recurring	Object	No	Recurring transaction metadata
importMetadata	Object	No	Import traceability
createdAt	Date	Yes	Creation timestamp
updatedAt	Date	Yes	Last update
11. Transaction Type

Supported transaction types:

income
expense
transfer
refund

The application shall define how each type participates in financial calculations.

For example, transfers between the user's own accounts should not automatically be treated as income or expense.

12. Transaction Source

Supported sources:

manual
import

Future sources may include:

bank
UPI
card
API

These are outside the initial MVP.

13. Monetary Data

Financial amounts require special handling.

The authoritative amount representation should avoid floating-point precision problems.

MongoDB Decimal128 is preferred for monetary values where supported by the application stack.

Example:

1250.50

shall not be represented internally in a way that introduces unacceptable monetary precision errors.

14. Transaction Indexes

Recommended indexes:

{ userId: 1, date: -1 }

{ userId: 1, category: 1, date: -1 }

{ userId: 1, merchant: 1, date: -1 }

{ userId: 1, type: 1, date: -1 }

These support common operations such as:

transaction history,
category filtering,
merchant analysis,
income/expense filtering.
15. Transaction Ownership

Every transaction must belong to exactly one user.

Conceptually:

Transaction.userId
        ↓
User._id

Every transaction query must apply the authenticated user's ownership scope.

16. Transaction Immutability Considerations

Some transaction fields may be edited by the user.

However, imported source metadata should be treated as historical metadata and should not be casually overwritten.

For example:

Original imported description
Original source file
Original row
Import identifier

should remain available where traceability is required.

17. Goals Collection

Collection:

goals

A goal represents a user's financial objective.

18. Goal Document

Conceptual structure:

{
  "_id": "ObjectId",
  "userId": "ObjectId",

  "name": "Goa Trip",
  "targetAmount": 50000,
  "currentAmount": 18000,

  "targetDate": "Date",

  "status": "on_track",

  "createdAt": "Date",
  "updatedAt": "Date"
}
19. Goal Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Goal identifier
userId	ObjectId	Yes	Owning user
name	String	Yes	Goal name
targetAmount	Decimal128	Yes	Target amount
currentAmount	Decimal128	Yes	Current saved amount
targetDate	Date	Yes	Target completion date
status	Enum	Derived	Current goal status
createdAt	Date	Yes	Creation timestamp
updatedAt	Date	Yes	Last update
20. Goal Status

Possible statuses:

ahead
on_track
at_risk
behind
completed
insufficient_data

Status should be derived from documented financial rules rather than arbitrary user-interface logic.

21. Goal Calculations

The following should generally be calculated by the application rather than permanently stored as authoritative values:

remainingAmount
progressPercentage
requiredMonthlyContribution
goalFeasibility

Example:

remainingAmount =
targetAmount - currentAmount

This avoids stale calculated values.

22. Goal Indexes

Recommended:

{ userId: 1, targetDate: 1 }

{ userId: 1, status: 1 }
23. Budgets Collection

Collection:

budgets

A budget defines a spending limit for a category and period.

24. Budget Document

Conceptual structure:

{
  "_id": "ObjectId",
  "userId": "ObjectId",

  "category": "Food",

  "amount": 8000,

  "period": {
    "type": "monthly",
    "startDate": "Date",
    "endDate": "Date"
  },

  "createdAt": "Date",
  "updatedAt": "Date"
}
25. Budget Fields
Field	Type	Required
_id	ObjectId	Yes
userId	ObjectId	Yes
category	String	Yes
amount	Decimal128	Yes
period	Object	Yes
createdAt	Date	Yes
updatedAt	Date	Yes
26. Budget Calculations

The following should generally be derived:

actualSpending
remainingBudget
usagePercentage
budgetStatus
projectedSpending

Example:

remainingBudget =
budgetAmount - actualSpending
27. Budget Indexes

Recommended:

{ userId: 1, category: 1 }

{ userId: 1, "period.startDate": 1 }
28. Insights Collection

Collection:

insights

Insights represent meaningful financial events that WealthWise has chosen to communicate to the user.

29. Insight Document

Conceptual structure:

{
  "_id": "ObjectId",
  "userId": "ObjectId",

  "type": "spending_increase",

  "severity": "medium",

  "title": "Food spending increased",

  "summary": "Your food spending increased compared with your recent average.",

  "context": {
    "category": "Food",
    "currentValue": 6200,
    "baselineValue": 3900,
    "percentageChange": 58.97
  },

  "impact": {
    "goalId": "ObjectId"
  },

  "recommendation": {
    "type": "scenario",
    "action": "reduce_category"
  },

  "ai": {
    "generated": true,
    "model": "model-name",
    "generatedAt": "Date"
  },

  "status": "active",

  "createdAt": "Date",
  "updatedAt": "Date"
}
30. Insight Philosophy

An insight should not simply be a text string.

It should retain enough structured context to answer:

What happened?
Why was it detected?
What data supports it?
Why does it matter?
What action can the user take?
31. Insight Fields
Field	Purpose
type	Event type
severity	Importance
title	Short description
summary	User-facing explanation
context	Supporting financial information
impact	Goal/budget impact
recommendation	Suggested next action
ai	AI generation metadata
status	active / dismissed / archived
32. Insight Indexes

Recommended:

{ userId: 1, createdAt: -1 }

{ userId: 1, status: 1, createdAt: -1 }

{ userId: 1, severity: 1, createdAt: -1 }
33. Insight Data Integrity

AI-generated insight text should never be the only stored representation of an insight.

The structured financial context should be preserved separately.

This allows WealthWise to distinguish:

FACT

from:

AI EXPLANATION
34. Scenarios Collection

Collection:

scenarios

A scenario represents a hypothetical financial simulation.

35. Scenario Document

Conceptual structure:

{
  "_id": "ObjectId",
  "userId": "ObjectId",

  "name": "Reduce Food Spending",

  "type": "category_reduction",

  "inputs": {
    "category": "Food",
    "changePercentage": -20
  },

  "baseline": {
    "monthlyIncome": 60000,
    "monthlyExpenses": 42000,
    "monthlySavings": 18000
  },

  "result": {
    "projectedExpenses": 40760,
    "projectedSavings": 19240,
    "savingsDifference": 1240
  },

  "goalImpact": {
    "goalId": "ObjectId",
    "estimatedImpact": 1240
  },

  "createdAt": "Date"
}
36. Scenario Principle

Scenario documents represent hypothetical states.

They must not be treated as actual financial records.

Scenario
    ≠
Transaction
37. Scenario Persistence

Scenario persistence is optional.

The MVP may:

calculate scenarios dynamically, or
allow users to save scenarios.

If scenarios are persisted, sufficient input data should be retained to reproduce the result.

38. Scenario Indexes

Recommended:

{ userId: 1, createdAt: -1 }

{ userId: 1, type: 1 }
39. Financial Context Collection

Collection:

financialContexts

This collection stores the structured Personal Money Model.

It is a derived representation of the user's financial state.

40. Financial Context Document

Conceptual structure:

{
  "_id": "ObjectId",
  "userId": "ObjectId",

  "income": {
    "monthlyAverage": 60000,
    "trend": "stable"
  },

  "expenses": {
    "monthlyAverage": 42000,
    "trend": "increasing"
  },

  "savings": {
    "monthlyAverage": 18000,
    "rate": 30
  },

  "topCategories": [
    {
      "category": "Housing",
      "average": 15000
    },
    {
      "category": "Food",
      "average": 6200
    }
  ],

  "behaviourSignals": [],

  "recurringCommitments": [],

  "goalSummary": [],

  "budgetSummary": [],

  "updatedAt": "Date"
}
41. Financial Context Philosophy

The Financial Context is a derived cache/context layer, not the original source of financial truth.

The authoritative source remains:

Transactions
Goals
Budgets

The Financial Context can therefore be rebuilt.

42. Financial Context Refresh

Conceptually:

Transaction Change
       ↓
Analytics Refresh
       ↓
Behaviour Analysis
       ↓
Goal / Budget Analysis
       ↓
Context Builder
       ↓
financialContexts
43. Financial Context Versioning

Where useful, the context may contain:

version
lastCalculatedAt
dataRange

Example:

{
  "version": 3,
  "lastCalculatedAt": "Date"
}

This helps determine whether the context is stale.

44. Financial Context Index

Recommended:

{ userId: 1 }

with a unique constraint if only one active context document exists per user.

45. AI Conversations Collection

Collection:

aiConversations

This collection stores conversational interactions when persistence is enabled.

46. AI Conversation Document

Conceptual structure:

{
  "_id": "ObjectId",
  "userId": "ObjectId",

  "sessionId": "String",

  "messages": [
    {
      "role": "user",
      "content": "Why did my savings fall?",
      "createdAt": "Date"
    },
    {
      "role": "assistant",
      "content": "Your savings fell mainly because...",
      "createdAt": "Date"
    }
  ],

  "metadata": {
    "model": "model-name",
    "provider": "provider-name"
  },

  "createdAt": "Date",
  "updatedAt": "Date"
}
47. AI Conversation Privacy

Conversation storage should be carefully considered because messages may contain financial information.

The system should:

minimize unnecessary retention,
protect conversations using user ownership,
avoid logging conversation content unnecessarily,
document AI-provider data handling.
48. AI Conversation Indexes

Recommended:

{ userId: 1, updatedAt: -1 }

{ userId: 1, sessionId: 1 }
49. Optional Import Collection

A dedicated:

imports

collection may be introduced if import history becomes important.

Possible structure:

{
  "_id": "ObjectId",
  "userId": "ObjectId",
  "fileName": "transactions.csv",
  "recordCount": 1000,
  "successCount": 950,
  "duplicateCount": 40,
  "errorCount": 10,
  "status": "completed",
  "createdAt": "Date"
}

This is recommended if import traceability is required beyond transaction-level metadata.

50. Optional Behaviour Signals Collection

Initially, behavioural signals may be stored inside insights or financial context.

A dedicated collection may later be introduced:

behaviourSignals

when:

signal history becomes important,
behaviour analysis becomes computationally expensive,
signals need independent lifecycle management.
51. Embedded vs Referenced Data

MongoDB allows both embedded and referenced documents.

WealthWise should use each appropriately.

Embed when:
data belongs tightly to its parent,
it is usually retrieved with the parent,
it has a bounded size,
it does not require independent querying.

Examples:

User preferences
Transaction classification
Scenario inputs
Insight context
Reference when:
data has its own lifecycle,
data can grow significantly,
it is queried independently,
it belongs to another major domain entity.

Examples:

Transaction → User
Goal → User
Budget → User
Insight → User
Scenario → User
52. Relationship Model

Conceptually:

User
 │
 ├──< Transactions
 │
 ├──< Goals
 │
 ├──< Budgets
 │
 ├──< Insights
 │
 ├──< Scenarios
 │
 ├─── FinancialContext
 │
 └──< AIConversations

Where:

1 → many

is represented using userId.

53. Why Not Embed Transactions Inside User?

Transactions should not be embedded inside the User document.

Incorrect:

User
 └── transactions[]

Reasons:

transaction history can grow very large,
transaction queries are frequent,
updates are independent,
MongoDB document size is limited,
analytics queries benefit from separate indexing.

Correct:

User
  ↑
userId
  │
Transactions collection
54. Why Not Store All Analytics Permanently?

Many analytics can be derived directly from transactions.

For example:

monthlyExpenses
categorySpending
savingsRate

should generally be calculated rather than treated as independent authoritative records.

Otherwise:

Transactions changed
        ↓
Analytics stale

The system should either recalculate or explicitly invalidate derived data.

55. Derived Data Strategy

WealthWise follows:

Authoritative Data
       ↓
Deterministic Calculation
       ↓
Derived Data
       ↓
Cache / Context

If derived data becomes inconsistent:

Derived Data
      ↓
Discard / Rebuild
      ↓
Authoritative Data
56. Data Freshness

Derived financial context should have a freshness indicator.

Example:

{
  "updatedAt": "Date",
  "dataRange": {
    "from": "Date",
    "to": "Date"
  }
}

The application should be able to determine whether the context is stale.

57. Database-Level Security

MongoDB access should occur only through the backend.

React
  ↓
Express
  ↓
Application Services
  ↓
Repositories
  ↓
MongoDB

The frontend shall never directly connect to the production database.

58. Query Ownership Rule

Every user-owned query should conceptually follow:

{
  userId: authenticatedUserId
}

Example:

transactions.find({
  userId: req.user.id
});

Not:

transactions.find({});

unless the operation is explicitly administrative and separately authorized.

59. Compound Ownership Indexes

Most financial queries should begin with userId.

Therefore indexes should frequently follow:

{ userId: 1, ... }

This improves both:

query performance,
ownership-aware access patterns.
60. Database Validation

Although application-level validation is mandatory, important document constraints should also be enforced through database schema validation where practical.

Examples:

amount > 0
required userId
valid transaction type
valid date
valid goal target

The database should provide a second layer of protection against malformed records.

61. Referential Integrity

MongoDB does not enforce relational foreign keys in the same manner as relational databases.

Therefore application services shall manage relationships explicitly.

Example:

Delete User
   ↓
Determine dependent records
   ↓
Transactions
Goals
Budgets
Insights
Scenarios
Context
Conversations
   ↓
Delete / Retain according to policy

The exact account-deletion strategy shall be documented before implementation.

62. Cascading Deletion

If user account deletion is supported, the system should ensure that associated financial data is removed or appropriately anonymized according to the documented retention policy.

The deletion operation should not leave orphaned user financial records unintentionally.

63. Timestamps

Major documents should contain:

createdAt
updatedAt

where relevant.

Dates should be stored using MongoDB Date types rather than formatted strings for authoritative date values.

64. Timezone Strategy

Financial periods depend on user-local dates.

The system shall store:

user.timezone

and use a consistent timezone strategy when calculating:

daily spending,
monthly spending,
goal periods,
budget periods,
recurring transactions.

The database should store actual timestamps consistently while application logic determines user-local periods.

65. Currency Strategy

The MVP may initially support:

INR

if the project's primary use case is Indian users.

However, transaction and financial schemas should retain a currency field so that future multi-currency support does not require a complete data-model redesign.

66. Data Lifecycle

The expected lifecycle of financial data is:

Created
   ↓
Validated
   ↓
Stored
   ↓
Analyzed
   ↓
Enriched
   ↓
Referenced by Insights / Goals / Scenarios
   ↓
Updated or Deleted

Derived data follows:

Source Data
   ↓
Generated
   ↓
Used
   ↓
Invalidated
   ↓
Rebuilt
67. Transaction Import Lifecycle
Upload
  ↓
Validate
  ↓
Parse
  ↓
Normalize
  ↓
Classify
  ↓
Deduplicate
  ↓
Persist
  ↓
Analyze
  ↓
Refresh Context
68. Data Retention

The final data-retention policy shall specify:

how long transactions are stored,
how long AI conversations are stored,
how long insight history is stored,
how deleted data is handled,
how backups are retained.

The MVP should not silently retain data indefinitely without documenting the policy.

69. Backup Strategy

Production deployment should implement:

Database
   ↓
Scheduled Backup
   ↓
Protected Backup Storage

Backup frequency and retention will depend on the selected deployment infrastructure.

70. Recovery Strategy

The system should be capable of restoring authoritative financial data from backups.

The primary recovery source should be:

Transactions
Goals
Budgets

Derived data such as:

FinancialContext
Behaviour Signals
Some analytics

should be rebuildable where practical.

71. Data Rebuild Strategy

A major advantage of separating authoritative and derived data is:

Authoritative Data
      ↓
Recalculate
      ↓
Analytics
      ↓
Behaviour
      ↓
Financial Context
      ↓
Insights

This allows the system to recover from stale derived data.

72. Database Performance Strategy

Performance should be supported through:

appropriate indexes,
bounded queries,
pagination,
aggregation pipelines,
selective field retrieval,
avoiding unnecessary document loading.
73. Transaction Pagination

Transaction history shall not load every transaction into the frontend simultaneously.

The API should support pagination.

Example:

GET /api/v1/transactions?page=1&limit=50

The exact pagination strategy will be finalized in API Architecture.

74. Analytics Query Strategy

Analytics should prefer database aggregation or optimized application-level processing depending on the operation.

Example:

Transactions
      ↓
MongoDB Aggregation
      ↓
Category Totals
      ↓
Analytics Service

Complex calculations may combine database aggregation with application-level business rules.

75. Database and AI Boundary

The AI Service shall not receive unrestricted database access.

Incorrect:

AI
 ↓
MongoDB

Correct:

AI Advisor
    ↓
Context Selector
    ↓
Application Services
    ↓
Validated Data
    ↓
Prompt Builder
    ↓
AI Provider
76. Database and Scenario Boundary

The Scenario Engine should read authoritative financial data but operate on an isolated in-memory or temporary simulation state.

MongoDB
   ↓
Current State
   ↓
Scenario Engine
   ↓
Temporary State
   ↓
Result

Actual records remain unchanged.

77. Database and Insight Boundary

The Insight Engine should use structured financial information.

Transactions
     ↓
Analytics
     ↓
Behaviour
     ↓
Insight Context
     ↓
AI

The generated insight should not overwrite the underlying transaction or financial metrics.

78. Data Integrity Rules

The following rules are mandatory:

Rule 1

Every financial record must belong to a user.

Rule 2

Financial amounts must use appropriate monetary precision.

Rule 3

Transactions are the primary source for transaction-derived analytics.

Rule 4

Goals are authoritative user-defined objectives.

Rule 5

Budgets are authoritative user-defined spending limits.

Rule 6

Derived financial context can be rebuilt.

Rule 7

AI-generated text is not authoritative financial data.

Rule 8

Scenario data is hypothetical.

79. Database Anti-Patterns to Avoid

The following patterns should be avoided.

79.1 Giant User Document

Do not store all financial history inside the User document.

79.2 AI as Database

Do not use AI conversation history as the financial source of truth.

79.3 Duplicate Financial Truth

Do not maintain the same authoritative financial number independently in many collections without a clear reason.

79.4 Unscoped Queries

Never retrieve user financial data without ownership constraints.

79.5 Stale Analytics as Truth

Do not assume stored analytics are always correct after transactions change.

79.6 Mixed Scenario and Actual Data

Never store hypothetical scenario transactions as actual transactions.

80. Initial Collection Summary
Collection	Type	Primary Purpose
users	Authoritative	User identity
transactions	Authoritative	Financial events
goals	Authoritative	Financial objectives
budgets	Authoritative	Spending limits
insights	Derived + Generated	Financial insights
scenarios	Hypothetical	Financial simulations
financialContexts	Derived	Personal Money Model
aiConversations	Generated	AI interaction history
81. Recommended MVP Collections

The MVP should begin with:

users
transactions
goals
budgets
insights
scenarios
financialContexts

aiConversations may be added when persistent conversational history is implemented.

82. Future Collections

Potential future additions:

imports
behaviourSignals
notifications
auditLogs
accounts
recurringTransactions

These should only be introduced when their independent lifecycle or query requirements justify separate persistence.