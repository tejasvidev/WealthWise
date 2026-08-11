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

For intelligence-related operations:

React Frontend
      ↓
REST API
      ↓
Backend Services
      ↓
Financial Intelligence
      ↓
AI Service
      ↓
AI Provider

The frontend must never communicate directly with MongoDB or the external AI provider.

3. API Design Principles

The WealthWise API follows these principles:

Resource-oriented
Stateless
Versioned
Authenticated
User-scoped
Validated
Predictable
Consistent

The API should expose business capabilities rather than database implementation details.

4. API Base URL

The production API will conceptually use:

/api/v1

Therefore:

/api/v1/...

is the root of the versioned API.

The actual domain will depend on deployment.

5. API Versioning

The initial API version is:

v1

Example:

/api/v1/transactions
/api/v1/goals
/api/v1/budgets

Versioning allows future API contracts to evolve without immediately breaking existing clients.

6. Versioning Strategy

The initial strategy is URL-based versioning:

/api/v1

Future breaking changes may introduce:

/api/v2

A new API version should only be created when compatibility cannot reasonably be maintained within the existing version.

7. Resource Model

Major API resources correspond to WealthWise product domains.

Authentication
Users
Transactions
Analytics
Budgets
Goals
Insights
Scenarios
Advisor

The API should not necessarily expose every database collection as a direct REST resource.

For example:

financialContexts

may remain an internal derived-data concept rather than becoming a public CRUD resource.

8. Endpoint Organization

The initial API structure is:

/api/v1
│
├── /auth
├── /users
├── /transactions
├── /analytics
├── /budgets
├── /goals
├── /insights
├── /scenarios
└── /advisor
9. Authentication Endpoints

Authentication is responsible for:

Registration
Login
Logout
Session verification
Password-related operations

Initial conceptual endpoints:

POST   /api/v1/auth/register
POST   /api/v1/auth/login
POST   /api/v1/auth/logout
GET    /api/v1/auth/me

Additional password recovery endpoints may be added later.

10. User Endpoints

User endpoints manage the authenticated user's profile and preferences.

Conceptual endpoints:

GET    /api/v1/users/me
PATCH  /api/v1/users/me
PATCH  /api/v1/users/me/preferences

The API should not expose arbitrary user IDs for ordinary self-service operations.

11. Transaction Endpoints

Transactions are one of the core API resources.

Initial endpoints:

GET    /api/v1/transactions
POST   /api/v1/transactions
GET    /api/v1/transactions/:id
PATCH  /api/v1/transactions/:id
DELETE /api/v1/transactions/:id

Import functionality:

POST   /api/v1/transactions/import
12. Transaction Retrieval

The transaction listing endpoint should support:

Pagination
Date filtering
Category filtering
Type filtering
Merchant filtering
Search
Sorting

Example:

GET /api/v1/transactions

Possible query parameters:

?page=1
&limit=20
&category=Food
&type=EXPENSE
&startDate=2026-08-01
&endDate=2026-08-31
&sort=date
&order=desc

The exact query parameter contract will be defined in the transaction API specification.

13. Transaction Creation

A transaction creation request may conceptually contain:

{
  "date": "2026-08-10",
  "description": "Dinner",
  "merchant": "Restaurant",
  "amount": 850,
  "type": "EXPENSE",
  "category": "Food"
}

The backend must validate:

Amount
Date
Type
Category
Required fields

The backend derives ownership from authentication.

The client must not control the authoritative userId.

14. Transaction Update

A transaction update may modify fields such as:

description
merchant
category
notes

Certain financial fields may require stricter handling.

For example:

amount
date
type

must be updated through validated backend logic because they can affect analytics, budgets, goals, and insights.

15. Transaction Deletion

Conceptually:

DELETE /api/v1/transactions/:id

Deletion behaviour will follow the database retention and business rules.

The API should not silently delete dependent intelligence without a defined policy.

16. Transaction Import

CSV import is a dedicated workflow rather than simply a large collection of individual POST requests.

Conceptually:

POST /api/v1/transactions/import

Flow:

CSV File
   ↓
Upload
   ↓
Parse
   ↓
Validate
   ↓
