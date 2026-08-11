# WealthWise — Indexing & Query Strategy

**Document Version:** 1.0  
**Status:** Database Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Database:** MongoDB  
**ODM:** Mongoose  

---

# 1. Purpose

This document defines how WealthWise will query MongoDB and how database indexes will support those queries.

The purpose is to ensure that:

- common queries remain efficient,
- user data remains isolated,
- dashboard queries are predictable,
- transaction history can scale,
- analytics queries remain practical,
- AI context retrieval is controlled,
- indexes are created according to actual access patterns.

The database should be optimized around the way WealthWise actually uses financial data.

---

# 2. Query Design Philosophy

WealthWise follows four major principles.

## 2.1 User-Scoped Queries

Almost every financial query begins with:

```text
userId

herefore, user ownership is a fundamental query dimension.

2.2 Time Is a Major Query Dimension

Financial analysis is inherently temporal.

Common operations include:

this month
last month
this year
last 30 days
between two dates
before a goal deadline

Therefore, transaction date must be indexed appropriately.

2.3 Filter Before Aggregate

Analytics queries should generally follow:

Match
   ↓
Filter
   ↓
Group
   ↓
Calculate
   ↓
Return

rather than loading all transactions into application memory.

2.4 Indexes Must Serve Real Queries

Indexes should not be created merely because a field exists.

Every index should have a reason based on:

query frequency,
filtering,
sorting,
aggregation,
uniqueness,
or ownership.
3. Primary Query Dimensions

The major query dimensions of WealthWise are:

User
Date
Category
Type
Merchant
Status
Goal
Budget Period
Insight Status

The most important combination is:

userId + date
4. Transaction Query Patterns

Transactions are expected to be the largest and most frequently queried financial collection.

Typical queries include:

Get recent transactions
Get transactions for a date range
Get monthly transactions
Filter by category
Filter by transaction type
Search merchant
Calculate category spending
Calculate monthly spending
Calculate income
Calculate expenses
Detect recurring transactions
5. Recent Transactions Query

The dashboard may request the most recent transactions.

Conceptually:

{
  userId: userId
}

sorted by:

date DESC

Recommended index:

{
  userId: 1,
  date: -1
}

This is one of the highest-priority indexes in WealthWise.

6. Date Range Query

Example requirement:

Get all transactions for a user between 1 August and 31 August.

Conceptually:

{
  userId: userId,
  date: {
    $gte: startDate,
    $lte: endDate
  }
}

Supported by:

{
  userId: 1,
  date: -1
}
7. Category Query

Example:

How much did the user spend on Food?

Conceptually:

{
  userId: userId,
  category: "Food",
  type: "EXPENSE"
}

Potential index:

{
  userId: 1,
  category: 1,
  date: -1
}

This supports both category filtering and time-based analysis.

8. Transaction Type Query

Examples:

All income
All expenses
All transfers
All refunds

Potential query:

{
  userId: userId,
  type: "EXPENSE"
}

If this becomes a frequent query pattern, the database may use:

{
  userId: 1,
  type: 1,
  date: -1
}
9. Merchant Query

Example:

How much did the user spend at a particular merchant?

Conceptually:

{
  userId: userId,
  merchant: "Amazon"
}

Potential index:

{
  userId: 1,
  merchant: 1,
  date: -1
}

This should only be retained if merchant-level queries are sufficiently frequent.

10. Transaction Search

Free-text search over transaction descriptions is different from ordinary filtering.

Examples:

"Amazon"
"Uber"
"salary"
"electricity"

A normal B-tree index may not provide efficient general text search.

For the MVP, transaction search may initially use:

merchant
description

with controlled query patterns.

A dedicated search solution should only be introduced if ordinary MongoDB queries become insufficient.

11. Pagination Strategy

Transaction lists must not load the entire transaction history.

The MVP may use:

page
limit

or:

skip
limit

for simple pagination.

For large datasets, cursor-based pagination is preferred.

Example:

lastSeenTransactionDate
        ↓
next query
        ↓
older transactions

This avoids increasingly expensive large skip values.

12. Dashboard Query Strategy

The dashboard requires a compact financial summary rather than the complete transaction dataset.

Conceptually:

Dashboard
   │
   ├── Current Income
   ├── Current Expenses
   ├── Savings
   ├── Savings Rate
   ├── Category Distribution
   ├── Recent Transactions
   ├── Budget Status
   ├── Goal Progress
   └── Important Insights

These should be retrieved through application services rather than by directly exposing database collections.

13. Dashboard Data Flow
Dashboard Request
       ↓
Dashboard Service
       ↓
┌──────┼────────┬────────┐
↓      ↓        ↓        ↓
Txn   Goals   Budgets  Insights
│
↓
Financial Metrics
       ↓
Dashboard Response

The frontend should receive a purpose-built response.

It should not independently perform multiple database-style operations.

14. Monthly Financial Query

A common WealthWise operation is:

Calculate the user's financial state for a month.

Conceptually:

{
  userId: userId,
  date: {
    $gte: monthStart,
    $lt: nextMonthStart
  }
}

The query should then separate:

INCOME
EXPENSE
TRANSFER
REFUND

according to the financial rules.

15. Monthly Income

Conceptually:

Transactions
    ↓
Filter user
    ↓
Filter month
    ↓
Filter INCOME
    ↓
SUM amount

Result:

Monthly Income
16. Monthly Expense

Conceptually:

Transactions
    ↓
Filter user
    ↓
Filter month
    ↓
Filter EXPENSE
    ↓
SUM amount

Result:

Monthly Expenses
17. Monthly Savings

The application calculates:

Savings = Income - Expenses

The result is derived.

It should not be treated as an independent transaction.

18. Savings Rate

The application calculates:

Savings Rate =
Savings / Income × 100

Special handling is required when:

Income = 0

to avoid division by zero.

19. Category Spending Query

Category analysis follows:

Transactions
     ↓
userId
     ↓
date range
     ↓
EXPENSE
     ↓
group by category
     ↓
sum amount

Example output:

Food          ₹6,000
Transport     ₹3,200
Shopping      ₹2,500
Entertainment ₹1,800
20. Category Distribution

For a selected period:

Category Percentage =
Category Amount / Total Expenses × 100

This is derived from transaction data.

The percentage should not be stored as an authoritative transaction field.

21. Historical Comparison

WealthWise frequently compares:

Current Period
        vs
Previous Period

Examples:

This month vs last month
This month vs recent average
Current category spending vs historical baseline

The application should retrieve only the required periods.

It should not load the user's entire transaction history for every comparison.

22. Historical Baseline

A behavioural baseline may be calculated from:

Previous N periods

For example:

Current Food Spending
        ↓
Compare with
        ↓
Previous 3 months

The exact baseline window will be determined by the Behaviour Intelligence specification.

23. Spending Trend Query

A trend query may group transactions by:

month
category

Conceptually:

Transactions
      ↓
Date Range
      ↓
Expense Filter
      ↓
Group by Month
      ↓
Group by Category
      ↓
SUM amount

Result:

Month       Food     Travel    Shopping
January     ₹4,000   ₹2,000    ₹1,500
February    ₹4,500   ₹1,000    ₹2,000
March       ₹5,500   ₹3,000    ₹1,800
24. Recurring Expense Queries

Recurring transaction detection may require identifying repeated:

merchant
amount
category
time interval

Examples:

Netflix
Rent
Electricity
Internet
Subscriptions

This analysis should initially be performed through application logic and aggregation queries.

A dedicated recurring-expense collection should not be created unless necessary.

25. Unusual Transaction Queries

An unusual transaction is derived from transaction history.

Possible inputs include:

amount
category
merchant
historical frequency
historical average
recent behaviour

The query should retrieve the relevant historical context.

The resulting anomaly is a derived intelligence signal.

26. Goal Queries

Common Goal queries include:

Get all active goals
Get goal by ID
Get completed goals
Get upcoming goals
Get high-priority goals

Most queries begin with:

{
  userId: userId
}
27. Goal Index

A useful index is:

{
  userId: 1,
  status: 1
}

This supports:

Get active goals
Get completed goals
Get paused goals
28. Goal Deadline Queries

To identify goals approaching their deadline:

userId
+
targetDate

Potential index:

{
  userId: 1,
  targetDate: 1
}

This should be added when deadline-based functionality becomes active.

29. Budget Queries

Common Budget queries include:

Get active budgets
Get budgets for current month
Get budget for category
Get exceeded budgets
Get budget progress

Typical ownership filter:

{
  userId: userId
}
30. Budget Index

Potential index:

{
  userId: 1,
  startDate: 1,
  endDate: 1
}

For category-specific retrieval:

{
  userId: 1,
  category: 1,
  startDate: 1
}

The final index set should be determined by the actual API query patterns.

31. Insight Queries

Common Insight operations include:

Get recent insights
Get unread insights
Get high-severity insights
Get insights by category
Get active insights
Dismiss insight
Mark insight as read
32. Insight Index

Potential index:

{
  userId: 1,
  status: 1,
  createdAt: -1
}

This supports:

User
+
Insight Status
+
Newest First
33. Insight Expiration

Insights may contain:

expiresAt

The application can use this to determine whether an insight is still relevant.

Expired insights should not automatically be deleted unless the retention policy requires it.

34. Scenario Queries

Scenarios are primarily user-specific.

Common queries:

Create scenario
Get scenario history
Get recent scenarios
Get scenario by ID

Potential index:

{
  userId: 1,
  createdAt: -1
}
35. Financial Context Queries

Financial Context is generally queried by:

userId
period

Example:

{
  userId: userId,
  "period.start": {
    $gte: startDate
  }
}

A potential index:

{
  userId: 1,
  "period.start": -1
}
36. Financial Context as a Read Model

Financial Context may function as a read-optimized representation of financial state.

Conceptually:

Transactions
     ↓
Financial Calculations
     ↓
Financial Context
     ↓
Dashboard / AI

It can reduce repeated calculations when the same financial context is required by multiple features.

However, it remains derived data.

37. AI Context Queries

The AI Advisor should not query raw collections indiscriminately.

Instead, it should request a context appropriate to the user's question.

Example:

User:
"Why am I saving less this month?"

The Context Selector may request:

Current Income
Current Expenses
Previous Income
Previous Expenses
Major Category Changes
Relevant Goals

rather than every transaction.

38. AI Context Retrieval

The preferred pattern is:

User Question
      ↓
Intent Detection
      ↓
Context Requirements
      ↓
Application Services
      ↓
Structured Context
      ↓
Prompt Builder
      ↓
AI Provider

The AI provider never directly queries MongoDB.

39. Query Ownership

Database queries should belong to the module responsible for the data.

Example:

TransactionRepository
    → transaction queries

GoalRepository
    → goal queries

BudgetRepository
    → budget queries

InsightRepository
    → insight queries

Higher-level services may combine results.

40. Cross-Module Queries

A service may need information from multiple modules.

For example:

Goal Feasibility Service
        ↓
Goal
        +
Transactions
        +
Budget

This does not mean one repository should directly access another collection.

Instead:

Goal Service
     ↓
Goal Repository

Transaction Service
     ↓
Transaction Repository

Budget Service
     ↓
Budget Repository

The domain/service layer combines the results.

41. Query Authorization

Every user-owned query must include ownership constraints.

Correct:

{
  _id: goalId,
  userId: authenticatedUserId
}

Incorrect:

{
  _id: goalId
}

The second form could allow an authorization vulnerability if the ID belongs to another user.

42. Never Trust Client Ownership

The client may send:

{
  "userId": "..."
}

but the backend should derive ownership from the authenticated identity.

Conceptually:

JWT
 ↓
Authenticated User
 ↓
server-side userId
 ↓
database query
43. Query Result Projection

Queries should retrieve only the fields required for the operation when practical.

For example, a dashboard summary does not necessarily require:

notes
importMetadata
classification details

from every transaction.

Projection can reduce:

memory usage,
network transfer,
serialization cost.
44. Pagination Limits

The API should enforce sensible pagination limits.

For example:

default limit → controlled
maximum limit → controlled

The exact values will be finalized during API design.

The client must not be able to request an unlimited number of transactions.

45. Aggregation Pipeline Guidelines

Aggregation pipelines should generally follow:

$match
   ↓
$project
   ↓
$group
   ↓
$sort
   ↓
$limit

where appropriate.

Filtering early reduces the amount of data processed by later stages.

46. Aggregation and Financial Correctness

Aggregation pipelines that calculate financial metrics must have:

explicit transaction-type rules,
clear date boundaries,
consistent currency handling,
deterministic calculations,
automated tests.

Financial calculations must not depend on AI-generated output.

47. Date Boundary Rules

Date ranges must define whether the upper boundary is:

inclusive

or:

exclusive

The preferred implementation pattern is:

{
  date: {
    $gte: startDate,
    $lt: endDate
  }
}

This avoids ambiguity when periods meet.

48. Example Monthly Query

Instead of:

date <= monthEnd

prefer:

date >= monthStart
AND
date < nextMonthStart

Conceptually:

1 August 00:00
       ↓
31 August
       ↓
1 September 00:00

This produces a clean half-open interval.

49. Query Performance Monitoring

During development, slow queries should be investigated using MongoDB query analysis tools.

Potential indicators include:

execution time
documents examined
documents returned
index usage

Indexes should be adjusted based on measured query behaviour.

50. Over-Indexing Warning

Indexes improve reads but introduce costs.

Every additional index may increase:

write cost,
storage requirements,
memory usage,
maintenance overhead.

Therefore:

Do not create an index unless a real query pattern justifies it.

51. Initial Index Plan

The initial database should prioritize the following:

Users
{ email: 1 }

with uniqueness.

Transactions
{ userId: 1, date: -1 }
Goals
{ userId: 1, status: 1 }
Budgets
{ userId: 1, startDate: 1 }
Insights
{ userId: 1, status: 1, createdAt: -1 }
Scenarios
{ userId: 1, createdAt: -1 }
Financial Context
{ userId: 1, "period.start": -1 }
AI Conversations
{ userId: 1, updatedAt: -1 }
52. Indexes That May Be Added Later

Depending on actual usage:

{ userId: 1, category: 1, date: -1 }

{ userId: 1, type: 1, date: -1 }

{ userId: 1, merchant: 1, date: -1 }

{ userId: 1, targetDate: 1 }

{ userId: 1, category: 1, startDate: 1 }

These are not automatically required in the MVP.

53. Query Strategy for Analytics

Analytics should favor database-side aggregation for large datasets.

Example:

Transaction Collection
        ↓
User Filter
        ↓
Date Filter
        ↓
Expense Filter
        ↓
Category Grouping
        ↓
SUM
        ↓
Analytics Result

The application should avoid:

Fetch 100,000 transactions
        ↓
Send to Node.js
        ↓
Calculate everything in memory

when MongoDB can perform the aggregation efficiently.

54. Query Strategy for AI

AI context should use pre-aggregated or purpose-built financial information where possible.

Example:

Raw Transactions
       ↓
Financial Services
       ↓
Relevant Metrics
       ↓
Context Selector
       ↓
AI

This reduces:

token usage,
latency,
unnecessary data exposure,
prompt complexity.
55. Query Strategy for Behaviour Analysis

Behaviour analysis may require historical data.

The preferred pattern is:

Current Period
      +
Historical Baseline
      ↓
Behaviour Analysis

The baseline should be bounded.

Do not query an unlimited transaction history for every behavioural calculation.

56. Query Strategy for Goal Analysis

Goal analysis may combine:

Goal
+
Current Savings
+
Income
+
Expenses
+
Historical Savings

The services responsible for these entities should provide the required data.

The Goal module should not directly manipulate transaction persistence.

57. Query Strategy for Budget Analysis

Budget analysis combines:

Budget
+
Transactions in Budget Period

Conceptually:

Budget
   ↓
Determine Period
   ↓
Query Transactions
   ↓
Filter Category
   ↓
Calculate Spending
   ↓
Budget Status
58. Query Strategy for Insights

Insights should generally be queried as already-derived information.

The frontend should not reconstruct an insight from raw transactions.

Instead:

Insight Service
      ↓
Insight Repository
      ↓
Insight

The underlying signals remain available for traceability where needed.

59. Query Strategy for Dashboard

A dashboard request should ideally result in a controlled service response:

{
  "summary": {},
  "spending": {},
  "budgets": [],
  "goals": [],
  "recentTransactions": [],
  "insights": []
}

The exact API response contract will be defined in:

07-api/
60. Query Strategy for Transaction Import

Import processing should avoid blindly inserting every record.

Conceptually:

Imported Row
     ↓
Normalize
     ↓
Validate
     ↓
Generate Duplicate Signature
     ↓
Check Existing Records
     ↓
Insert / Flag Duplicate

The duplicate strategy will be finalized during implementation.

61. Query Strategy for AI Conversation History

Conversation history should normally be retrieved by:

userId
+
sessionId

or:

userId
+
updatedAt

depending on the use case.

Older conversations should not automatically be loaded into every AI request.

62. Query Isolation

Queries must never cross user boundaries.

Every financial query must conceptually follow:

userId
+
resource-specific filter

Example:

{
  userId: authenticatedUserId,
  category: "Food",
  date: {
    $gte: startDate,
    $lt: endDate
  }
}
63. Data Access Flow

The final database access architecture is:

HTTP Request
     ↓
Controller
     ↓
Service
     ↓
Repository
     ↓
Mongoose
     ↓
MongoDB

The reverse path is:

MongoDB
     ↓
Mongoose
     ↓
Repository
     ↓
Service
     ↓
Controller
     ↓
API Response
64. Database Query Responsibility
Layer	Responsibility
Controller	Receive request
Service	Apply business/domain logic
Repository	Execute persistence queries
Mongoose Model	Schema + database mapping
MongoDB	Store/query data
65. What Controllers Must Not Do

Controllers should not contain:

MongoDB queries
Aggregation pipelines
Financial calculations
AI prompt construction
Complex analytics

These responsibilities belong to lower architectural layers.

66. What Repositories Must Not Do

Repositories should not decide:

Whether a user can spend more
Whether a goal is feasible
Whether an insight is important
What an AI should recommend

Repositories retrieve and persist data.

Business decisions belong to services and domain logic.

67. Database Failure Handling

The application must handle:

Connection failure
Query timeout
Validation failure
Duplicate key error
Unavailable database

without exposing internal database details to users.

68. Transactional Operations

MongoDB transactions may be used when multiple writes must succeed or fail together.

They should not be introduced automatically for every operation.

Use transactions when there is a genuine atomicity requirement.

69. Caching

Caching should not be introduced prematurely.

For the MVP:

MongoDB
+
efficient queries
+
appropriate indexes

should be sufficient for expected usage.

Caching may be introduced later for:

dashboard summaries
financial context
frequently requested analytics

if measured performance justifies it.

70. Query Strategy Summary

WealthWise primarily follows:

User-scoped
      ↓
Time-bounded
      ↓
Indexed
      ↓
Aggregated where appropriate
      ↓
Purpose-built result
71. Initial Database Query Matrix
Feature	Main Collection	Main Filter	Typical Operation
Recent Transactions	Transactions	userId + date	Find
Monthly Expenses	Transactions	userId + date + type	Aggregate
Category Spending	Transactions	userId + category + date	Aggregate
Spending Trends	Transactions	userId + date	Aggregate
Goals	Goals	userId + status	Find
Budget Status	Budgets + Transactions	userId + period + category	Aggregate
Recent Insights	Insights	userId + status	Find
Scenario History	Scenarios	userId + createdAt	Find
Financial Context	FinancialContexts	userId + period	Find
AI History	AIConversations	userId + sessionId	Find
72. Performance Priorities

The highest database performance priorities are:

1. Transaction retrieval
2. Transaction aggregation
3. Dashboard loading
4. Historical comparisons
5. Goal/budget analysis
6. Insight retrieval
7. AI context retrieval

Transactions are expected to dominate database volume.

73. Scalability Considerations

The architecture should allow future growth in:

Users
Transactions per user
Historical periods
Insights
AI conversations

The initial design should therefore avoid assumptions that a user will only have a small number of transactions.

74. Final Index Philosophy

The final rule is:

Indexes exist to support known access patterns, not to decorate schemas.

The initial WealthWise index strategy prioritizes:

userId
+
date
+
status
+
category

with additional indexes introduced only when justified by actual queries.

75. Final Database Query Architecture
                         USER REQUEST
                              │
                              ▼
                         API CONTROLLER
                              │
                              ▼
                         DOMAIN SERVICE
                              │
               ┌──────────────┼──────────────┐
               ▼              ▼              ▼
          Transaction      Goal          Budget
          Repository     Repository     Repository
               │              │              │
               └──────────────┼──────────────┘
                              ▼
                         MongoDB
                              │
                              ▼
                       Structured Result
                              │
                              ▼
                         Service Layer
                              │
                              ▼
                    Financial Intelligence
                              │
                              ▼
                         AI Context
                              │
                              ▼
                          AI Advisor