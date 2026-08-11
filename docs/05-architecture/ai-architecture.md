2.2 Versioned

All production endpoints shall use an explicit API version.

/api/v1

This allows future API evolution without unnecessarily breaking existing clients.

2.3 Authenticated by Default

Financial endpoints shall require authentication unless explicitly documented as public.

2.4 User Scoped

Every protected resource shall be scoped to the authenticated user.

The client should not be trusted to specify arbitrary ownership.

2.5 JSON First

Normal request and response bodies shall use JSON.

File uploads are an exception and may use:

multipart/form-data
2.6 Deterministic Financial Data

Numerical financial values returned by the API should originate from deterministic backend calculations.

AI-generated text shall not replace authoritative financial calculations.

3. Base URL

Development:

http://localhost:<PORT>/api/v1

Production:

https://<domain>/api/v1

The production domain shall be configured through deployment configuration.

4. HTTP Methods
Method	Purpose
GET	Retrieve resource
POST	Create resource / execute supported operation
PUT	Replace resource
PATCH	Partially update resource
DELETE	Delete resource

The implementation should use the simplest appropriate method for each operation.

5. Standard HTTP Status Codes
Status	Meaning
200	Successful request
201	Resource created
204	Successful request with no response body
400	Invalid request
401	Authentication required / invalid authentication
403	Authenticated but not authorized
404	Resource not found
409	Conflict
422	Validation failure
429	Rate limit exceeded
500	Internal server error
502	External service failure
503	Service temporarily unavailable
6. Standard Response Structure

Successful responses should follow a consistent structure.

Example:

{
  "success": true,
  "data": {
    "id": "..."
  }
}

For collections:

{
  "success": true,
  "data": [],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 120,
    "totalPages": 6
  }
}
7. Standard Error Structure

Errors shall use a consistent structure.

Example:

{
  "success": false,
  "error": {
    "code": "INVALID_TRANSACTION",
    "message": "The transaction amount must be greater than zero.",
    "details": []
  }
}

The API shall not expose:

stack traces,
database credentials,
internal file paths,
secret keys,
sensitive implementation details.
8. Error Code Convention

Error codes should use uppercase snake case.

Examples:

INVALID_REQUEST
UNAUTHORIZED
FORBIDDEN
RESOURCE_NOT_FOUND
INVALID_TRANSACTION
INVALID_GOAL
INVALID_BUDGET
IMPORT_FAILED
AI_SERVICE_UNAVAILABLE
SCENARIO_INVALID
INSUFFICIENT_DATA
9. Authentication

Authentication endpoints:

POST /auth/register
POST /auth/login
POST /auth/logout
GET  /auth/me
10. Register
POST /api/v1/auth/register
Request
{
  "name": "Ricky",
  "email": "user@example.com",
  "password": "********"
}
Response
{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "name": "Ricky",
      "email": "user@example.com"
    }
  }
}

The API shall never return the password or password hash.

11. Login
POST /api/v1/auth/login
Request
{
  "email": "user@example.com",
  "password": "********"
}
Response

The exact authentication mechanism will be finalized during Security Architecture.

Conceptually:

{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "name": "Ricky",
      "email": "user@example.com"
    },
    "token": "..."
  }
}

If an HTTP-only cookie strategy is selected, the token should not be returned in the JSON body.

12. Logout
POST /api/v1/auth/logout
Response
{
  "success": true,
  "data": null
}
13. Current User
GET /api/v1/auth/me

Returns the currently authenticated user's profile.

14. Authentication Middleware

Protected requests shall pass through authentication middleware.

Conceptually:

Request
   ↓
Authentication Middleware
   ↓
Authenticated User
   ↓
Controller

The middleware should attach the authenticated identity to the request context.

15. Authorization

Authorization shall be based on the authenticated user.

For example:

GET /transactions/:id
        ↓
Authenticate User
        ↓
Find transaction
        ↓
Verify transaction.userId
        ↓
