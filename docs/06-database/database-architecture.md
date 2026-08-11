# WealthWise — Database Architecture

**Document Version:** 1.0  
**Status:** Architecture Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Technology:** MongoDB + Mongoose  
**Database Type:** Document-Oriented NoSQL Database  
**Architecture Style:** Modular Monolith  

---

# 1. Purpose

This document defines the database architecture of WealthWise.

It establishes:

- database technology,
- collection structure,
- data ownership,
- entity relationships,
- document schemas,
- embedded and referenced data,
- indexing strategy,
- validation rules,
- financial data integrity,
- temporal data handling,
- AI-related persistence,
- privacy considerations,
- and database access boundaries.

The database architecture must support the central WealthWise principle:

> **Financial data remains structured, deterministic, and authoritative. AI-generated content must never become the source of truth for financial calculations.**

---

# 2. Database Technology

WealthWise will use:

> **MongoDB**

with:

> **Mongoose**

as the Object Data Modeling (ODM) layer for the Node.js backend.

MongoDB is appropriate for WealthWise because the application contains both structured financial records and evolving contextual information.

The database must nevertheless maintain strict schema expectations for critical financial entities.

---

# 3. Database Philosophy

The database should follow five principles.

## 3.1 Financial Data Is Authoritative

Transactions, goals, budgets, and calculated financial state must remain structured.

AI-generated explanations must not replace these records.

---

## 3.2 User Data Is Isolated

Financial records must always belong to a specific authenticated user.

Every user-owned financial document must contain a reliable ownership reference.

---

## 3.3 Read Patterns Influence Schema Design

Collections and indexes should be designed around actual application access patterns rather than purely theoretical normalization.

---

## 3.4 Derived Data Must Be Identifiable

Calculated or generated information should be distinguishable from original financial records.

For example:

- transaction = source data,
- analytics = derived calculation,
- behaviour signal = derived interpretation,
- insight = generated intelligence,
- AI explanation = generated language.

---

## 3.5 Sensitive Data Must Be Minimized

Only data necessary for WealthWise functionality should be persisted.

Sensitive financial information should not be unnecessarily duplicated across collections.

---

# 4. Database Collections

The initial WealthWise database will contain the following major collections:

```text
MongoDB
│
├── users
├── transactions
├── goals
├── budgets
├── insights
├── scenarios
├── financialContexts
└── aiConversations

Additional collections may be introduced only when a clear architectural requirement exists.

5. Entity Overview
Entity	Purpose	Primary Owner
User	Identity and account information	Auth
Transaction	Raw financial record	Transactions
Goal	Financial objective	Goals
Budget	Spending constraint/plan	Budgets
Insight	Meaningful financial observation	Insights
Scenario	Hypothetical financial simulation	Scenarios
FinancialContext	Aggregated financial state	Money Model
AIConversation	Conversational AI history	Advisor
6. High-Level Relationship Model

The conceptual relationship is:

                    User
                     │
       ┌─────────────┼──────────────┐
       │             │              │
       ▼             ▼              ▼
 Transactions      Goals         Budgets
       │             │              │
       └──────┬──────┴──────────────┘
              │
              ▼
       Financial Context
              │
       ┌──────┴─────────┐
       ▼                ▼
 Behaviour          Insights
       │                │
       └──────┬─────────┘
              ▼
          AI Advisor
              │
              ▼
       AI Conversations

User
 │
 └──────────────→ Scenarios
7. User Collection

The users collection stores identity and account-level information.

7.1 Purpose

The User entity is responsible for:

authentication,
identity,
account settings,
preferences,
currency preference,
timestamps.

Financial records should reference the user rather than duplicate user information.

7.2 Conceptual Schema
{
  _id: ObjectId,

  name: String,

  email: String,

  passwordHash: String,

  currency: String,

  preferences: {
    timezone: String,
    dateFormat: String
  },

  createdAt: Date,

  updatedAt: Date
}
7.3 User Rules
Email must be unique.
Password must never be stored in plaintext.
Authentication secrets must never be returned through normal API responses.
Financial data must not be embedded directly inside the User document.
User deletion must define a clear policy for associated financial records.
8. Transaction Collection

Transactions are the primary financial source records of WealthWise.

8.1 Purpose

The Transaction entity represents an individual financial event.

Examples:

salary,
food purchase,
rent,
subscription,
UPI payment,
transfer,
refund,
bill payment.
8.2 Conceptual Schema
{
  _id: ObjectId,

  userId: ObjectId,

  date: Date,

  description: String,

  merchant: String,

  amount: Number,

  type: String,

  category: String,

  source: String,

  account: String,

  notes: String,

  importMetadata: {
    sourceFile: String,
    rowIdentifier: String,
    importedAt: Date
  },

  classification: {
    method: String,
    confidence: Number
  },

  createdAt: Date,

  updatedAt: Date
}
9. Transaction Type

The initial supported transaction types are:

INCOME
EXPENSE
TRANSFER
REFUND

The transaction type must be explicit.

It should not be inferred repeatedly by downstream modules.

10. Transaction Categories

The initial category taxonomy includes:

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

The taxonomy may evolve as the product develops.

Category definitions should be maintained centrally rather than duplicated across services.

11. Transaction Source

Transactions may originate from different sources.

Examples:

MANUAL
CSV_IMPORT
SYSTEM

Future sources may include:

BANK_IMPORT
API

but these should not be implemented until explicitly required.

12. Transaction Classification

Classification metadata should distinguish between deterministic and AI-assisted classification.

Example:

{
  classification: {
    method: "RULE",
    confidence: 1.0
  }
}

or:

{
  classification: {
    method: "AI",
    confidence: 0.91
  }
}

The original transaction values remain authoritative.

AI classification should be treated as an interpretation layer.

13. Transaction Ownership

Every transaction must contain:

userId

This establishes ownership.

All transaction queries must be scoped to the authenticated user.

Conceptually:

Authenticated User
        ↓
userId
        ↓
Transaction Query
        ↓
Only that user's records

A request must never be allowed to retrieve another user's transactions by simply supplying a different ID.

14. Transaction Indexes

Important transaction indexes include:

{ userId: 1, date: -1 }

for time-based transaction retrieval.

Potential additional indexes:

{ userId: 1, category: 1 }

{ userId: 1, merchant: 1 }

{ userId: 1, type: 1 }

{ userId: 1, date: 1, category: 1 }

Indexes should be introduced according to actual query patterns.

Indexes must not be added indiscriminately.

15. Duplicate Detection

Imported transactions require duplicate protection.

A conceptual duplicate signature may combine:

userId
+
date
+
amount
+
merchant
+
description

The exact algorithm will be defined during implementation.

Duplicate detection should not automatically assume that two similar transactions are identical.

The system may classify a record as:

Potential Duplicate

before exclusion.

16. Goal Collection

Goals represent financial objectives.

Examples:

emergency fund,
vacation,
education,
major purchase,
savings target.
16.1 Conceptual Schema
{
  _id: ObjectId,

  userId: ObjectId,

  name: String,

  targetAmount: Number,

  currentAmount: Number,

  targetDate: Date,

  category: String,

  priority: String,

  status: String,

  createdAt: Date,

  updatedAt: Date
}
17. Goal Status

Possible statuses include:

ACTIVE
COMPLETED
PAUSED
CANCELLED

The status should be derived or updated consistently according to goal rules.

18. Budget Collection

Budgets represent planned spending limits.

18.1 Conceptual Schema
{
  _id: ObjectId,

  userId: ObjectId,

  category: String,

  amount: Number,

  period: String,

  startDate: Date,

  endDate: Date,

  createdAt: Date,

  updatedAt: Date
}
19. Budget Tracking

Budget progress is derived from transaction data.

Conceptually:

Budget
   +
Transactions
   ↓
Spent Amount
   ↓
Remaining Amount
   ↓
Budget Status

The database should avoid treating spentAmount as an independent source of truth unless a deliberate caching strategy is introduced.

20. Budget Status

A budget may be evaluated using states such as:

ON_TRACK
WARNING
EXCEEDED
COMPLETED

The exact thresholds belong to the business-rules specification.

21. Insight Collection

Insights represent meaningful financial observations surfaced to the user.

21.1 Purpose

An Insight is not simply raw analytics.

It represents a financial event that WealthWise considers worth bringing to the user's attention.

21.2 Conceptual Schema
{
  _id: ObjectId,

  userId: ObjectId,

  type: String,

  title: String,

  summary: String,

  category: String,

  severity: String,

  sourceSignals: [],

  context: {},

  recommendation: String,

  aiGenerated: Boolean,

  createdAt: Date,

  expiresAt: Date,

  status: String
}
22. Insight Data Separation

The Insight document may contain references to the signals that caused it.

However, the Insight should not duplicate the entire transaction history.

Conceptually:

Transactions
      ↓
Analytics
      ↓
Behaviour Signal
      ↓
Insight

This maintains clear data ownership.

23. Scenario Collection

Scenarios represent hypothetical financial simulations.

Examples:

Reduce food spending by 20%
Increase monthly savings by ₹2,000
Purchase a ₹10,000 phone
Increase transportation spending
23.1 Conceptual Schema
{
  _id: ObjectId,

  userId: ObjectId,

  type: String,

  parameters: {},

  result: {},

  relatedGoalIds: [],

  createdAt: Date
}
24. Scenario Data Rule

Scenario calculations must not mutate actual financial records.

A scenario is:

A simulation of a possible future state.

It is not an actual transaction.

Therefore:

Scenario
   ↓
Simulation
   ↓
Result

must remain separate from:

Actual Transactions
25. Financial Context Collection

The Financial Context represents an aggregated representation of the user's current financial state.

It belongs to the Personal Money Model.

25.1 Purpose

It may contain derived information such as:

Current Income
Current Expenses
Current Savings
Savings Rate
Category Trends
Behaviour Signals
Active Goals
Budget Status
Recent Financial Events
25.2 Conceptual Schema
{
  _id: ObjectId,

  userId: ObjectId,

  period: {
    start: Date,
    end: Date
  },

  metrics: {
    income: Number,
    expenses: Number,
    savings: Number,
    savingsRate: Number
  },

  categorySummary: [],

  behaviourSignals: [],

  goalSummary: [],

  budgetSummary: [],

  generatedAt: Date,

  updatedAt: Date
}
26. Financial Context as Derived Data

Financial Context is not the original financial source of truth.

The source hierarchy remains:

Transactions
      ↓
Calculations
      ↓
Financial Context

If the context becomes inconsistent, it must be possible to reconstruct it from authoritative source data.

27. AI Conversation Collection

AI conversations store interaction history with the WealthWise advisor.

27.1 Conceptual Schema
{
  _id: ObjectId,

  userId: ObjectId,

  sessionId: String,

  messages: [
    {
      role: String,
      content: String,
      createdAt: Date
    }
  ],

  metadata: {
    contextType: String
  },

  createdAt: Date,

  updatedAt: Date
}
28. AI Conversation Separation

AI conversations must remain separate from financial source data.

A conversation should not automatically become part of the user's financial truth.

For example:

User:
"I think I spend too much on food."


must not modify:

Food spending

unless the user explicitly performs a financial action through an appropriate workflow.

This follows the component architecture's rule that conversation history should remain isolated from the core financial model.

29. Embedded vs Referenced Data

WealthWise should use a hybrid MongoDB modeling strategy.

Embed when:
data is tightly coupled,
data is small,
data is generally retrieved with its parent,
independent querying is unnecessary.

Examples:

User preferences
Transaction classification
AI message metadata
Reference when:
entities have independent lifecycles,
records can grow significantly,
independent queries are common,
ownership needs to remain explicit.

Examples:

User → Transactions
User → Goals
User → Budgets
User → Insights
User → Scenarios
30. Referential Model

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
 ├──< FinancialContexts
 │
 └──< AIConversations

The < indicates a one-to-many relationship.

31. Financial Precision

Financial calculations must avoid careless floating-point operations.

The implementation must define a consistent monetary representation.

Possible approaches include:

integer minor units

or an appropriate decimal representation supported by MongoDB/Mongoose.

For example:

₹125.50

should not be represented in a way that introduces avoidable floating-point precision errors.

The final monetary representation will be finalized during implementation.

32. Currency

The initial product may focus on:

INR

However, the data model should avoid hard-coding currency into every calculation.

A user's preferred currency should be associated with their account.

Future multi-currency support may introduce:

currency
exchangeRate
baseCurrency

where required.

33. Date and Time Strategy

Financial records are time-sensitive.

Transactions should store a canonical date representation.

The application must define a consistent timezone strategy.

Important time concepts include:

Transaction Date
Import Date
Goal Deadline
Budget Period
Insight Creation Time
AI Conversation Time

The application should distinguish between:

When a financial event occurred

and:

When WealthWise processed the event.

34. Data Ownership

Each collection should have a clear owning module.

Collection	Owning Module
users	Auth
transactions	Transactions
goals	Goals
budgets	Budgets
insights	Insights
scenarios	Scenarios
financialContexts	Money Model
aiConversations	Advisor

Other modules should access data through the owning module's services rather than directly manipulating another module's persistence model.

This follows the architectural rule that modules should communicate through explicit service interfaces rather than directly accessing each other's collections.

35. Database Access Boundary

The preferred access pattern is:

Controller
    ↓
Service
    ↓
Repository
    ↓
Mongoose Model
    ↓
MongoDB

Controllers should not directly query MongoDB.

Services should not contain raw database implementation details.

Repositories should isolate persistence operations.

36. AI Database Boundary

The AI layer must not directly access MongoDB.

Instead:

AI Advisor
    ↓
Context Selector
    ↓
Application Services
    ↓
Structured Financial Context
    ↓
AI Provider

This ensures that AI receives only the information required for the specific task.

The component architecture explicitly establishes this boundary.

37. Data Derivation Hierarchy

WealthWise data can be viewed as four levels.

LEVEL 1 — SOURCE DATA

Transactions
Goals
Budgets


        ↓


LEVEL 2 — FINANCIAL CALCULATIONS

Income
Expenses
Savings
Trends
Category Metrics


        ↓


LEVEL 3 — INTELLIGENCE

Behaviour Signals
Financial Context
Scenario Results
Insights


        ↓


LEVEL 4 — COMMUNICATION

AI Explanations
Recommendations
Conversational Responses

The higher levels must not silently overwrite the lower levels.

38. Rebuilding Derived Data

Derived information should be reproducible whenever practical.

For example:

Transactions
     ↓
Analytics
     ↓
Behaviour
     ↓
Financial Context

If Financial Context becomes stale, the system should be able to regenerate it.

Similarly, an insight should be traceable to the underlying financial signals that generated it.

39. Soft Deletion

The application should carefully distinguish between:

Deleted

and:

Archived

Financial records may require stronger retention semantics than ordinary application data.

The final deletion policy will be defined alongside security and business requirements.

40. Auditability

Important financial changes should be traceable.

Potential audit information includes:

createdAt
updatedAt
source
classification method
import metadata

Future versions may introduce a dedicated audit collection if required.

41. Database Validation

Mongoose schemas should enforce important structural constraints.

Examples:

required fields,
valid enum values,
positive monetary amounts where appropriate,
valid dates,
valid ObjectId references,
valid percentages,
maximum string lengths.

Application-level validation should complement, not replace, schema validation.

42. Indexing Strategy

Indexes should primarily support:

User-scoped queries
userId
Time-based queries
userId + date
Category analysis
userId + category
Merchant analysis
userId + merchant
Goal and budget retrieval
userId + status

The exact index set will be finalized after API and query patterns are defined.

43. Performance Considerations

The database architecture should support:

efficient user-scoped queries,
pagination for transactions,
date-range filtering,
category aggregation,
indexed lookups,
efficient dashboard loading,
incremental derived-data refresh.

Large transaction histories should not be loaded entirely into application memory.

44. Aggregation Strategy

MongoDB aggregation pipelines may be used for deterministic financial analysis where appropriate.

Examples:

Transactions
    ↓
$match
    ↓
$group
    ↓
$sum
    ↓
Category / Period Metrics

However, financial business rules should remain understandable and independently testable.

Database aggregation must not become an opaque replacement for domain logic.

45. Dashboard Data

The dashboard may require:

Current Income
Current Expenses
Savings
Savings Rate
Top Categories
Recent Transactions
Budget Status
Goal Progress
Important Insights

The application may combine several repository/service operations or introduce optimized read models if performance later requires them.

46. Data Consistency

Operations that modify multiple authoritative entities must define their consistency requirements.

For example:

Creating a Goal

is primarily a Goal operation.

Whereas:

Importing Transactions

may trigger:

Transaction persistence
        ↓
Analytics refresh
        ↓
Behaviour analysis
        ↓
Insight evaluation

The MVP may perform these steps synchronously.

Future versions may use domain events when scale or processing complexity justifies it.

47. Transaction Import Persistence

Imported transactions should preserve enough metadata to identify their origin.

Example:

{
  importMetadata: {
    sourceFile: "statement.csv",
    rowIdentifier: "row-104",
    importedAt: "..."
  }
}

This supports:

debugging,
duplicate detection,
import traceability,
future reprocessing.
48. Financial Data Integrity Rules

The database layer must preserve the following principles:

Rule 1

A transaction belongs to exactly one user.

Rule 2

AI-generated text cannot modify authoritative financial values.

Rule 3

Scenario results cannot modify actual transactions.

Rule 4

Derived financial context must be reproducible.

Rule 5

Financial calculations must use consistent monetary representation.

Rule 6

Cross-user data access must be prevented.

Rule 7

Deleted or modified records must follow defined retention rules.

49. Database Security

The database must be protected through:

authenticated application access,
restricted database credentials,
environment-based secret management,
least-privilege database permissions,
secure connection configuration,
controlled repository access.

Database credentials must never be committed to Git.

50. Sensitive Financial Data

The system may contain sensitive information including:

transaction amounts,
merchant information,
spending behaviour,
financial goals,
income information.

Therefore:

Financial Data
      ↓
Authenticated Access
      ↓
User Authorization
      ↓
Controlled Service Layer
      ↓
Repository
      ↓
Database

Financial information should never be exposed through unnecessary logs or debugging output.

51. AI Data Minimization

When sending financial context to an external AI provider, WealthWise should provide only the information necessary for the requested task.

For example:

Question:
"Why did my savings decrease?"

The AI may receive:

Current Income
Current Expenses
Previous Expenses
Major Spending Changes
Savings Difference
Relevant Goal

It does not necessarily require every transaction.

52. Collection Growth Considerations

The transactions and aiConversations collections are expected to grow more rapidly than many other collections.

Therefore:

Transactions
AI Conversations

should receive particular attention regarding:

indexing,
pagination,
archival,
query efficiency,
retention.
53. Backup and Recovery

Production deployment should include a database backup strategy.

The strategy should define:

backup frequency,
retention,
recovery procedure,
recovery testing,
backup security.

The exact production policy will be defined during deployment planning.

54. Database Environment Separation

The project should maintain separate environments where appropriate:

Development
Testing
Production

Production data must never be casually copied into development environments.

55. Development Database

Development may use:

Local MongoDB

or:

MongoDB Atlas Development Cluster

depending on the deployment setup.

The database connection string must be stored through environment variables.

Example:

MONGODB_URI=...

and must never be committed to the repository.

56. Migration Strategy

MongoDB does not require traditional relational migrations for every schema change.

However, schema evolution must still be managed deliberately.

Changes such as:

adding fields
renaming fields
changing data representation
changing category taxonomy

must be documented.

When necessary, migration scripts should be created rather than relying on undocumented manual database modifications.

57. Seed Data

Development environments may use controlled seed data for:

demo users,
transactions,
goals,
budgets,
behavioural patterns,
insights.

Seed data should never contain real personal financial information.

58. Database Testing

Database-related testing should include:

Schema Tests

Verify:

required fields,
enums,
validation,
defaults.
Repository Tests

Verify:

CRUD operations,
filtering,
ownership isolation,
indexes/query behaviour.
Integration Tests

Verify:

API
 ↓
Service
 ↓
Repository
 ↓
MongoDB
59. Data Ownership Boundary

The final ownership model is:

                    MongoDB
                       │
       ┌───────────────┼────────────────┐
       │               │                │
       ▼               ▼                ▼
 Financial Core   Intelligence      AI Persistence
       │               │                │
       │               │                │
 Transactions      Insights       AI Conversations
 Goals             Context
 Budgets           Scenarios

The Financial Core remains authoritative.

60. Authoritative vs Derived Data
Data	Type	Authoritative
User	Source	Yes
Transaction	Source	Yes
Goal	Source	Yes
Budget	Source	Yes
Financial Metrics	Derived	No
Behaviour Signal	Derived	No
Financial Context	Derived	No
Scenario Result	Derived	No
Insight	Derived	No
AI Explanation	Generated	No
AI Conversation	Generated/User Content	No

This distinction is fundamental to WealthWise.

61. Complete Data Flow

The database participates in the WealthWise intelligence pipeline as follows:

                  USER
                    │
                    ▼
              Transaction
                    │
                    ▼
               MongoDB
                    │
                    ▼
              Analytics
                    │
                    ▼
          Behaviour Intelligence
                    │
                    ▼
          Personal Money Model
                    │
             ┌──────┴──────┐
             ▼             ▼
           Goals         Budgets
             │             │
             └──────┬──────┘
                    ▼
             Insight Engine
                    │
                    ▼
               AI Advisor
                    │
                    ▼
             AI Provider
                    │
                    ▼
            User Explanation
62. Example — Transaction to Insight

Suppose a user has:

Average Food Spending = ₹4,000
Current Food Spending = ₹6,000

The database contains the underlying transactions.

The application calculates:

+50% spending increase

The Behaviour module creates a structured signal.

The Insight module determines whether it is significant.

The AI layer may then generate:

Your food spending increased significantly this month compared with your recent baseline.

The AI explanation does not replace the underlying transaction data or financial calculation.

63. Example — Scenario Persistence

Suppose the user asks:

"What if I reduce food spending by 20%?"

The Scenario Engine calculates:

Current Food Spending = ₹6,000

Reduction = 20%

Projected Food Spending = ₹4,800

Potential Monthly Saving = ₹1,200

The result may be stored in the Scenario collection.

The actual transaction data remains unchanged.

64. Example — Goal Feasibility

Suppose:

Goal Target = ₹60,000
Current Amount = ₹20,000
Remaining = ₹40,000

The Goal module calculates the required contribution.

The calculation may use:

Goal
+
Current Savings Capacity
+
Target Date

The resulting feasibility information is derived.

The original Goal document remains the authoritative definition of the user's target.

65. Database Architecture and AI Boundary

The complete boundary is:

                    MongoDB
                       │
                       ▼
             Structured Financial Data
                       │
                       ▼
              Application Services
                       │
                       ▼
             Personal Money Model
                       │
                       ▼
              Context Selector
                       │
                       ▼
                Prompt Builder
                       │
                       ▼
                  AI Provider
                       │
                       ▼
              Response Processor
                       │
                       ▼
                     User

The AI provider does not directly query the database.

66. Future Database Evolution

Future versions may introduce:

dedicated event collections,
optimized read models,
materialized analytics,
financial-data imports,
bank integrations,
multi-currency support,
audit logs,
notification persistence,
recommendation history.

These should be introduced only when justified by actual product requirements.

67. MVP Database Scope

The MVP database should initially focus on:

User
Transaction
Goal
Budget
Insight
Scenario
FinancialContext
AIConversation

Avoid creating collections for future functionality before it is required.

68. Database Architecture Summary

The WealthWise database is designed around a clear distinction between:

SOURCE
    ↓
CALCULATED
    ↓
INTELLIGENCE
    ↓
AI COMMUNICATION

The authoritative financial foundation consists primarily of:

Users
Transactions
Goals
Budgets

The intelligence layer derives:

Financial Metrics
Behaviour Signals
Financial Context
Scenario Results
Insights

The AI layer persists conversational information separately.

Most importantly:

MongoDB stores the financial truth and structured application state.

The intelligence layer derives meaning from that state.

The AI layer explains that meaning to the user.

Therefore:

WealthWise remains a financial intelligence system with AI assistance, rather than an AI system attempting to become the financial source of truth.