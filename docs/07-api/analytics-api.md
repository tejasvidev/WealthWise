# WealthWise — Analytics API

**Document Version:** 1.0  
**Status:** API Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**API Style:** REST  
**API Version:** v1  

---

# 1. Purpose

This document defines the API contract for WealthWise financial analytics.

Analytics transform authoritative transaction data into structured financial measurements.

The Analytics API is responsible for exposing:

- income,
- expenses,
- savings,
- savings rate,
- category spending,
- spending distribution,
- historical trends,
- period comparisons,
- and other deterministic financial metrics.

The Analytics API does not replace the Intelligence or AI layers.

---

# 2. Analytics Principle

The fundamental architecture is:

```text
Transactions
      ↓
Deterministic Calculations
      ↓
Financial Metrics
      ↓
Analytics API
      ↓
Frontend / Intelligence / AI Context

The API returns calculated financial facts.

AI may later explain those facts.

3. Analytics Are Derived Data

Analytics are not authoritative financial records.

For example:

Transactions
    ↓
Income = ₹50,000
Expenses = ₹32,000
Savings = ₹18,000

The ₹18,000 savings figure is derived.

If transaction data changes, the metric must be recalculated.

4. Analytics Endpoints

Initial endpoints:

Method	Endpoint	Purpose
GET	/api/v1/analytics/summary	Financial overview
GET	/api/v1/analytics/income	Income analysis
GET	/api/v1/analytics/expenses	Expense analysis
GET	/api/v1/analytics/savings	Savings analysis
GET	/api/v1/analytics/categories	Category spending
GET	/api/v1/analytics/trends	Historical trends
GET	/api/v1/analytics/comparison	Period comparison
5. Authentication

All analytics endpoints require authentication.

Request
   ↓
Authentication
   ↓
Authenticated User
   ↓
Analytics Service

Analytics must always be calculated from the authenticated user's transactions.

6. Analysis Period

Analytics endpoints use a controlled date range.

Example:

GET /api/v1/analytics/summary
    ?startDate=2026-08-01
    &endDate=2026-09-01

The preferred internal convention is:

date >= startDate
AND
date < endDate

This creates a half-open interval.

7. Default Period

If the client does not provide a period, the API may use a defined default.

The initial default is:

Current Calendar Month

The exact timezone used for determining calendar boundaries comes from the user's configured timezone where applicable.

8. Summary Endpoint
Endpoint
GET /api/v1/analytics/summary
Purpose

Returns the primary financial state for the selected period.

9. Summary Response

Conceptual response:

{
  "success": true,
  "data": {
    "period": {
      "startDate": "2026-08-01",
      "endDate": "2026-09-01"
    },
    "income": 50000,
    "expenses": 32000,
    "savings": 18000,
    "savingsRate": 36
  }
}
10. Income

Income is calculated from transactions classified as:

type = INCOME

Conceptually:

Income
=
SUM(INCOME transactions)

Only transactions belonging to the authenticated user and selected period are considered.

11. Expenses

Expenses are calculated from:

type = EXPENSE

Conceptually:

Expenses
=
SUM(EXPENSE transactions)

The treatment of refunds and transfers follows the financial business rules.

12. Savings

WealthWise initially defines:

Savings = Income - Expenses

Example:

Income   = ₹50,000
Expenses = ₹32,000

Savings  = ₹18,000

Savings is derived and is not stored as a transaction.

13. Savings Rate

The initial formula is:

Savings Rate =
(Savings / Income) × 100

Example:

Savings = ₹18,000
Income  = ₹50,000

Savings Rate = 36%

If income is zero, the API must handle the calculation explicitly rather than producing an invalid numeric result.

14. Income Endpoint
Endpoint
GET /api/v1/analytics/income

Returns income information for the selected period.

Conceptual response:

{
  "success": true,
  "data": {
    "total": 50000,
    "transactionCount": 2
  }
}
15. Expense Endpoint
Endpoint
GET /api/v1/analytics/expenses

Conceptual response:

{
  "success": true,
  "data": {
    "total": 32000,
    "transactionCount": 48
  }
}
16. Savings Endpoint
Endpoint
GET /api/v1/analytics/savings

Conceptual response:

{
  "success": true,
  "data": {
    "income": 50000,
    "expenses": 32000,
    "savings": 18000,
    "savingsRate": 36
  }
}
17. Category Analytics
Endpoint
GET /api/v1/analytics/categories

Purpose:

Determine how expenses are distributed across categories.

18. Category Response

Conceptual response:

{
  "success": true,
  "data": {
    "categories": [
      {
        "category": "Food",
        "amount": 6000,
        "percentage": 18.75,
        "transactionCount": 18
      },
      {
        "category": "Transport",
        "amount": 3200,
        "percentage": 10,
        "transactionCount": 12
      }
    ]
  }
}
19. Category Percentage

For each category:

Category Percentage =
(Category Amount / Total Expenses) × 100

If total expenses are zero, the API must return a defined zero/empty representation rather than divide by zero.

20. Category Ordering

The default ordering should be:

Highest spending
        ↓
Lowest spending

unless the client explicitly requests another supported ordering.

21. Category Filter

The category endpoint may optionally support:

category

when a specific category is required.

However, a request for a single category may be more appropriately served by the transaction or analytics service depending on the use case.

22. Trends Endpoint
Endpoint
GET /api/v1/analytics/trends

Purpose:

Show how financial behaviour changes across multiple periods.

23. Trend Period

The API may support a controlled interval such as:

MONTHLY
WEEKLY
YEARLY

Example:

GET /api/v1/analytics/trends
    ?startDate=2026-01-01
    &endDate=2026-09-01
    &groupBy=month

The backend must whitelist supported grouping values.

24. Trend Response

Conceptual response:

{
  "success": true,
  "data": [
    {
      "period": "2026-06",
      "income": 48000,
      "expenses": 30000,
      "savings": 18000
    },
    {
      "period": "2026-07",
      "income": 50000,
      "expenses": 31000,
      "savings": 19000
    },
    {
      "period": "2026-08",
      "income": 50000,
      "expenses": 32000,
      "savings": 18000
    }
  ]
}
25. Category Trends

The trend API may optionally return category-level trends.

Example:

{
  "period": "2026-08",
  "category": "Food",
  "amount": 6000
}

This allows the frontend and Intelligence Engine to identify changes over time.

26. Period Comparison
Endpoint
GET /api/v1/analytics/comparison

Purpose:

Compare two financial periods.

Example:

Current Period:
August 2026

Previous Period:
July 2026
27. Comparison Request

Conceptually:

GET /api/v1/analytics/comparison
    ?currentStart=2026-08-01
    &currentEnd=2026-09-01
    &previousStart=2026-07-01
    &previousEnd=2026-08-01

The API validates all date ranges.

28. Comparison Response

Conceptual response:

{
  "success": true,
  "data": {
    "income": {
      "current": 50000,
      "previous": 48000,
      "change": 2000,
      "changePercentage": 4.17
    },
    "expenses": {
      "current": 32000,
      "previous": 30000,
      "change": 2000,
      "changePercentage": 6.67
    },
    "savings": {
      "current": 18000,
      "previous": 18000,
      "change": 0,
      "changePercentage": 0
    }
  }
}
29. Change Calculation

For a metric:

Change =
Current - Previous

Percentage change:

Change Percentage =
((Current - Previous) / Previous) × 100

If the previous value is zero, the API must use an explicit zero-baseline rule.

30. Historical Baseline

The Intelligence Engine may request historical analytics to establish a baseline.

Example:

Current Food Spending
        ↓
Previous 3 Months
        ↓
Average Food Spending
        ↓
Deviation

The Analytics API provides the deterministic measurements.

The Behaviour Engine decides whether the deviation is meaningful.

31. Analytics and Behaviour Intelligence

The boundary is:

Analytics
    ↓
"What happened?"

versus:

Behaviour Intelligence
    ↓
"Is this meaningful?"

Example:

Analytics:
Food spending increased 24%.

Behaviour:
This is significantly above the user's recent baseline.
32. Analytics and Insights

Analytics produce facts.

Insights interpret those facts into user-relevant observations.

Analytics
    ↓
Food spending +24%
    ↓
Behaviour Signal
    ↓
Insight
33. Analytics and AI

AI should receive structured analytics rather than independently calculating financial values.

Example:

Analytics:
Income = ₹50,000
Expenses = ₹32,000
Savings = ₹18,000

AI may explain:

Your savings remained stable even though your expenses increased because your income also increased.

AI does not calculate the underlying financial truth.

34. Analytics Query Performance

Analytics should use database-side aggregation where appropriate.

Preferred:

MongoDB
    ↓
Aggregation
    ↓
Financial Result

Avoid unnecessarily loading the complete transaction history into Node.js.

35. Analytics Response Precision

Monetary values must use the same representation defined by the database architecture.

The API must not introduce a different monetary precision convention.

36. Analytics Error Cases

Potential errors:

INVALID_DATE_RANGE
INVALID_GROUPING
INVALID_CATEGORY
INVALID_PERIOD
VALIDATION_ERROR
37. Empty Data

If a user has no transactions for the requested period, the API should return a valid zero/empty financial result.

Example:

{
  "success": true,
  "data": {
    "income": 0,
    "expenses": 0,
    "savings": 0,
    "savingsRate": 0
  }
}

The exact representation of unavailable metrics will be standardized during implementation.

38. Analytics Security

Analytics must always be scoped to:

authenticatedUserId

A client cannot request analytics for another user by supplying another userId.

39. Analytics Endpoint Summary
GET /api/v1/analytics/summary
    → Overall financial state

GET /api/v1/analytics/income
    → Income analysis

GET /api/v1/analytics/expenses
    → Expense analysis

GET /api/v1/analytics/savings
    → Savings analysis

GET /api/v1/analytics/categories
    → Category distribution

GET /api/v1/analytics/trends
    → Historical trends

GET /api/v1/analytics/comparison
    → Period comparison
40. Analytics Architecture
                TRANSACTIONS
                     │
                     ▼
              Analytics Service
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       Income     Expenses    Savings
          │          │          │
          └──────────┼──────────┘
                     ▼
               Categories
                     │
                     ▼
                  Trends
                     │
                     ▼
                Comparison
                     │
             ┌───────┴───────┐
             ▼               ▼
       Behaviour         Dashboard
       Intelligence
             │
             ▼
          Insights
             │
             ▼
         AI Advisor