Return / reject

The client must not be able to bypass ownership checks by supplying another user ID.

16. User Profile API
GET   /users/me
PATCH /users/me
Update Example
{
  "name": "Ricky",
  "currency": "INR",
  "timezone": "Asia/Kolkata"
}

Sensitive authentication fields should be updated through dedicated authentication endpoints where appropriate.

17. Transaction API

The transaction resource is:

/transactions

Endpoints:

GET    /transactions
POST   /transactions
GET    /transactions/:id
PATCH  /transactions/:id
DELETE /transactions/:id
18. Create Transaction
POST /api/v1/transactions
Request
{
  "amount": 1250.50,
  "currency": "INR",
  "type": "expense",
  "date": "2026-08-11",
  "description": "Swiggy Order",
  "merchant": "Swiggy",
  "category": "Food"
}
Response
{
  "success": true,
  "data": {
    "transaction": {
      "id": "...",
      "amount": 1250.50,
      "currency": "INR",
      "type": "expense",
      "date": "2026-08-11",
      "description": "Swiggy Order",
      "merchant": "Swiggy",
      "category": "Food"
    }
  }
}
19. List Transactions
GET /api/v1/transactions

Supported query parameters:

page
limit
startDate
endDate
category
type
merchant
minAmount
maxAmount
search
sortBy
sortOrder

Example:

GET /api/v1/transactions?page=1&limit=20&category=Food
20. Transaction Pagination

Default:

page = 1
limit = 20

The API shall enforce a maximum page size.

Example:

limit ≤ 100

The exact maximum may be adjusted during performance testing.

21. Transaction Filtering

Example:

GET /api/v1/transactions?type=expense&category=Food

Date filtering:

GET /api/v1/transactions?startDate=2026-08-01&endDate=2026-08-31
22. Transaction Search

Example:

GET /api/v1/transactions?search=swiggy

Search behaviour should be documented and consistent.

23. Transaction Detail
GET /api/v1/transactions/:id

Returns a single transaction owned by the authenticated user.

24. Update Transaction
PATCH /api/v1/transactions/:id

Example:

{
  "category": "Entertainment",
  "merchant": "Netflix"
}

Only permitted fields may be modified.

25. Delete Transaction
DELETE /api/v1/transactions/:id

Deleting a transaction shall trigger appropriate invalidation or recalculation of affected derived financial data.

26. Transaction Import API
POST /api/v1/transactions/import

Content type:

multipart/form-data

Example:

file = transactions.csv
27. Import Response

Example:

{
  "success": true,
  "data": {
    "importId": "...",
    "totalRecords": 1000,
    "imported": 950,
    "duplicates": 40,
    "errors": 10
  }
}
28. Import Status

If import processing becomes asynchronous:

GET /api/v1/imports/:id

Example response:

{
  "success": true,
  "data": {
    "id": "...",
    "status": "processing",
    "progress": 72
  }
}

This endpoint is optional for the initial synchronous MVP implementation.

29. Analytics API

Analytics endpoints:

GET /analytics/summary
GET /analytics/trends
GET /analytics/categories
GET /analytics/merchants
GET /analytics/savings
30. Financial Summary
GET /api/v1/analytics/summary

Optional query parameters:

startDate
endDate

Example response:

{
  "success": true,
  "data": {
    "income": 60000,
    "expenses": 42000,
    "savings": 18000,
    "savingsRate": 30,
    "period": {
      "start": "2026-08-01",
      "end": "2026-08-31"
    }
  }
}
31. Spending Categories
GET /api/v1/analytics/categories

Example:

{
  "success": true,
  "data": [
    {
      "category": "Housing",
      "amount": 15000,
      "percentage": 35.71
    },
    {
      "category": "Food",
      "amount": 6200,
      "percentage": 14.76
    }
  ]
}
32. Trends
GET /api/v1/analytics/trends

Example:

