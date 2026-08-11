
---

# FILE 2 — `docs/07-api/budget-api.md`

```markdown
# WealthWise — Budget API

**Document Version:** 1.0  
**Status:** API Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**API Style:** REST  
**API Version:** v1  

---

# 1. Purpose

This document defines the API contract for budgeting in WealthWise.

The Budget API allows users to:

- create budgets,
- view budgets,
- update budgets,
- delete budgets,
- monitor budget usage,
- identify budget status,
- and connect spending behaviour with planned limits.

A budget represents a user's intended spending constraint.

Actual spending remains determined by transactions.

---

# 2. Budget Principle

The core relationship is:

```text
Budget
   +
Actual Transactions
   ↓
Budget Usage
   ↓
Budget Status

A budget does not modify transaction data.

3. Budget Endpoints

Initial endpoints:

Method	Endpoint	Purpose
GET	/api/v1/budgets	List budgets
POST	/api/v1/budgets	Create budget
GET	/api/v1/budgets/:id	Retrieve budget
PATCH	/api/v1/budgets/:id	Update budget
DELETE	/api/v1/budgets/:id	Delete budget
GET	/api/v1/budgets/:id/progress	Calculate budget progress
4. Authentication

All budget endpoints require authentication.

Request
   ↓
Authentication
   ↓
Authenticated User
   ↓
Budget Service

All budgets are scoped to the authenticated user.

5. List Budgets
Endpoint
GET /api/v1/budgets

Returns budgets belonging to the authenticated user.

6. Budget Filters

The listing endpoint may support:

status
category
period
startDate
endDate

Example:

GET /api/v1/budgets
    ?status=ACTIVE
    &category=Food
7. Budget List Response

Conceptual response:

{
  "success": true,
  "data": [
    {
      "id": "budget-id",
      "category": "Food",
      "amount": 6000,
      "period": "MONTHLY",
      "startDate": "2026-08-01",
      "endDate": "2026-09-01",
      "status": "ON_TRACK"
    }
  ]
}

Progress information may be included if it is inexpensive to calculate or already available through the budget service.

8. Create Budget
Endpoint
POST /api/v1/budgets
9. Create Request

Example:

{
  "category": "Food",
  "amount": 6000,
  "period": "MONTHLY",
  "startDate": "2026-08-01",
  "endDate": "2026-09-01"
}
10. Create Request Fields
Field	Required	Description
category	Yes	Budget category
amount	Yes	Spending limit
period	Yes	Budget period
startDate	Yes	Budget start
endDate	Yes	Budget end

userId is derived from authentication.

11. Budget Validation

The API must validate:

category
amount
period
startDate
endDate

Rules include:

amount > 0

endDate > startDate

category must be valid

period must be supported
12. Budget Period

Initial supported values:

WEEKLY
MONTHLY
YEARLY
CUSTOM
13. Budget Category

The category should correspond to a supported financial category.

Initial categories:

Housing
Food
Transport
Shopping
Entertainment
Healthcare
Education
Utilities
Subscriptions
Travel
Personal Care
Financial
Other
14. Budget Creation Response

Successful creation:

HTTP 201 Created

Example:

{
  "success": true,
  "data": {
    "id": "budget-id",
    "category": "Food",
    "amount": 6000,
    "period": "MONTHLY",
    "startDate": "2026-08-01",
    "endDate": "2026-09-01"
  }
}
15. Get Budget
Endpoint
GET /api/v1/budgets/:id

The API must verify ownership.

16. Budget Response

Example:

{
  "success": true,
  "data": {
    "id": "budget-id",
    "category": "Food",
    "amount": 6000,
    "period": "MONTHLY",
    "startDate": "2026-08-01",
    "endDate": "2026-09-01"
  }
}
17. Budget Not Found

If the budget does not exist or belongs to another user:

HTTP 404 Not Found

Conceptual response:

