# WealthWise — Goal API

**Document Version:** 1.0  
**Status:** API Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**API Style:** REST  
**API Version:** v1  

---

# 1. Purpose

This document defines the API contract for financial goals in WealthWise.

Goals allow users to define financial objectives and connect their financial behaviour with those objectives.

Examples include:

- emergency fund,
- vacation,
- education,
- major purchase,
- device purchase,
- wedding,
- investment corpus,
- custom savings target.

The Goal API manages the goal itself.

Goal feasibility, behavioural analysis, and AI recommendations are handled by the appropriate intelligence services.

---

# 2. Goal Principle

The core relationship is:

```text
User Goal
    +
Financial Behaviour
    +
Current Savings
    +
Time Remaining
    ↓
Goal Progress / Feasibility

A goal is an objective.

It is not itself a transaction.

3. Goal Endpoints

Initial endpoints:

Method	Endpoint	Purpose
GET	/api/v1/goals	List goals
POST	/api/v1/goals	Create goal
GET	/api/v1/goals/:id	Retrieve goal
PATCH	/api/v1/goals/:id	Update goal
DELETE	/api/v1/goals/:id	Delete/deactivate goal
GET	/api/v1/goals/:id/progress	Retrieve goal progress
4. Authentication

All goal endpoints require authentication.

Request
   ↓
Authentication
   ↓
Authenticated User
   ↓
Goal Service

The authenticated user's identity determines goal ownership.

5. Goal List
Endpoint
GET /api/v1/goals

Returns goals belonging to the authenticated user.

6. Goal Filters

The endpoint may support:

status
priority
targetDate

Example:

GET /api/v1/goals?status=ACTIVE
7. Goal Status

Initial statuses:

ACTIVE
COMPLETED
PAUSED
CANCELLED

The exact lifecycle rules will be finalized in the business-rules documentation.

8. Goal Priority

Initial priority values:

LOW
MEDIUM
HIGH

Priority represents the user's importance assigned to the goal.

It does not automatically determine the financial recommendation.

9. Goal List Response

Conceptual response:

{
  "success": true,
  "data": [
    {
      "id": "goal-id",
      "name": "Emergency Fund",
      "targetAmount": 100000,
      "currentAmount": 30000,
      "targetDate": "2027-01-01",
      "status": "ACTIVE",
      "priority": "HIGH"
    }
  ]
}
10. Create Goal
Endpoint
POST /api/v1/goals

Creates a new financial goal.

11. Create Goal Request

Example:

{
  "name": "Emergency Fund",
  "description": "Build an emergency savings fund",
  "targetAmount": 100000,
  "currentAmount": 30000,
  "targetDate": "2027-01-01",
  "priority": "HIGH"
}
12. Goal Request Fields
Field	Required	Description
name	Yes	Goal name
description	No	Goal description
targetAmount	Yes	Desired amount
currentAmount	No	Amount already allocated
targetDate	Yes	Target completion date
priority	No	User-defined importance

userId is derived from authentication.

13. Goal Validation

The API must validate:

name
targetAmount
currentAmount
targetDate
priority

Initial rules:

targetAmount > 0

currentAmount >= 0

currentAmount <= targetAmount

targetDate must be valid

priority must be supported
14. Current Amount

currentAmount represents the amount already allocated toward the goal.

It should not automatically be interpreted as:

total bank balance

or:

total savings

It is goal-specific progress.

15. Create Goal Response

Successful creation:

HTTP 201 Created

Example:

{
  "success": true,
  "data": {
    "id": "goal-id",
    "name": "Emergency Fund",
    "targetAmount": 100000,
    "currentAmount": 30000,
    "targetDate": "2027-01-01",
    "priority": "HIGH",
    "status": "ACTIVE"
  }
}
16. Get Goal
Endpoint
GET /api/v1/goals/:id

Returns a single goal belonging to the authenticated user.

17. Goal Not Found

If the goal does not exist or belongs to another user:

HTTP 404 Not Found

Example:

{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Goal not found."
  }
}
18. Update Goal
Endpoint
PATCH /api/v1/goals/:id

Allows partial modification of a goal.

19. Editable Goal Fields

Potentially editable:

name
description
targetAmount
currentAmount
targetDate
priority
status

Every update must be validated against the complete resulting state.

20. Update Example
{
  "targetAmount": 120000,
  "priority": "HIGH"
}

The backend updates only the permitted fields.

21. Goal Completion

A goal may become:

COMPLETED

when its completion criteria are satisfied.

The initial condition may be:

currentAmount >= targetAmount

The exact completion semantics will be finalized in business rules.

22. Goal Deletion
Endpoint
DELETE /api/v1/goals/:id

A goal may be deleted or deactivated according to the final retention policy.

Deleting a goal must not modify:

Transactions
Budgets
Analytics
23. Goal Progress
Endpoint
GET /api/v1/goals/:id/progress

Returns derived progress information.

24. Goal Progress Calculation

Conceptually:

Target Amount
      ↓
Current Amount
      ↓
Remaining Amount
      ↓
Progress Percentage
25. Progress Formula
Progress =
(Current Amount / Target Amount) × 100

Example:

Current = ₹30,000
Target  = ₹100,000

Progress = 30%

The result may be capped at 100% for presentation purposes.

26. Remaining Amount
Remaining =
Target Amount - Current Amount

Example:

Target  = ₹100,000
Current = ₹30,000

Remaining = ₹70,000
27. Goal Progress Response

Example:

{
  "success": true,
  "data": {
    "goalId": "goal-id",
    "targetAmount": 100000,
    "currentAmount": 30000,
    "remainingAmount": 70000,
    "progressPercentage": 30,
    "targetDate": "2027-01-01",
    "status": "ACTIVE"
  }
}
28. Required Contribution

Goal intelligence may calculate the amount required to reach the target.

Conceptually:

Remaining Amount
        ÷
Remaining Period
        =
Required Periodic Contribution

This is derived information.

It should not be stored as an authoritative goal value unless explicitly required.

29. Goal Feasibility

Goal feasibility answers:

Can the user realistically reach this goal based on their current financial behaviour?

It may consider:

Current Amount
Target Amount
Income
Expenses
Savings
Historical Savings
Time Remaining
Existing Goals
Budgets

This belongs to the intelligence layer.

30. Goal API vs Goal Intelligence

The boundary is:

Goal API
    ↓
"What is the user's goal?"

while:

Goal Intelligence
    ↓
"How realistic is this goal?"
31. Goal and Transactions

Transactions may provide financial context for goal analysis.

However:

Goal

does not become a transaction.

The relationship is:

Transactions
     ↓
Financial Behaviour
     ↓
Goal Analysis
32. Goal and Analytics

Analytics may provide:

Monthly Income
Monthly Expenses
Monthly Savings
Savings Rate
Historical Savings

Goal intelligence can use these metrics to assess feasibility.

33. Goal and Budgets

Budgets may influence available savings.

Example:

Reduced discretionary spending
        ↓
Potential savings increase
        ↓
Goal feasibility improves

The Goal API itself does not modify budgets.

34. Goal and Insights

Goal-related signals may produce insights.

Example:

Goal:
₹100,000 by January

Current progress:
30%

Required contribution:
₹10,000/month

Historical savings:
₹6,000/month

This may produce an insight such as:

Your current savings pace may not be sufficient to reach this goal on schedule.

The final natural-language insight belongs to the Insight Engine.

35. Goal and Scenario Engine

The Scenario Engine may simulate changes.

Example:

Current savings:
₹6,000/month

Scenario:
Save ₹2,000 more/month

Projected goal completion:
Earlier than current estimate

The scenario does not change the actual goal.

36. Goal and AI Advisor

The AI Advisor may explain goal progress and feasibility.

Example:

User:
"Can I reach my travel goal by December?"

The backend retrieves relevant:

Goal
+
Financial Context
+
Historical Savings

Then provides structured context to the AI.

37. AI Boundary

The AI may:

Explain
Recommend
Compare
Simulate

The AI may not directly:

Modify goal
Modify transaction
Transfer money
Create financial transactions
38. Goal Authorization

Every goal query must include:

authenticatedUserId

Correct:

{
  _id: goalId,
  userId: authenticatedUserId
}
39. Goal Query Performance

Common goal queries include:

userId
status
targetDate

These should be supported by appropriate indexes defined in the database documentation.

40. Goal Error Codes

Initial goal-specific errors:

INVALID_GOAL
INVALID_TARGET_AMOUNT
INVALID_CURRENT_AMOUNT
INVALID_TARGET_DATE
INVALID_GOAL_STATUS
INVALID_GOAL_PRIORITY
GOAL_NOT_FOUND
GOAL_ALREADY_COMPLETED
41. Empty Goal State

A user with no goals should receive:

{
  "success": true,
  "data": []
}

rather than an error.

42. Goal API Security

The API must prevent:

Cross-user goal access
Unauthorized modification
Invalid monetary values
Invalid dates
Invalid goal states
43. Goal Lifecycle
              CREATE
                 │
                 ▼
               ACTIVE
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
   Progress   Pause     Cancel
       │
       ▼
   Target Reached
       │
       ▼
   COMPLETED
44. Goal API Endpoint Summary
GET /api/v1/goals
    → List goals

POST /api/v1/goals
    → Create goal

GET /api/v1/goals/:id
    → Retrieve goal

PATCH /api/v1/goals/:id
    → Update goal

DELETE /api/v1/goals/:id
    → Delete/deactivate goal

GET /api/v1/goals/:id/progress
    → Retrieve goal progress
45. Goal Architecture
                     GOAL API
                        │
                        ▼
                   Goal Service
                        │
               ┌────────┴────────┐
               ▼                 ▼
        Goal Repository     Analytics Service
               │                 │
               ▼                 ▼
             Goals          Financial Metrics
               │                 │
               └────────┬────────┘
                        ▼
                 Goal Intelligence
                        │
             ┌──────────┼──────────┐
             ▼          ▼          ▼
         Feasibility  Insights    AI Advisor
46. Final Principle

The Goal API represents:

What the user wants to achieve financially.

It does not itself determine:

Whether the user can achieve it.

That determination belongs to WealthWise's intelligence layer.