Normalize
   ↓
Duplicate Detection
   ↓
Transaction Persistence
   ↓
Financial Refresh
17. Import Response

The import API should eventually provide information such as:

Total Rows
Valid Rows
Imported Rows
Duplicate Rows
Rejected Rows
Validation Errors

Example conceptual response:

{
  "total": 100,
  "imported": 87,
  "duplicates": 8,
  "rejected": 5
}
18. Analytics Endpoints

Analytics are derived financial information.

They should not be exposed as arbitrary database queries.

Initial conceptual endpoints:

GET /api/v1/analytics/summary
GET /api/v1/analytics/spending
GET /api/v1/analytics/income
GET /api/v1/analytics/savings
GET /api/v1/analytics/categories
GET /api/v1/analytics/trends
19. Analytics Query Scope

Analytics endpoints should accept a controlled analysis period.

Example:

GET /api/v1/analytics/summary
    ?startDate=2026-08-01
    &endDate=2026-08-31

The backend determines which transactions belong to that period.

20. Analytics Response Principle

Analytics responses should return structured financial information.

Example:

{
  "income": 50000,
  "expenses": 32000,
  "savings": 18000,
  "savingsRate": 36
}

The frontend should not need to reconstruct these values from raw transactions.

21. Category Analytics

A category analytics response may contain:

{
  "categories": [
    {
      "category": "Food",
      "amount": 6000,
      "percentage": 18.75
    }
  ]
}

The backend remains responsible for calculation.

22. Trend Analytics

Trend endpoints may return:

Period
Category
Amount
Change

Example:

{
  "period": "2026-08",
  "amount": 6000,
  "changeFromPrevious": 12.5
}

The exact structure will be defined in the analytics endpoint specification.

23. Budget Endpoints

Initial budget endpoints:

GET    /api/v1/budgets
POST   /api/v1/budgets
GET    /api/v1/budgets/:id
PATCH  /api/v1/budgets/:id
DELETE /api/v1/budgets/:id
24. Budget Progress

Budget progress is derived from:

Budget
+
Transactions

The API may expose:

GET /api/v1/budgets/:id/progress

or include progress in the normal budget response.

The final choice will depend on the API contract.

25. Goal Endpoints

Initial endpoints:

GET    /api/v1/goals
POST   /api/v1/goals
GET    /api/v1/goals/:id
PATCH  /api/v1/goals/:id
DELETE /api/v1/goals/:id
26. Goal Progress

Goal progress is derived from the Goal state and relevant financial information.

Possible endpoint:

GET /api/v1/goals/:id/progress

The response may include:

Target Amount
Current Amount
Remaining Amount
Progress Percentage
Target Date
Required Contribution
Feasibility
27. Insight Endpoints

Insights are intelligence outputs.

Initial endpoints:

GET   /api/v1/insights
GET   /api/v1/insights/:id
PATCH /api/v1/insights/:id

The PATCH operation may support state changes such as:

READ
DISMISSED
ACTIONED
28. Insight Filtering

The API may support:

status
severity
type
category
date range

Example:

GET /api/v1/insights
    ?status=NEW
    &severity=HIGH
29. Scenario Endpoints

Scenarios are simulations rather than ordinary CRUD resources.

Initial endpoints:

POST /api/v1/scenarios
GET  /api/v1/scenarios
GET  /api/v1/scenarios/:id

A scenario is created by submitting assumptions.

30. Scenario Request

Example:

{
  "type": "REDUCE_CATEGORY_SPENDING",
  "parameters": {
    "category": "Food",
    "reductionPercentage": 20
  }
}

The backend:

Validates
   ↓
Loads financial context
   ↓
Runs Scenario Engine
   ↓
Calculates result
   ↓
Persists scenario
   ↓
Returns result
31. Scenario Integrity

Scenario requests must never mutate actual financial transactions.

The API treats scenarios as:

Simulation

not:

Financial Action
32. Advisor Endpoints

The AI Advisor is exposed through a controlled backend API.

Initial endpoints:

POST /api/v1/advisor/chat
GET  /api/v1/advisor/conversations
GET  /api/v1/advisor/conversations/:id
33. Advisor Chat Request