{
  "success": true,
  "data": [
    {
      "period": "2026-06",
      "income": 60000,
      "expenses": 39000,
      "savings": 21000
    },
    {
      "period": "2026-07",
      "income": 60000,
      "expenses": 41000,
      "savings": 19000
    }
  ]
}
33. Merchant Analytics
GET /api/v1/analytics/merchants

Optional parameters:

startDate
endDate
limit
34. Behaviour API

Behaviour endpoints:

GET /behaviour/signals
GET /behaviour/summary
35. Behaviour Signals
GET /api/v1/behaviour/signals

Example:

{
  "success": true,
  "data": [
    {
      "type": "SPENDING_INCREASE",
      "category": "Food",
      "currentValue": 6200,
      "baselineValue": 3900,
      "percentageChange": 58.97,
      "significance": "high"
    }
  ]
}
36. Behaviour Summary
GET /api/v1/behaviour/summary

This endpoint may provide a higher-level summary of current behavioural patterns.

37. Goals API

Endpoints:

GET    /goals
POST   /goals
GET    /goals/:id
PATCH  /goals/:id
DELETE /goals/:id
38. Create Goal
POST /api/v1/goals
Request
{
  "name": "Goa Trip",
  "targetAmount": 50000,
  "currentAmount": 18000,
  "targetDate": "2027-02-01"
}
39. Goal Response
{
  "success": true,
  "data": {
    "goal": {
      "id": "...",
      "name": "Goa Trip",
      "targetAmount": 50000,
      "currentAmount": 18000,
      "remainingAmount": 32000,
      "progressPercentage": 36,
      "targetDate": "2027-02-01",
      "status": "on_track"
    }
  }
}

Derived fields such as progress and remaining amount should be calculated by backend logic.

40. Goal Feasibility
GET /api/v1/goals/:id/feasibility

Example:

{
  "success": true,
  "data": {
    "requiredMonthlyContribution": 5333.33,
    "estimatedSavingsCapacity": 8000,
    "status": "feasible"
  }
}

The exact calculation methodology will be defined in the financial calculation specification.

41. Budgets API

Endpoints:

GET    /budgets
POST   /budgets
GET    /budgets/:id
PATCH  /budgets/:id
DELETE /budgets/:id
42. Create Budget
POST /api/v1/budgets

Example:

{
  "category": "Food",
  "amount": 8000,
  "period": {
    "type": "monthly",
    "startDate": "2026-08-01",
    "endDate": "2026-08-31"
  }
}
43. Budget Status
GET /api/v1/budgets/:id/status

Example:

{
  "success": true,
  "data": {
    "budget": 8000,
    "spent": 6200,
    "remaining": 1800,
    "usagePercentage": 77.5,
    "status": "within_budget"
  }
}
44. Insights API

Endpoints:

GET   /insights
GET   /insights/:id
PATCH /insights/:id
POST  /insights/:id/dismiss
45. List Insights
GET /api/v1/insights

Supported filters:

status
severity
type
startDate
endDate

Example:

GET /api/v1/insights?status=active&severity=high
46. Insight Detail
GET /api/v1/insights/:id

The response should include structured context where appropriate.

Example:

{
  "success": true,
  "data": {
    "insight": {
      "id": "...",
      "type": "spending_increase",
      "severity": "medium",
      "title": "Food spending increased",
      "summary": "Your food spending increased compared with your recent average.",
      "context": {
        "currentValue": 6200,
        "baselineValue": 3900,
        "percentageChange": 58.97
      }
    }
  }
}
47. Dismiss Insight
POST /api/v1/insights/:id/dismiss

Example response:

{
  "success": true,
  "data": {
    "status": "dismissed"
  }
}
48. Scenario API

Endpoints:

POST   /scenarios
GET    /scenarios
GET    /scenarios/:id
DELETE /scenarios/:id
POST   /scenarios/simulate

The exact persistence model is optional for MVP.

49. Run Scenario
POST /api/v1/scenarios/simulate

Example:

{
  "type": "category_reduction",
  "inputs": {
    "category": "Food",
    "changePercentage": -20
  }
}
50. Scenario Result

Example:

{
  "success": true,
  "data": {
    "baseline": {
      "monthlyExpenses": 42000,
      "monthlySavings": 18000
    },
    "scenario": {
      "monthlyExpenses": 40760,
      "monthlySavings": 19240
    },
    "impact": {
      "monthlySavingsDifference": 1240
    }
  }
}

The response shall clearly distinguish:

baseline

from:

scenario
51. Scenario Types

Initial supported scenario types:

category_reduction
category_increase
income_change
savings_change
purchase
goal_deadline_change

Not all types are required for the first MVP implementation.

52. Scenario Isolation

Scenario execution shall not mutate actual financial records.

The API should treat:

POST /scenarios/simulate

as a calculation operation rather than a transaction mutation.

53. Personal Money Model API

The Personal Money Model may be exposed through:

GET /api/v1/financial-context

Example:

{
  "success": true,
  "data": {
    "income": {
      "monthlyAverage": 60000
    },
    "expenses": {
      "monthlyAverage": 42000
    },
    "savings": {
      "monthlyAverage": 18000,
      "rate": 30
    },
    "topCategories": [],
    "goals": [],
    "budgets": []
  }
}

This endpoint should expose only information appropriate for the authenticated user.

54. AI Advisor API

Primary endpoint:

POST /api/v1/advisor/chat
55. AI Advisor Request

Example:

{
  "message": "Why did my savings fall this month?",
  "conversationId": "optional-id"
}

The backend determines relevant financial context.

The client should not submit an arbitrary "financial context" object and expect the backend to trust it.

56. AI Advisor Response

Example:

{
  "success": true,
  "data": {
    "message": "Your savings fell mainly because your food and entertainment spending increased compared with the previous month.",
    "context": {
      "metricsUsed": [
        "monthlySavings",
        "categorySpending",
        "historicalComparison"
      ]
    }
  }
}
57. AI Conversation History

If persistent conversations are enabled:

GET /api/v1/advisor/conversations
GET /api/v1/advisor/conversations/:id
DELETE /api/v1/advisor/conversations/:id

Conversation persistence may initially be optional.

58. AI Context Architecture

The API shall not directly pass unrestricted user data to the AI provider.

The flow is:

POST /advisor/chat
       ↓
Advisor Service
       ↓
Intent / Context Selection
       ↓
Analytics / Goals / Behaviour
       ↓
Structured Context
       ↓
Prompt Builder
       ↓
AI Provider
59. Dashboard API

The dashboard requires a consolidated view.

Possible endpoint:

GET /api/v1/dashboard

Example response:

{
  "success": true,
  "data": {
    "summary": {
      "income": 60000,
      "expenses": 42000,
      "savings": 18000,
      "savingsRate": 30
    },
    "topCategories": [],
    "goals": [],
    "budgets": [],
    "insights": []
  }
}
60. Dashboard API Philosophy

The dashboard endpoint may aggregate multiple services.

It should not create a new independent source of financial truth.

Conceptually:

Dashboard Service
      │
 ┌────┼────┬────┬────┐
 ↓    ↓    ↓    ↓    ↓
Analytics Goals Budgets Insights Behaviour
61. Settings API

Possible endpoints:

GET   /users/me
PATCH /users/me
PATCH /users/me/preferences

Example:

{
  "notifications": true,
  "insights": true
}
62. API Resource Summary
Resource	GET	POST	PATCH	DELETE
Auth	✓	✓	—	—
Transactions	✓	✓	✓	✓
Goals	✓	✓	✓	✓
Budgets	✓	✓	✓	✓
Insights	✓	—	✓	—
Scenarios	✓	✓	—	✓
Financial Context	✓	—	—	—
AI Advisor	—	✓	—	—
Dashboard	✓	—	—	—
63. Query Parameter Conventions