{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Budget not found."
  }
}
18. Update Budget
Endpoint
PATCH /api/v1/budgets/:id

Allows partial modification of a budget.

19. Editable Budget Fields

Potentially editable:

category
amount
period
startDate
endDate

Every update must be validated against the complete resulting budget state.

20. Update Example
{
  "amount": 7000
}

The backend updates the budget while retaining its other properties.

21. Budget Update Validation

The updated budget must still satisfy:

amount > 0

endDate > startDate

valid category

valid period
22. Delete Budget
Endpoint
DELETE /api/v1/budgets/:id

Deletes or deactivates the budget according to the final retention policy.

Transactions remain unchanged.

23. Budget Progress
Endpoint
GET /api/v1/budgets/:id/progress

This endpoint calculates actual spending against the budget.

24. Progress Calculation

Conceptually:

Budget
   ↓
Determine Budget Period
   ↓
Retrieve Matching Transactions
   ↓
Filter Category
   ↓
Calculate Actual Spending
   ↓
Compare Against Limit
25. Budget Progress Response

Example:

{
  "success": true,
  "data": {
    "budgetId": "budget-id",
    "limit": 6000,
    "spent": 4200,
    "remaining": 1800,
    "percentageUsed": 70,
    "status": "ON_TRACK"
  }
}
26. Spent Amount

Spent amount is derived from relevant transactions.

Conceptually:

Spent =
SUM(
  qualifying EXPENSE transactions
)

within:

budget period
+
budget category
+
authenticated user
27. Remaining Amount

Initial formula:

Remaining =
Budget Limit - Spent Amount

If spending exceeds the budget:

Remaining < 0

may be preserved internally to represent the amount exceeded.

The API may additionally expose:

amountExceeded

if required.

28. Percentage Used

Formula:

Percentage Used =
(Spent / Budget Limit) × 100

Example:

Spent = ₹4,200
Limit = ₹6,000

Percentage Used = 70%
29. Budget Status

Initial conceptual statuses:

ON_TRACK
WARNING
EXCEEDED
COMPLETED

The exact threshold definitions belong to the financial business-rules specification.

30. Budget Status Example

Possible interpretation:

0–79%
→ ON_TRACK

80–99%
→ WARNING

100%+
→ EXCEEDED

These values are provisional and should be finalized as business rules rather than hard-coded directly into controllers.

31. Budget and Transactions

The Budget API reads transaction data to determine actual spending.

Budget
   +
Transactions
   ↓
Budget Progress

The Budget API must never modify transactions as part of progress calculation.

32. Budget and Analytics

Analytics provide general financial measurements.

Budgeting provides:

planned vs actual spending.

Example:

Analytics:
Food spending = ₹4,200

Budget:
Food limit = ₹6,000

Budget analysis:
70% of budget consumed
33. Budget and Insights

Budget status may contribute to the Insight Engine.

Example:

Budget
   ↓
95% consumed
   ↓
Behaviour / Budget Signal
   ↓
Insight

Potential insight:

You are close to reaching your Food budget for this month.

The Budget API itself does not generate the final natural-language insight.

34. Budget and Goals

Budgets may indirectly influence financial goals.

Example:

Reduced discretionary spending
        ↓
Higher potential savings
        ↓
Improved goal feasibility

The Budget API does not directly implement goal calculations.

The Goal and Intelligence services consume the relevant budget state.

35. Budget Overlap

The system should define how overlapping budgets are handled.

Example:

Budget A:
Food
August

Budget B:
Food
August

The initial MVP should prevent ambiguous duplicate active budgets for the same user, category, and period where practical.

The final uniqueness rule will be established during implementation.

36. Budget Period Overlap

Custom periods may overlap.

Example:

Budget A:
1 Aug → 31 Aug

Budget B:
15 Aug → 15 Sep

The API must either:

allow with explicit semantics

or:

reject conflicting budgets

The MVP should prefer simple, predictable budgeting rules.