Conceptually:

{
  "message": "Why did my savings decrease this month?"
}

The backend then determines the required financial context.

The frontend does not construct the financial context itself.

34. Advisor Flow
User Message
      ↓
Advisor API
      ↓
Intent / Context Selection
      ↓
Financial Services
      ↓
Structured Context
      ↓
Prompt Builder
      ↓
AI Provider
      ↓
Response Validation
      ↓
Advisor Response
35. AI Context Boundary

The AI endpoint must not accept arbitrary raw financial context from the browser.

Avoid:

{
  "message": "...",
  "transactions": [...]
}

as the normal architecture.

Instead:

{
  "message": "Why did my savings decrease?"
}

The backend decides what financial information is relevant.

36. AI Response

A response may conceptually contain:

{
  "message": "Your savings decreased mainly because...",
  "context": {
    "period": "2026-08"
  }
}

The final response contract should distinguish:

Human-readable explanation
Structured metadata
Optional recommendation
37. API Authentication

Protected API requests require authentication.

Conceptually:

Request
   ↓
Authentication Middleware
   ↓
Authenticated User
   ↓
Controller
38. Authentication Header

If bearer-token authentication is used:

Authorization: Bearer <token>

The final token/session implementation will be defined in the security architecture.

The API contract should remain independent of the exact authentication mechanism where practical.

39. Authorization

Authentication answers:

Who is this user?

Authorization answers:

Can this user access this resource?

Every user-owned resource must pass both checks.

Example:

Authenticated User
        ↓
Resource ID
        ↓
Ownership Check
        ↓
Allowed?
40. User Ownership Rule

The backend must derive the user identity from the authenticated session/token.

Never trust:

{
  "userId": "some-id"
}

provided by the frontend for authorization.

41. HTTP Methods

WealthWise follows conventional HTTP semantics.

Method	Purpose
GET	Retrieve
POST	Create / execute
PATCH	Partial update
PUT	Full replacement when required
DELETE	Delete / remove

PATCH is preferred for partial resource updates.

42. HTTP Status Codes

Initial conventions:

Status	Meaning
200	Successful request
201	Resource created
204	Successful request with no body
400	Invalid request
401	Unauthenticated
403	Forbidden
404	Resource not found
409	Conflict
422	Validation failure
429	Rate limited
500	Internal server error
503	Service unavailable

The exact use of 400 vs 422 will be standardized during implementation.

43. Success Response Structure

A consistent response structure should be used where appropriate.

Conceptually:

{
  "success": true,
  "data": {},
  "message": null
}

For collections:

{
  "success": true,
  "data": [],
  "pagination": {}
}
44. Error Response Structure

Errors should use a predictable format.

Conceptually:

{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid transaction amount.",
    "details": []
  }
}

The API should not expose stack traces or internal database details.

45. Error Codes

Error codes should be machine-readable.

Examples:

AUTHENTICATION_REQUIRED
INVALID_CREDENTIALS
FORBIDDEN
RESOURCE_NOT_FOUND
VALIDATION_ERROR
DUPLICATE_RESOURCE
INVALID_TRANSACTION
INVALID_BUDGET
INVALID_GOAL
SCENARIO_ERROR
AI_SERVICE_ERROR
RATE_LIMIT_EXCEEDED
INTERNAL_ERROR
46. Validation

Validation occurs before business logic.

Flow:

Request
   ↓
Schema Validation
   ↓
Authentication
   ↓
Authorization
   ↓
Business Logic

The exact middleware ordering will be finalized in backend implementation.

47. Request Validation

The API must validate:

Body
Query Parameters
Route Parameters
Uploaded Files

Examples:

amount
date
category
transaction type
goal target
budget amount
scenario parameters
AI message
48. Financial Input Validation

Financial amounts should:

Be numeric
Be finite
Respect supported precision
Respect business constraints

Negative amounts should not be accepted where the transaction type model already represents direction through:

type

The exact amount-sign convention will be finalized before implementation.

49. Pagination Convention

Collection endpoints should use consistent parameters.

Initial convention:

?page=1
&limit=20

Response:

{
  "data": [],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5
  }
}

Cursor pagination may be introduced for very large transaction histories.

50. Pagination Limits

The backend must enforce a maximum page size.

Conceptually:

defaultLimit = controlled
maxLimit = controlled

The client cannot request unlimited records.

Exact values will be finalized during implementation.

51. Filtering Convention

Filters should use query parameters.

Example:

GET /api/v1/transactions
    ?category=Food
    &type=EXPENSE

Avoid sending complex filter logic as arbitrary executable expressions.

52. Date Filtering

Date filters should use:

startDate
endDate

Example:

GET /api/v1/transactions
    ?startDate=2026-08-01
    &endDate=2026-08-31

The backend defines exact date boundary semantics.

53. Sorting

Sorting should use controlled fields.

Example:

?sort=date
&order=desc

The backend must whitelist sortable fields.

Do not directly pass arbitrary client strings into MongoDB sorting logic.

54. Search

Search parameters should be controlled.

Example:

?search=amazon

The backend determines whether the search applies to:

merchant
description

or another approved field.

55. Response Projection

The API should return only fields necessary for the client.

For example, a transaction response should not expose:

internal classification metadata
internal database details
private processing metadata

unless the frontend genuinely requires them.

56. Resource IDs

MongoDB ObjectIds may be represented as strings in the HTTP API.

Example:

{
  "id": "66b..."
}

The API does not expose MongoDB implementation details unnecessarily.

57. API Naming Convention

Endpoints use plural resource names:

/transactions
/goals
/budgets
/insights
/scenarios

Avoid:

/getTransactions
/createGoal
/deleteBudget

HTTP methods already express the operation.

58. Nested Resources

Nested routes should only be used when the relationship is meaningful.

Example:

/goals/:id

is sufficient for a goal.

Avoid unnecessarily deep routes such as:

/users/:userId/goals/:goalId

because authentication already identifies the current user.

59. Action Endpoints

Some operations are actions rather than CRUD.

Examples:

/transactions/import
/advisor/chat

These are acceptable because they represent workflows rather than simple resource mutations.

60. Idempotency

Operations that may be retried should consider idempotency.

This is especially relevant to:

transaction imports
financial mutations
future external integrations

The final implementation may introduce idempotency keys for appropriate operations.

61. Transaction Import Idempotency

An import operation should avoid creating duplicate transactions when a request is retried.

Possible strategy:

Import ID
+
Row Identifier
+
User

The exact mechanism will be defined in the transaction API specification.

62. Rate Limiting

Rate limiting should be applied particularly to:

Authentication
AI Advisor
Import APIs

because these endpoints are more susceptible to abuse or expensive processing.

63. AI Rate Limiting

AI requests may consume external resources.

Therefore:

User
   ↓
Rate Limit
   ↓
Advisor API
   ↓
AI Provider

The user should receive a controlled response when the limit is exceeded.

64. AI Failure Handling

If the external AI provider fails:

AI Provider
     ↓
Failure
     ↓
Backend
     ↓
Controlled API Error

The API must not expose:

provider credentials
raw provider errors
internal prompts
stack traces
65. AI Response Validation

AI output should be treated as untrusted generated content.

The backend should validate expected structured output where structured data is required.

For example:

Recommendation
Severity
Category
Scenario Explanation

must conform to the expected schema.

66. Financial Safety Boundary

The API must preserve the rule:

AI
 ↓
Recommendation

not:

AI
 ↓
Automatic Financial Action

The AI Advisor cannot directly:

Create transactions
Delete transactions
Transfer money
Modify bank accounts
Execute investments
67. Analytics Safety

Analytics endpoints are deterministic.

They must not depend on AI to calculate:

Income
Expenses
Savings
Savings Rate
Category Spending
Budget Usage
Goal Progress

AI may explain those results afterward.

68. API Data Flow

The complete API architecture is:

                 FRONTEND
                    │
                    ▼
               REST API
                    │
              ┌─────┴─────┐
              ▼           ▼
        Auth Middleware   Validation
              │           │
              └─────┬─────┘
                    ▼
                Controller
                    │
                    ▼
                 Service
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
   Repository   Intelligence   AI
        │           │           │
        ▼           ▼           ▼
     MongoDB    Financial     Provider
                Services