Dates:

startDate=YYYY-MM-DD
endDate=YYYY-MM-DD

Pagination:

page=1
limit=20

Sorting:

sortBy=date
sortOrder=desc

Filtering:

category=Food
type=expense

The API should use consistent parameter names across resources.

64. Pagination Response

All paginated collection endpoints should return metadata.

Example:

{
  "success": true,
  "data": [],
  "meta": {
    "page": 2,
    "limit": 20,
    "total": 100,
    "totalPages": 5,
    "hasNext": true,
    "hasPrevious": true
  }
}
65. Validation

Request validation shall happen before business logic executes.

Example:

POST /transactions
       ↓
Schema Validation
       ↓
Business Validation
       ↓
Transaction Service
66. Validation Error

Example:

{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The request contains invalid fields.",
    "details": [
      {
        "field": "amount",
        "message": "Amount must be greater than zero."
      }
    ]
  }
}
67. Resource Not Found

Example:

{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "The requested transaction was not found."
  }
}

The response should not reveal whether a resource exists for another user.

68. Authorization Failure

Example:

{
  "success": false,
  "error": {
    "code": "FORBIDDEN",
    "message": "You are not authorized to access this resource."
  }
}
69. Insufficient Data

Financial intelligence may legitimately lack sufficient history.

Example:

{
  "success": false,
  "error": {
    "code": "INSUFFICIENT_DATA",
    "message": "There is not enough financial history to generate a reliable comparison."
  }
}

This should not be treated as a system failure.

70. AI Service Error

If the AI provider is unavailable:

{
  "success": false,
  "error": {
    "code": "AI_SERVICE_UNAVAILABLE",
    "message": "The AI Advisor is temporarily unavailable. Your financial data and analytics remain available."
  }
}
71. Rate Limiting

Rate limiting should be applied more aggressively to expensive endpoints.

Priority:

Authentication
     ↓
AI Advisor
     ↓
File Import
     ↓
Complex Analytics

Normal read endpoints may use less restrictive limits.

72. API Authentication Header

If bearer-token authentication is selected:

Authorization: Bearer <token>

The final authentication strategy will be defined in Security Architecture.

73. Request Ownership

The client should never be trusted to determine resource ownership.

Bad:

{
  "userId": "another-user-id"
}

Correct:

Authenticated identity
        ↓
Backend determines userId

The API may ignore or reject client-provided ownership fields.

74. API and Financial Calculations

The API shall return deterministic financial values from backend services.

Example:

Analytics Service
      ↓
Savings = ₹18,000
      ↓
API
      ↓
Frontend

Not:

Frontend
      ↓
Calculate savings
75. API and AI Boundary

The API should expose the AI Advisor as an application capability.

It should not expose internal AI provider credentials or implementation details.

Correct:

POST /advisor/chat

Incorrect:

POST /openai/request

The frontend should know about WealthWise's AI capability, not its underlying provider implementation.

76. API and Scenario Boundary

The scenario endpoint should clearly communicate that the result is hypothetical.

Example:

{
  "data": {
    "isHypothetical": true,
    "baseline": {},
    "scenario": {},
    "impact": {}
  }
}

This prevents accidental interpretation of scenario values as actual financial records.

77. API Security Boundaries

Every protected request should conceptually follow:

HTTP Request
      ↓
CORS / Transport Security
      ↓
Rate Limiting
      ↓
Authentication
      ↓
Authorization
      ↓
Validation
      ↓
Controller
      ↓
Service
78. API Logging

The API should log operational metadata such as:

request ID
HTTP method
route
status
latency
error code

It should not unnecessarily log:

passwords
tokens
full transaction history
AI financial context
sensitive user data
79. Request IDs

The API should support a request identifier for troubleshooting.

Example:

X-Request-ID: 8c91f...

The same identifier may be included in error responses or logs where appropriate.

80. API Idempotency

Operations that may be retried should be designed carefully to avoid duplicate financial records.

