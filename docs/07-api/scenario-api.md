# WealthWise — Scenario API

**Document Version:** 1.0  
**Status:** API Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**API Style:** REST  
**API Version:** v1  

---

# 1. Purpose

This document defines the API contract for the WealthWise Scenario Engine.

Scenarios allow users to explore hypothetical financial decisions without changing their actual financial data.

Examples:

- What if I reduce food spending by 20%?
- What if I save ₹5,000 more every month?
- What if my income increases by ₹10,000?
- What if I increase my travel budget?
- How much earlier can I reach my goal if I save more?

The Scenario API provides **simulation**, not financial execution.

---

# 2. Core Principle

The most important rule of the Scenario Engine is:

> **A scenario must never silently modify the user's real financial state.**

The relationship is:

```text
Actual Financial State
        │
        ▼
Scenario Assumptions
        │
        ▼
Simulation Engine
        │
        ▼
Projected Financial State

The projected state is hypothetical.

3. Scenario vs Actual Data
Actual Transaction
→ Represents what actually happened.

Scenario
→ Represents what might happen.

Insight
→ Interprets what actually happened.

Recommendation
→ Suggests what the user could do.

These concepts must remain separate.

4. Scenario Endpoints

Initial endpoints:

Method	Endpoint	Purpose
POST	/api/v1/scenarios	Create and execute scenario
GET	/api/v1/scenarios	List scenario history
GET	/api/v1/scenarios/:id	Retrieve scenario
DELETE	/api/v1/scenarios/:id	Remove scenario history
5. Authentication

All scenario endpoints require authentication.

Request
   ↓
Authentication
   ↓
Authenticated User
   ↓
Scenario Service

Every scenario belongs to the authenticated user.

6. Scenario Types

Initial scenario types:

REDUCE_CATEGORY_SPENDING
INCREASE_CATEGORY_SPENDING
INCREASE_SAVINGS
CHANGE_INCOME
CHANGE_EXPENSE
GOAL_CONTRIBUTION
CUSTOM

The exact supported types may evolve as the Scenario Engine develops.

7. Reduce Category Spending

Example:

What happens if I reduce food spending by 20%?

Request:

{
  "type": "REDUCE_CATEGORY_SPENDING",
  "parameters": {
    "category": "Food",
    "reductionPercentage": 20
  }
}
8. Reduce Spending Calculation

Conceptually:

Current Category Spending
        ↓
Apply Reduction %
        ↓
Projected Category Spending
        ↓
Savings Difference

Example:

Current Food Spending = ₹6,000

Reduction = 20%

Projected Spending = ₹4,800

Potential Savings Improvement = ₹1,200
9. Increase Savings Scenario

Example:

What if I save ₹3,000 more every month?

Request:

{
  "type": "INCREASE_SAVINGS",
  "parameters": {
    "additionalMonthlySavings": 3000
  }
}

The Scenario Engine calculates the projected effect on relevant financial goals.

10. Change Income Scenario

Example:

What if my monthly income increases by ₹10,000?

Request:

{
  "type": "CHANGE_INCOME",
  "parameters": {
    "monthlyChange": 10000
  }
}

The simulation may calculate:

Projected Income
Projected Savings
Projected Savings Rate
Goal Impact
11. Change Expense Scenario

Example:

What if my monthly expenses increase by ₹5,000?

Request:

{
  "type": "CHANGE_EXPENSE",
  "parameters": {
    "monthlyChange": 5000
  }
}

The engine calculates the projected effect without modifying actual transactions.

12. Goal Contribution Scenario

Example:

What if I contribute ₹8,000 per month toward my travel goal?

Request:

{
  "type": "GOAL_CONTRIBUTION",
  "parameters": {
    "goalId": "goal-id",
    "monthlyContribution": 8000
  }
}

The engine may estimate:

Projected completion date
Required contribution
Remaining amount
Time saved
13. Custom Scenario

The CUSTOM scenario type exists for future extensibility.

A custom scenario must still conform to a controlled parameter schema.

The API must never allow arbitrary executable expressions from the client.

14. Create Scenario
Endpoint
POST /api/v1/scenarios

The endpoint creates and executes a scenario.

15. Create Request

Example:

{
  "type": "REDUCE_CATEGORY_SPENDING",
  "parameters": {
    "category": "Food",
    "reductionPercentage": 20
  }
}
16. Request Validation

The API validates:

scenario type
parameters
category
percentages
monetary values
goal IDs
date ranges

Invalid scenario parameters must be rejected before simulation.

17. Scenario Execution Flow
Scenario Request
       ↓
Authentication
       ↓
Validation
       ↓
Load Financial Context
       ↓
Load Relevant Goals / Budgets
       ↓
Apply Hypothetical Assumptions
       ↓
Run Simulation
       ↓
Calculate Projected Results
       ↓
Persist Scenario
       ↓
Return Result
18. Scenario Does Not Mutate Actual Data

The Scenario Engine must not perform operations such as:

UPDATE transactions
DELETE transactions
CREATE transactions
MODIFY budgets
MODIFY goals

as a side effect of simulation.

19. Scenario Result

Conceptual response:

{
  "success": true,
  "data": {
    "id": "scenario-id",
    "type": "REDUCE_CATEGORY_SPENDING",
    "parameters": {
      "category": "Food",
      "reductionPercentage": 20
    },
    "result": {
      "currentSpending": 6000,
      "projectedSpending": 4800,
      "potentialMonthlySavings": 1200
    }
  }
}
20. Scenario Result Categories

Depending on scenario type, results may include:

Current Value
Projected Value
Difference
Percentage Change
Monthly Impact
Annual Impact
Goal Impact
Projected Completion Date

Only relevant fields should be returned.

21. Scenario History
Endpoint
GET /api/v1/scenarios

Returns previous scenarios belonging to the authenticated user.

22. Scenario History Response

Example:

{
  "success": true,
  "data": [
    {
      "id": "scenario-id",
      "type": "INCREASE_SAVINGS",
      "createdAt": "2026-08-10T12:00:00Z"
    }
  ]
}

Pagination may be added when scenario history becomes large.

23. Retrieve Scenario
Endpoint
GET /api/v1/scenarios/:id

Returns the stored scenario and its result.

24. Scenario Not Found

If the scenario does not exist or belongs to another user:

HTTP 404 Not Found

Conceptual response:

{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Scenario not found."
  }
}
25. Delete Scenario
Endpoint
DELETE /api/v1/scenarios/:id

Deleting scenario history does not affect:

Transactions
Budgets
Goals
Analytics
Insights
26. Scenario Data Ownership

Every scenario must contain:

userId

derived from the authenticated identity.

The client must not be able to assign the scenario to another user.

27. Scenario and Analytics

The Scenario Engine consumes analytics.

Example:

Current Expenses = ₹32,000

Scenario:
Reduce Food by 20%

Projected Expenses = ₹30,800

The Scenario Engine builds on deterministic financial calculations.

28. Scenario and Goals

Scenario results may estimate goal impact.

Example:

Current monthly savings = ₹6,000

Scenario:
Save additional ₹3,000/month

Projected savings = ₹9,000/month

The Goal Engine can then estimate:

Earlier completion

or:

Reduced time to target
29. Scenario and Budgets

A scenario may simulate changes to spending behaviour without modifying the actual budget.

Example:

Current Food Budget = ₹6,000

Scenario:
Reduce Food spending by 20%

Projected Food spending = ₹4,800

The actual budget remains:

₹6,000
30. Scenario and Insights

Scenario results should not automatically become permanent behavioural insights.

For example:

Scenario:
Reduce shopping by 20%

Result:
Potential savings = ₹2,000

This is hypothetical.

It should not generate:

SAVINGS_OPPORTUNITY

as an actual behavioural insight unless the system separately identifies real evidence.

31. Scenario and AI

The AI Advisor may use the Scenario Engine.

Preferred architecture:

User Question
      ↓
Advisor
      ↓
Scenario Intent
      ↓
Scenario Engine
      ↓
Structured Result
      ↓
AI Explanation

The AI should not perform financial arithmetic independently when the Scenario Engine can provide deterministic results.

32. AI Scenario Example

User asks:

What if I cut shopping by 15%?

Flow:

User
 ↓
Advisor API
 ↓
Interpret Intent
 ↓
Scenario Engine
 ↓
Projected Result
 ↓
AI Explanation
33. Scenario Explainability

Every scenario result should be explainable through:

Assumptions
+
Baseline
+
Calculation
+
Projected Result

Example:

Baseline Food Spending:
₹6,000

Assumption:
20% reduction

Projected Spending:
₹4,800

Potential Savings:
₹1,200/month
34. Scenario Limitations

The Scenario Engine should clearly distinguish:

Projection

from:

Prediction

A scenario does not guarantee future financial outcomes.

It calculates what would happen under the defined assumptions.

35. Scenario Error Codes

Initial scenario-specific errors:

INVALID_SCENARIO
INVALID_SCENARIO_TYPE
INVALID_SCENARIO_PARAMETERS
INVALID_CATEGORY
INVALID_GOAL
SCENARIO_EXECUTION_FAILED
SCENARIO_NOT_FOUND
36. Scenario Performance

The Scenario Engine should retrieve only the financial context necessary for the requested simulation.

Example:

Food reduction scenario

does not necessarily require:

All historical transactions

if the required category metrics are already available.

37. Scenario Security

The API must prevent:

Cross-user goal access
Cross-user budget access
Cross-user financial context access
Arbitrary simulation expressions
Unauthorized financial mutations
38. Scenario Architecture
                    SCENARIO API
                         │
                         ▼
                  Scenario Service
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
          Analytics    Goals     Budgets
              │          │          │
              └──────────┼──────────┘
                         ▼
                  Scenario Engine
                         │
                         ▼
                 Projected Results
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
          Scenario API          AI Advisor
39. Scenario Endpoint Summary
POST /api/v1/scenarios
    → Create and execute scenario

GET /api/v1/scenarios
    → List scenario history

GET /api/v1/scenarios/:id
    → Retrieve scenario

DELETE /api/v1/scenarios/:id
    → Delete scenario history
40. Final Principle

The Scenario Engine provides:

"What if?" intelligence.

It allows WealthWise to move beyond describing the user's financial past and help them explore possible financial decisions.

What happened?
      ↓
Analytics

Why did it happen?
      ↓
Behaviour Intelligence

What could happen if I change something?
      ↓
Scenario Engine

What should I consider doing?
      ↓
AI Advisor