69. API Module Ownership
API Domain	Primary Backend Module
/auth	Auth
/users	User
/transactions	Transactions
/analytics	Analytics
/budgets	Budgets
/goals	Goals
/insights	Insights
/scenarios	Scenario Engine
/advisor	AI Advisor
70. API and Database Boundary

The API should not expose database collections one-to-one.

For example:

MongoDB
financialContexts

does not automatically mean:

GET /financialContexts

Instead, the API exposes the product capability:

GET /analytics/summary

This keeps the API aligned with product behaviour rather than persistence structure.

71. API and Domain Boundary

The API should translate:

HTTP Request

into:

Domain Operation

rather than:

HTTP Request
↓
MongoDB CRUD
72. Example — Create Transaction

Incorrect architecture:

POST /transactions
      ↓
Controller
      ↓
MongoDB.insert()

Preferred:

POST /transactions
      ↓
Controller
      ↓
Transaction Service
      ↓
Validate financial rules
      ↓
Transaction Repository
      ↓
MongoDB
      ↓
Financial refresh
      ↓
Response
73. Example — Ask Advisor

Incorrect:

POST /advisor
      ↓
Frontend sends all transactions
      ↓
AI provider

Preferred:

POST /advisor/chat
      ↓
Advisor Service
      ↓
Understand request
      ↓
Retrieve relevant financial context
      ↓
Build controlled AI context
      ↓
AI Provider
      ↓
Validate response
      ↓
Return explanation
74. API Documentation

The API should eventually be documented using:

OpenAPI / Swagger

The generated API documentation should describe:

endpoints,
parameters,
request bodies,
response bodies,
authentication,
status codes,
validation errors.
75. API Testing

Every important API endpoint should eventually have tests for:

Success
Validation failure
Authentication failure
Authorization failure
Not found
Conflict
Server failure

Financial endpoints should additionally test correctness of calculations.

76. API Contract Testing

Frontend and backend should agree on:

Request shape
Response shape
Field names
Enums
Error codes
Pagination
Dates
Monetary representation

before feature implementation is considered complete.

77. API Evolution

Backward-compatible changes may be introduced within v1.

Examples:

Adding optional response fields
Adding optional query parameters
Adding new insight types

Breaking changes should require a new API version.

78. API Security Principles

The API must:

Authenticate users
Authorize resource access
Validate input
Rate-limit sensitive endpoints
Protect secrets
Avoid sensitive logging
Sanitize output
Prevent cross-user access
79. API Logging

Logs may contain:

Request method
Route
Status code
Latency
Request ID

Logs must not contain:

Passwords
Authentication tokens
Full transaction histories
AI prompts containing sensitive financial context
AI provider credentials
80. Request IDs

The API may assign a request identifier:

X-Request-ID

This helps correlate:

Frontend
↓
API
↓
Service
↓
Database
↓
AI Provider

during debugging.

81. API Health

The backend should provide an internal health endpoint such as:

GET /health

or:

GET /api/health

The final production route will be determined during deployment.

The health endpoint should not expose sensitive infrastructure information.

82. API Availability

A successful health response should indicate that the application is operational.

A deeper readiness check may verify:

Application
+
Database

without exposing connection details.

83. API Architecture Summary
                    WEALTHWISE API
                         │
                         ▼
                    /api/v1
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
       ▼                 ▼                 ▼
      Auth          Financial Core     Intelligence
                         │                 │
             ┌───────────┼─────────┐       │
             ▼           ▼         ▼       ▼
        Transactions   Goals    Budgets Analytics
                                           │
                             ┌─────────────┴─────────────┐
                             ▼                           ▼
                         Insights                    Scenarios
                             │                           │
                             └────────────┬──────────────┘
                                          ▼
                                      AI Advisor
84. API Design Principle

The WealthWise API should expose:

Financial capabilities, not database mechanics.

The API therefore sits between the user interface and the intelligence architecture:

Frontend
   ↓
API
   ↓
Business Logic
   ↓
Financial Intelligence
   ↓
AI