This is particularly important for:

Transaction creation
Transaction import

A future idempotency mechanism may be introduced for operations where duplicate execution is possible.

81. Import Idempotency

Imported files should use sufficient metadata to reduce accidental repeated imports.

Potential mechanisms include:

file hash
import ID
transaction signature
source metadata

The exact strategy will be defined in the Import Architecture.

82. API Performance

The API should follow these targets from the NFR document:

Normal API P50 ≤ 300 ms
Normal API P95 ≤ 1000 ms

Exceptions:

AI requests
Large imports
Complex analytics

These targets must be validated through testing.

83. API Caching

Caching may be introduced for relatively stable derived information.

Potential candidates:

Dashboard summary
Category analytics
Financial context

However, cached financial information must be invalidated when underlying transactions change.

84. API Cache Invalidation

Conceptually:

Transaction Created
       ↓
Invalidate affected analytics
       ↓
Invalidate financial context
       ↓
Recalculate when required

The API should never knowingly serve stale authoritative financial data.

85. API Versioning Strategy

Current:

/api/v1

Future breaking changes may introduce:

/api/v2

Non-breaking additions should generally remain within the current version.

86. API Documentation

The final implementation should generate or maintain machine-readable API documentation.

OpenAPI/Swagger may be used during implementation.

Potential future file:

docs/03-architecture/openapi.yaml

The API contract should remain synchronized with the implementation.

87. Frontend API Client

The React frontend should use a centralized API client rather than constructing HTTP requests throughout components.

Example:

frontend/src/services/api/
│
├── client.ts
├── auth.api.ts
├── transactions.api.ts
├── analytics.api.ts
├── goals.api.ts
├── budgets.api.ts
├── insights.api.ts
├── scenarios.api.ts
└── advisor.api.ts
88. Frontend Communication Flow
React Component
      ↓
Feature Hook
      ↓
API Client
      ↓
HTTP Request
      ↓
WealthWise API

The component should not directly manage:

authentication headers,
raw HTTP configuration,
API base URLs.
89. API State Handling

The frontend should distinguish between:

loading
success
empty
validation error
authorization error
server error
AI unavailable

This is especially important for financial analytics where "no data" and "system failure" are different states.

90. Empty Data Response

An empty collection should generally be a successful response:

{
  "success": true,
  "data": [],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 0,
    "totalPages": 0
  }
}

This is different from:

500 Internal Server Error
91. Dashboard Aggregation

The dashboard endpoint may aggregate multiple backend services.

Conceptually:

GET /dashboard
       ↓
Dashboard Service
       │
       ├── Analytics
       ├── Goals
       ├── Budgets
       ├── Insights
       └── Behaviour
       ↓
Unified Response

This reduces frontend request complexity.

92. API Dependency Graph
                    API
                     │
        ┌────────────┼─────────────┐
        ▼            ▼             ▼
   Transactions   Analytics      Auth
        │            │
        │            ▼
        │        Behaviour
        │            │
        └──────┬─────┘
               ▼
             Goals
               │
             Budgets
               │
        ┌──────┴──────┐
        ▼             ▼
    Insights       Scenarios
        │             │
        └──────┬──────┘
               ▼
          AI Advisor

This is a logical dependency graph rather than a requirement that every API endpoint directly call every other module.

93. API Anti-Patterns

The following should be avoided.

93.1 Business Logic in Controllers

Controllers should remain thin.

93.2 Frontend Financial Calculations

The frontend should not be the authoritative financial calculator.

93.3 Direct Database Access from Routes

Routes should not directly execute database queries.

93.4 AI Provider Exposure

The frontend should not directly call the AI provider.

93.5 Unscoped Resources

Every protected financial resource must be user-scoped.

93.6 Inconsistent Responses

Endpoints should not randomly return unrelated response structures.

93.7 Silent Errors

The API should provide meaningful error codes and messages.

94. API Security Checklist

Before implementation is considered complete:

