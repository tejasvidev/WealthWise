
---

# FILE 2 — `docs/07-api/transaction-api.md`

```markdown
# WealthWise — Transaction API

**Document Version:** 1.0  
**Status:** API Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**API Style:** REST  
**API Version:** v1  

---

# 1. Purpose

This document defines the API contract for transaction management in WealthWise.

It covers:

- transaction creation,
- retrieval,
- updates,
- deletion,
- filtering,
- sorting,
- pagination,
- transaction import,
- validation,
- ownership,
- duplicate handling,
- and financial-data integrity.

Transactions are the primary source records from which much of WealthWise's financial intelligence is derived.

---

# 2. Transaction API Endpoints

Initial transaction endpoints:

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/v1/transactions` | List transactions |
| POST | `/api/v1/transactions` | Create transaction |
| GET | `/api/v1/transactions/:id` | Get transaction |
| PATCH | `/api/v1/transactions/:id` | Update transaction |
| DELETE | `/api/v1/transactions/:id` | Delete transaction |
| POST | `/api/v1/transactions/import` | Import transactions |

---

# 3. Authentication

All transaction endpoints require authentication.

```text
Request
   ↓
Authentication
   ↓
Authenticated User
   ↓
Transaction API

The user's identity comes from the authentication layer.

The client must not provide ownership as an authoritative value.

4. List Transactions
Endpoint
GET /api/v1/transactions
Purpose

Returns a paginated list of transactions belonging to the authenticated user.

5. Basic Request
GET /api/v1/transactions

Default behaviour:

Newest transactions first
Controlled page size
Authenticated user only
6. Pagination

Supported query parameters:

page
limit

Example:

GET /api/v1/transactions?page=1&limit=20

Conceptual response:

{
  "success": true,
  "data": [],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 120,
    "totalPages": 6
  }
}

The backend must enforce a maximum limit.

7. Date Filtering

Transactions may be filtered using:

startDate
endDate

Example:

GET /api/v1/transactions?startDate=2026-08-01&endDate=2026-08-31

The backend should use a consistent half-open date range internally:

date >= startDate
AND
date < endDate

where appropriate.

8. Category Filtering

Example:

GET /api/v1/transactions?category=Food

Multiple categories may be supported later.

The category must belong to the controlled WealthWise category vocabulary.

9. Type Filtering

Example:

GET /api/v1/transactions?type=EXPENSE

Supported initial types:

INCOME
EXPENSE
TRANSFER
REFUND
10. Merchant Filtering

Example:

GET /api/v1/transactions?merchant=Amazon

The exact matching strategy may be:

exact
normalized
partial

depending on implementation.

11. Search

Example:

GET /api/v1/transactions?search=amazon

Search may inspect approved fields such as:

merchant
description

The API must not allow arbitrary database query expressions.

12. Sorting

Example:

GET /api/v1/transactions?sort=date&order=desc

Initial sortable fields may include:

date
amount
merchant
category

The backend must whitelist allowed sorting fields.

13. Combined Filtering

Example:

GET /api/v1/transactions
    ?startDate=2026-08-01
    &endDate=2026-08-31
    &category=Food
    &type=EXPENSE
    &sort=date
    &order=desc

The backend combines these filters with the authenticated user's ownership constraint.

14. Transaction List Response

Conceptual response:

{
  "success": true,
  "data": [
    {
      "id": "transaction-id",
      "date": "2026-08-10",
      "description": "Dinner",
      "merchant": "Restaurant",
      "amount": 850,
      "type": "EXPENSE",
      "category": "Food"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1,
    "totalPages": 1
  }
}

Internal database fields should not be exposed unnecessarily.

15. Get Single Transaction
Endpoint
GET /api/v1/transactions/:id

Example:

GET /api/v1/transactions/66abc123

The API must verify that the transaction belongs to the authenticated user.

16. Successful Transaction Retrieval
{
  "success": true,
  "data": {
    "id": "66abc123",
    "date": "2026-08-10",
    "description": "Dinner",
    "merchant": "Restaurant",
    "amount": 850,
    "type": "EXPENSE",
    "category": "Food",
    "source": "MANUAL"
  }
}
17. Transaction Not Found

If the transaction does not exist or does not belong to the authenticated user:

HTTP 404 Not Found

Conceptual response:

{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Transaction not found."
  }
}

The API should avoid revealing whether an inaccessible transaction exists.

18. Create Transaction
Endpoint
POST /api/v1/transactions
Purpose

Creates a new financial transaction.

19. Create Request

Example:

{
  "date": "2026-08-10",
  "description": "Dinner",
  "merchant": "Restaurant",
  "amount": 850,
  "type": "EXPENSE",
  "category": "Food",
  "notes": "Dinner with friends"
}
20. Create Request Fields
Field	Required	Description
date	Yes	Financial event date
description	Yes	Transaction description
merchant	No	Merchant/payee
amount	Yes	Monetary value
type	Yes	Transaction type
category	Yes	Financial category
notes	No	User notes

userId must not be accepted as the ownership source.

21. Create Validation

The API validates:

date
description
amount
type
category

Validation includes:

required values,
valid data types,
valid enum values,
monetary precision,
business constraints.
22. Amount Convention

The transaction model should use a consistent amount convention.

The recommended MVP convention is:

amount = positive monetary magnitude
type = determines financial direction

Therefore:

INCOME + ₹50,000
EXPENSE + ₹2,000

rather than encoding direction through negative numbers.

The financial services layer determines the effect of the transaction.

23. Transaction Type Semantics
INCOME
→ increases income

EXPENSE
→ increases expenses

TRANSFER
→ moves money without representing income/expense

REFUND
→ reverses or offsets an expense according to business rules

Exact refund treatment will be defined in the financial business rules.

24. Category Validation

The category must belong to the approved taxonomy.

Initial values:

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
25. Source

For manually created transactions:

source = MANUAL

The client should not be able to falsely mark a manually entered transaction as:

CSV_IMPORT
SYSTEM

The backend determines source based on the workflow.

26. Classification

A newly created transaction may be classified through:

Rule-based classification
User-selected category
AI-assisted classification

The final category remains a structured transaction field.

AI classification does not become an independent financial source of truth.

27. Create Response

Successful creation:

HTTP 201 Created

Conceptual response:

{
  "success": true,
  "data": {
    "id": "transaction-id",
    "date": "2026-08-10",
    "description": "Dinner",
    "merchant": "Restaurant",
    "amount": 850,
    "type": "EXPENSE",
    "category": "Food",
    "source": "MANUAL"
  }
}
28. Transaction Update
Endpoint
PATCH /api/v1/transactions/:id

Partial updates are preferred.

29. Editable Fields

The initial editable fields may include:

description
merchant
amount
date
type
category
notes

However, changes to financial fields must trigger the appropriate recalculation or invalidation of derived information.

30. Example Update
{
  "category": "Entertainment",
  "notes": "Updated category"
}

The backend updates only the permitted fields.

31. Financial Mutation Rule

Changing:

amount
date
type
category

may affect:

Analytics
Budgets
Goals
Behaviour Signals
Insights
Financial Context

Therefore, the update workflow must account for derived-data refresh.

32. Update Flow
PATCH Transaction
       ↓
Authenticate
       ↓
Verify Ownership
       ↓
Validate Changes
       ↓
Update Transaction
       ↓
Invalidate / Refresh Derived Data
       ↓
Return Updated Transaction
33. Delete Transaction
Endpoint
DELETE /api/v1/transactions/:id

The API verifies ownership before deletion.

34. Delete Response

Possible response:

HTTP 204 No Content

The final deletion and retention strategy will follow the database and business rules.

35. Derived Data After Deletion

Deleting a transaction may affect:

Monthly totals
Category totals
Savings
Budget usage
Goal analysis
Behaviour signals
Insights
Financial Context

The application must define how these derived representations are refreshed.

36. CSV Import
Endpoint
POST /api/v1/transactions/import

This is a multipart/form-data endpoint.

Conceptually:

Content-Type: multipart/form-data

with a CSV file.

37. Import Workflow
CSV Upload
    ↓
File Validation
    ↓
CSV Parsing
    ↓
Column Mapping
    ↓
Row Normalization
    ↓
Row Validation
    ↓
Duplicate Detection
    ↓
Persistence
    ↓
Derived Data Refresh
    ↓
Import Summary
38. Import Validation

The importer validates:

Date
Amount
Description
Transaction Type
Category
Required columns
File format
File size

Invalid rows should be reported rather than silently discarded.

39. Import Result

Conceptual response:

{
  "success": true,
  "data": {
    "totalRows": 100,
    "imported": 87,
    "duplicates": 8,
    "rejected": 5,
    "errors": []
  }
}
40. Import Errors

A rejected row may contain:

{
  "row": 24,
  "code": "INVALID_AMOUNT",
  "message": "Amount must be a valid monetary value."
}

The API should provide enough information for the user to correct the source file.

41. Duplicate Detection

Imported transactions should be checked for potential duplicates.

A conceptual duplicate signature may consider:

userId
date
amount
merchant
description

The exact duplicate algorithm will be finalized during implementation.

42. Duplicate Handling

The system should distinguish:

Confirmed duplicate
Potential duplicate
Valid new transaction

The MVP may use a deterministic matching strategy.

Fuzzy duplicate detection may be introduced later.

43. Import Source

All successfully imported transactions should be marked:

source = CSV_IMPORT

Import metadata may contain:

sourceFile
rowIdentifier
importedAt
44. Import Ownership

All imported transactions belong to the authenticated user initiating the import.

The CSV must not be able to specify an arbitrary userId.

45. Transaction Query Authorization

Every transaction query follows:

Authenticated User
        ↓
authenticatedUserId
        ↓
Transaction Repository
        ↓
{
    userId: authenticatedUserId
}
46. Transaction API Security

The API must protect against:

Cross-user access
Mass data extraction
Invalid transaction mutation
Malicious file uploads
Oversized imports
Injection attempts
Unauthorized deletion
47. Pagination Security

The API must enforce:

maximum limit
valid page numbers
controlled sorting fields
controlled filtering fields

The client must not be able to request arbitrary MongoDB expressions.

48. File Upload Security

CSV imports should validate:

File type
File size
File structure
Row count
Encoding

The uploaded file should be processed as data.

It must never be treated as executable content.

49. Transaction Error Codes

Initial transaction-specific error codes:

INVALID_TRANSACTION
INVALID_AMOUNT
INVALID_DATE
INVALID_TRANSACTION_TYPE
INVALID_CATEGORY
TRANSACTION_NOT_FOUND
TRANSACTION_DUPLICATE
IMPORT_INVALID_FILE
IMPORT_VALIDATION_ERROR
IMPORT_TOO_LARGE
50. Transaction API and Analytics

Transactions are authoritative source data.

The transaction API does not directly calculate every analytics result.

Instead:

Transaction API
      ↓
Transaction Service
      ↓
Transaction Repository
      ↓
MongoDB
      ↓
Analytics Services

Analytics remain a separate domain.

51. Transaction API and Budget

A transaction mutation may affect budget calculations.

However, the Transaction API should not directly implement budget logic.

Instead:

Transaction
      ↓
Financial State Change
      ↓
Budget Service
      ↓
Updated Budget Evaluation
52. Transaction API and Goals

Similarly, transaction changes may affect goal feasibility.

The transaction service should trigger or invalidate the relevant derived calculations rather than embedding Goal logic inside the transaction controller.

53. Transaction API and Insights

A transaction may contribute to an insight.

For example:

New large expense
      ↓
Behaviour Analysis
      ↓
Potential anomaly
      ↓
Insight Engine

The Transaction API itself should not generate the final natural-language insight.

54. Transaction API and AI

The AI Advisor may use transaction-derived context.

However:

AI

must not directly modify transaction records.

The API boundary remains:

Transaction API
→ Financial Truth

Advisor API
→ Explanation and Guidance
55. Transaction Response Principle

The API should return structured financial information.

The frontend should not need to infer:

transaction type
category
financial effect

from free-form text.

56. Transaction Data Example

A complete transaction response may conceptually be:

{
  "id": "66abc123",
  "date": "2026-08-10",
  "description": "Dinner",
  "merchant": "Restaurant",
  "amount": 850,
  "type": "EXPENSE",
  "category": "Food",
  "source": "MANUAL",
  "notes": "Dinner with friends",
  "createdAt": "2026-08-10T20:30:00Z",
  "updatedAt": "2026-08-10T20:30:00Z"
}
57. Query Contract Summary
GET /transactions
    → List with pagination and filters

GET /transactions/:id
    → Retrieve one

POST /transactions
    → Create one

PATCH /transactions/:id
    → Update one

DELETE /transactions/:id
    → Delete one

POST /transactions/import
    → Import CSV
58. Transaction Lifecycle
             CREATE
                │
                ▼
           VALIDATE
                │
                ▼
            PERSIST
                │
                ▼
        FINANCIAL STATE
             CHANGE
                │
       ┌────────┼─────────┐
       ▼        ▼         ▼
   Analytics  Budgets   Behaviour
       │        │         │
       └────────┼─────────┘
                ▼
             Insights
                │
                ▼
        Financial Context
                │
                ▼
             AI Layer
59. Transaction Integrity Principle

The fundamental rule is:

A transaction represents an actual financial event.

It must therefore remain separate from:

Scenario
Insight
AI conversation
Recommendation

These are derived or interpretive layers.