37. Budget Query Authorization

Correct:

{
  _id: budgetId,
  userId: authenticatedUserId
}

Incorrect:

{
  _id: budgetId
}

The second approach could expose another user's budget.

38. Budget Progress Query

Conceptually:

Budget ID
    ↓
Verify Ownership
    ↓
Load Budget
    ↓
Extract Category + Period
    ↓
Query Transactions
    ↓
Calculate Spending
    ↓
Determine Status
    ↓
Return Result
39. Budget Performance

Budget progress should be calculated efficiently.

The transaction query should be restricted using:

userId
category
date range
transaction type

This allows the database to use appropriate indexes.

40. Budget Caching

The MVP does not require a dedicated cached budget-progress collection.

The initial approach is:

Budget
+
Indexed Transactions
↓
Calculated Progress

A cached representation may be introduced later if dashboard performance requires it.

41. Budget Error Codes

Initial budget-specific errors:

INVALID_BUDGET
INVALID_BUDGET_AMOUNT
INVALID_BUDGET_PERIOD
INVALID_BUDGET_DATE_RANGE
INVALID_CATEGORY
BUDGET_NOT_FOUND
BUDGET_CONFLICT
42. Budget API Security

The Budget API must protect against:

Cross-user access
Unauthorized modification
Invalid budget values
Arbitrary database filters
43. Budget API and AI

The AI Advisor may consume budget context.

Example:

Food Budget:
₹6,000

Spent:
₹5,700

Remaining:
₹300

The AI may explain:

You have only ₹300 remaining in your Food budget for the current period.

However, the AI does not calculate the ₹300.

44. Budget API and Scenario Engine

The Scenario Engine may simulate budget changes.

Example:

Current Food Budget:
₹6,000

Scenario:
Increase budget by ₹1,000

Projected Budget:
₹7,000

The scenario remains hypothetical.

The actual budget changes only through an explicit budget mutation.

45. Budget API and Dashboard

The dashboard may request:

GET /api/v1/budgets

and display:

Category
Limit
Spent
Remaining
Percentage Used
Status

The dashboard should not calculate these values independently.

46. Budget Response Design

The API should return structured data.

Example:

{
  "id": "budget-id",
  "category": "Food",
  "limit": 6000,
  "spent": 4200,
  "remaining": 1800,
  "percentageUsed": 70,
  "status": "ON_TRACK"
}
47. Budget Lifecycle
             CREATE
                │
                ▼
            VALIDATE
                │
                ▼
             ACTIVE
                │
                ▼
        Transaction Activity
                │
                ▼
         Calculate Progress
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
   ON_TRACK  WARNING  EXCEEDED
                │
                ▼
             COMPLETE
48. Budget Source-of-Truth Boundary

The budget represents:

What the user plans to spend.

Transactions represent:

What the user actually spent.

Therefore:

Budget ≠ Transaction

and:

Budget
+
Transactions
=
Budget Intelligence
49. Budget API Endpoint Summary
GET /api/v1/budgets
    → List budgets

POST /api/v1/budgets
    → Create budget

GET /api/v1/budgets/:id
    → Retrieve budget

PATCH /api/v1/budgets/:id
    → Update budget

DELETE /api/v1/budgets/:id
    → Delete/deactivate budget

GET /api/v1/budgets/:id/progress
    → Calculate budget progress
50. Budget Architecture
                    BUDGET API
                        │
                        ▼
                  Budget Service
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
        Budget Repository   Transaction Service
              │                   │
              ▼                   ▼
          Budgets             Transactions
              │                   │
              └─────────┬─────────┘
                        ▼
                 Budget Progress
                        │
              ┌─────────┼─────────┐
              ▼         ▼         ▼
           Dashboard  Insights   Goals
51. Final Principle

The Budget API must preserve a strict distinction:

A budget is a plan.

A transaction is an actual financial event.

Budget progress is derived from comparing the two.