[ ] Authentication implemented
[ ] Authorization implemented
[ ] User ownership enforced
[ ] Input validation implemented
[ ] Rate limiting implemented where needed
[ ] HTTPS enabled in production
[ ] Secrets excluded from source code
[ ] Sensitive data excluded from logs
[ ] File uploads validated
[ ] AI provider isolated behind backend
[ ] Error responses sanitized
95. API Testing Strategy

API tests should cover:

Authentication
Register
Login
Logout
Unauthorized access
Transactions
Create
Read
Update
Delete
Filtering
Pagination
Ownership
Analytics
Summary
Categories
Trends
Edge cases
Goals
Create
Update
Delete
Progress
Feasibility
Budgets
Create
Update
Delete
Usage
Status
Scenarios
Valid scenario
Invalid scenario
Baseline calculation
Goal impact
Isolation
AI
Valid question
Insufficient context
AI failure
Unauthorized request
96. API Contract Evolution

When changing an endpoint:

determine whether the change is breaking,
update API documentation,
update frontend client,
update tests,
update dependent services,
update version if necessary.

API changes should not be made silently.

97. Initial API Endpoint Map
/api/v1
│
├── /auth
│   ├── POST /register
│   ├── POST /login
│   ├── POST /logout
│   └── GET  /me
│
├── /users
│   └── /me
│
├── /transactions
│   ├── GET
│   ├── POST
│   ├── GET /:id
│   ├── PATCH /:id
│   ├── DELETE /:id
│   └── POST /import
│
├── /analytics
│   ├── /summary
│   ├── /trends
│   ├── /categories
│   ├── /merchants
│   └── /savings
│
├── /behaviour
│   ├── /signals
│   └── /summary
│
├── /goals
│   ├── GET
│   ├── POST
│   ├── GET /:id
│   ├── PATCH /:id
│   ├── DELETE /:id
│   └── GET /:id/feasibility
│
├── /budgets
│   ├── GET
│   ├── POST
│   ├── GET /:id
│   ├── PATCH /:id
│   ├── DELETE /:id
│   └── GET /:id/status
│
├── /insights
│   ├── GET
│   ├── GET /:id
│   ├── PATCH /:id
│   └── POST /:id/dismiss
│
├── /scenarios
│   ├── POST /simulate
│   ├── GET
│   ├── GET /:id
│   └── DELETE /:id
│
├── /financial-context
│
├── /dashboard
│
└── /advisor
    ├── POST /chat
    └── /conversations
98. Core API Flow

The API architecture ultimately supports:

                     USER
                       │
                       ▼
                 REACT FRONTEND
                       │
                       ▼
                  REST API
                       │
       ┌───────────────┼────────────────┐
       │               │                │
       ▼               ▼                ▼
 Transactions      Analytics          Goals
       │               │                │
       └───────────────┼────────────────┘
                       ▼
                 Intelligence
                       │
              ┌────────┴────────┐
              ▼                 ▼
          Insights          Scenarios
              │                 │
              └────────┬────────┘
                       ▼
                  AI Advisor
                       │
                       ▼
                  AI Provider
99. Core Architectural Principle

The WealthWise API should expose financial capabilities, not implementation details.

The frontend should be able to ask:

"Give me my financial summary."

rather than:

"Run this MongoDB aggregation."

The frontend should be able to ask:

"Simulate reducing Food spending by 20%."

rather than:

"Modify these transaction values and calculate the difference."

The frontend should be able to ask:

"Explain this insight."

rather than directly interacting with the LLM provider.

100. Status

This document defines the initial API architecture for WealthWise.

The API contract will be refined during:

backend implementation,
frontend implementation,
security architecture,
AI architecture,
database implementation,
integration testing.

The next architecture documents should define:

AI Architecture
Data Flow Architecture
Security Architecture
Architecture Decision Records

The API specification should eventually be converted into an OpenAPI specification once the endpoint contracts are finalized.