# WealthWise — Data Dictionary

**Document Version:** 1.0  
**Status:** Database Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Database:** MongoDB  
**ODM:** Mongoose  

---

# 1. Purpose

This document defines the logical data model of WealthWise at the field level.

It describes:

- entities,
- fields,
- data types,
- required/optional status,
- allowed values,
- relationships,
- ownership,
- derived fields,
- and data responsibilities.

This document is intentionally independent of the final Mongoose implementation.

The purpose is to establish a stable data contract before database models are implemented.

---

# 2. Data Model Philosophy

WealthWise separates data into four conceptual levels:

```text
Source Data
    ↓
Financial Calculations
    ↓
Financial Intelligence
    ↓
AI Communication

in between why is there a diff between 01 and 04 

05 is already architecture
Pasted markdown.md
File

Yes — after reading the actual component-architecture.md, we should not create a separate frontend-architecture.md right now.

Your existing document already explicitly defines both the frontend and backend architecture. It covers the React structure, feature modules, shared components, backend modules, controllers, services, repositories, AI boundaries, API flow, dependency rules, and the complete component model.

So your current 05-architecture is actually well organized:

05-architecture/
│
├── system-architecture.md
├── component-architecture.md   ← frontend + backend already here
├── data-flow-architecture.md
├── database-architecture.md
├── api-architecture.md
├── ai-architecture.md
└── security-architecture.md

And importantly, the component architecture already establishes the core architectural philosophy:

deterministic financial system → calculates and validates
intelligence layer → understands patterns
AI layer → interprets and communicates

That boundary is explicitly defined in the document.

It also already defines the most important WealthWise pipeline:

Transaction
    ↓
Financial Metric
    ↓
Behaviour Signal
    ↓
Financial Context
    ↓
Goal / Budget Impact
    ↓
Scenario
    ↓
Insight
    ↓
AI Explanation
    ↓
User Decision

Therefore, our next document should NOT be frontend architecture.

Looking at what you already have, the sensible progression is:

01-product
      ↓
02-requirements
      ↓
03-features
      ↓
04-design
      ↓
05-architecture
      ↓
06-database
      ↓
07-api
      ↓
08-ui-ux
      ↓
09-testing
      ↓
10-academic

And within 05-architecture, we should first make sure the existing documents don't overlap or contradict each other.

What I suggest we do next

Before writing anything new, let's audit 05-architecture:

system-architecture.md
component-architecture.md ✅ reviewed
data-flow-architecture.md
database-architecture.md
api-architecture.md
ai-architecture.md
security-architecture.md

Then we move to 06-database.

That will prevent us from creating the same architecture decisions three different times — which is exactly what we want to avoid in a serious project documentation set.

lets move to 06

Yes. We now move to 06-database.

Since component-architecture.md already establishes the database-facing components and says the final schema will be defined separately, this is where we formally define what WealthWise stores, how entities relate, what each collection means, indexes, validation, financial-data rules, and AI-data separation.

Create:

docs/06-database/database-architecture.md

Use the following as the first complete version:

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


### One important thing

Notice I have **not** yet locked down every field to a production-level Mongoose schema. That's intentional.

The next documents will determine the exact requirements and API/query patterns, after which we can refine:

```text
06-database
    ↓
exact schemas
    ↓
07-api
    ↓
actual endpoints + query patterns

That way we don't prematurely design a database around APIs that we haven't formally defined yet.

Create docs/06-database/database-architecture.md with the above. Then we can move to the next database document rather than jumping to API implementation prematurely.

next

Next, we stay inside 06-database.

We have now defined the overall database architecture. The next logical document is the Data Dictionary / Schema Specification — this gives every entity a precise field-level definition without jumping into Mongoose implementation yet.

Create:

docs/06-database/data-dictionary.md

Use this:

# WealthWise — Data Dictionary

**Document Version:** 1.0  
**Status:** Database Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Database:** MongoDB  
**ODM:** Mongoose  

---

# 1. Purpose

This document defines the logical data model of WealthWise at the field level.

It describes:

- entities,
- fields,
- data types,
- required/optional status,
- allowed values,
- relationships,
- ownership,
- derived fields,
- and data responsibilities.

This document is intentionally independent of the final Mongoose implementation.

The purpose is to establish a stable data contract before database models are implemented.

---

# 2. Data Model Philosophy

WealthWise separates data into four conceptual levels:

```text
Source Data
    ↓
Financial Calculations
    ↓
Financial Intelligence
    ↓
AI Communication

The source data remains authoritative.

Derived and AI-generated information must never silently overwrite source financial records.

3. Entity Inventory

The initial WealthWise data model contains:

1. User
2. Transaction
3. Goal
4. Budget
5. Insight
6. Scenario
7. Financial Context
8. AI Conversation
4. Entity Relationship Overview
                           USER
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ▼                 ▼                 ▼
    TRANSACTIONS          GOALS            BUDGETS
          │                 │                 │
          └─────────────────┼─────────────────┘
                            │
                            ▼
                    FINANCIAL CONTEXT
                            │
                ┌───────────┴───────────┐
                ▼                       ▼
          BEHAVIOUR SIGNALS          INSIGHTS
                                        │
                                        ▼
                                  AI ADVISOR
                                        │
                                        ▼
                                AI CONVERSATION

USER
 │
 └──────────────────────────────→ SCENARIOS
5. Common Field Conventions

All major entities should use:

_id
createdAt
updatedAt

where applicable.

MongoDB ObjectIds are used for entity identifiers.

References should use:

userId
goalId
budgetId
transactionId
insightId
scenarioId

rather than duplicating entire entities.

6. User
6.1 Purpose

Represents an authenticated WealthWise account.

6.2 Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Unique user identifier
name	String	Yes	User's display name
email	String	Yes	Unique login email
passwordHash	String	Yes	Hashed authentication credential
currency	String	Yes	Preferred currency
preferences	Object	No	User-level application preferences
preferences.timezone	String	No	User timezone
preferences.dateFormat	String	No	Preferred date format
createdAt	Date	Yes	Account creation time
updatedAt	Date	Yes	Last account update
7. User Constraints
email
→ unique

passwordHash
→ never plaintext

currency
→ valid supported currency

name
→ non-empty

userId
→ unique MongoDB identifier

The backend remains responsible for authentication and authorization.

8. Transaction
8.1 Purpose

Represents an individual financial event.

Transactions are the primary financial source records of WealthWise.

8.2 Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Transaction identifier
userId	ObjectId	Yes	Owning user
date	Date	Yes	Date financial event occurred
description	String	Yes	Original transaction description
merchant	String	No	Merchant or payee
amount	Number/Decimal	Yes	Transaction amount
type	Enum	Yes	Financial transaction type
category	Enum/String	Yes	Expense/income category
source	Enum	Yes	Origin of transaction
account	String	No	User-defined account/source
notes	String	No	Optional user notes
importMetadata	Object	No	Import-related information
classification	Object	No	Categorization metadata
createdAt	Date	Yes	Record creation time
updatedAt	Date	Yes	Record update time
9. Transaction Type

Initial values:

INCOME
EXPENSE
TRANSFER
REFUND

The value must be validated against the controlled vocabulary.

10. Transaction Category

Initial WealthWise categories:

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

The category system may evolve.

Category changes must be documented because analytics and historical comparisons depend on category consistency.

11. Transaction Source

Initial values:

MANUAL
CSV_IMPORT
SYSTEM

Future sources may include:

BANK_IMPORT
API

but these should not be introduced until required.

12. Transaction Classification
classification
├── method
└── confidence
method

Possible values:

RULE
USER
AI
SYSTEM
confidence

A normalized confidence score:

0.0 → 1.0

Example:

0.95

means the classifier has high confidence.

Classification confidence must not be treated as financial certainty.

13. Transaction Import Metadata
importMetadata
├── sourceFile
├── rowIdentifier
└── importedAt

Purpose:

duplicate detection,
traceability,
debugging,
reprocessing,
import history.
14. Goal
14.1 Purpose

Represents a financial objective defined by the user.

Examples:

Emergency Fund
Vacation
New Laptop
Education
Investment Target
14.2 Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Goal identifier
userId	ObjectId	Yes	Owning user
name	String	Yes	Goal name
targetAmount	Number/Decimal	Yes	Desired amount
currentAmount	Number/Decimal	Yes	Current progress
targetDate	Date	No	Desired completion date
category	String	No	Goal category
priority	Enum	No	Goal importance
status	Enum	Yes	Current goal state
createdAt	Date	Yes	Creation time
updatedAt	Date	Yes	Last update
15. Goal Priority

Possible values:

LOW
MEDIUM
HIGH
CRITICAL

Priority affects recommendation ranking but should not independently alter financial calculations.

16. Goal Status

Possible values:

ACTIVE
COMPLETED
PAUSED
CANCELLED
17. Goal Rules
targetAmount > 0

currentAmount >= 0

currentAmount <= targetAmount

If the product later allows overfunded goals, that rule can be revised.

18. Budget
18.1 Purpose

Represents a planned spending limit.

A budget may apply to:

a category,
a time period,
or another defined budgeting dimension.
18.2 Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Budget identifier
userId	ObjectId	Yes	Owning user
category	String	Yes	Budgeted category
amount	Number/Decimal	Yes	Planned spending limit
period	Enum	Yes	Budget period
startDate	Date	Yes	Period start
endDate	Date	Yes	Period end
createdAt	Date	Yes	Creation time
updatedAt	Date	Yes	Last update
19. Budget Period

Initial supported periods:

WEEKLY
MONTHLY
YEARLY
CUSTOM
20. Budget Derived Values

The following values should generally be calculated rather than treated as independent source data:

spentAmount
remainingAmount
percentageUsed
status

Conceptually:

Budget
   +
Relevant Transactions
   ↓
Budget Usage
   ↓
Budget Status

This prevents duplicated financial truth.

21. Insight
21.1 Purpose

An Insight represents a meaningful observation about the user's financial behaviour.

An insight should answer:

Why should the user care about this information?

21.2 Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Insight identifier
userId	ObjectId	Yes	Owning user
type	String/Enum	Yes	Insight classification
title	String	Yes	Short insight title
summary	String	Yes	Human-readable explanation
category	String	No	Related financial category
severity	Enum	Yes	Importance
sourceSignals	Array	No	Supporting intelligence signals
context	Object	No	Relevant financial context
recommendation	String	No	Suggested action
aiGenerated	Boolean	Yes	Whether AI contributed
createdAt	Date	Yes	Creation time
expiresAt	Date	No	Optional expiration
status	Enum	Yes	Current insight state
22. Insight Types

Initial conceptual types:

SPENDING_SPIKE
CATEGORY_TREND
RECURRING_EXPENSE
BUDGET_WARNING
BUDGET_EXCEEDED
SAVINGS_CHANGE
GOAL_RISK
GOAL_PROGRESS
UNUSUAL_TRANSACTION
POSITIVE_BEHAVIOUR

Additional types may be introduced as intelligence capabilities expand.

23. Insight Severity
INFO
LOW
MEDIUM
HIGH
CRITICAL

Severity represents importance, not financial certainty.

24. Insight Status
NEW
READ
DISMISSED
ACTIONED
EXPIRED
25. Scenario
25.1 Purpose

Represents a hypothetical financial situation evaluated by WealthWise.

25.2 Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Scenario identifier
userId	ObjectId	Yes	Owning user
type	String	Yes	Scenario category
parameters	Object	Yes	User-defined assumptions
result	Object	Yes	Calculated outcome
relatedGoalIds	Array	No	Goals affected
createdAt	Date	Yes	Scenario creation time
26. Scenario Parameters

Parameters depend on scenario type.

Example:

{
    "category": "Food",
    "reductionPercentage": 20
}

Another example:

{
    "additionalMonthlySavings": 2000
}

Parameters must be validated according to the scenario type.

27. Scenario Result

A scenario result may contain:

projectedExpenses
projectedSavings
savingsDifference
goalImpact
projectedCompletionDate
warnings

The exact result structure belongs to the Scenario Engine specification.

28. Scenario Integrity

Scenario data must never modify:

transactions
budgets
goals

unless the user explicitly converts a scenario into an actual financial action through a separate workflow.

29. Financial Context
29.1 Purpose

Represents a derived snapshot of the user's financial state.

29.2 Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Context identifier
userId	ObjectId	Yes	Owning user
period	Object	Yes	Analysis period
period.start	Date	Yes	Period start
period.end	Date	Yes	Period end
metrics	Object	Yes	Financial metrics
categorySummary	Array	No	Category-level summary
behaviourSignals	Array	No	Behaviour observations
goalSummary	Array	No	Goal state
budgetSummary	Array	No	Budget state
generatedAt	Date	Yes	Context generation time
updatedAt	Date	Yes	Last refresh
30. Financial Metrics

Initial metrics:

income
expenses
savings
savingsRate

Definitions:

Savings = Income - Expenses

Savings Rate =
Savings / Income × 100

The authoritative implementation of these calculations belongs to the financial services layer.

31. Category Summary

A category summary may contain:

category
amount
percentage
transactionCount
changeFromPreviousPeriod

Example:

{
  category: "Food",
  amount: 6000,
  percentage: 24,
  transactionCount: 18,
  changeFromPreviousPeriod: 12.5
}

These are derived values.

32. Behaviour Signals

A behaviour signal represents a structured observation.

Example:

{
  type: "SPENDING_INCREASE",
  category: "Food",
  magnitude: 0.24,
  period: "MONTHLY",
  confidence: 0.91
}

Behaviour signals are not raw transactions.

33. Goal Summary

A Financial Context may contain a compact representation of active goals:

goalId
targetAmount
currentAmount
remainingAmount
progressPercentage
targetDate
feasibility

The complete Goal remains stored in the Goals collection.

34. Budget Summary

A Financial Context may contain:

budgetId
category
limit
spent
remaining
percentageUsed
status

The complete Budget remains stored separately.

35. AI Conversation
35.1 Purpose

Stores conversations with the WealthWise AI Advisor.

35.2 Conversation Fields
Field	Type	Required	Description
_id	ObjectId	Yes	Conversation identifier
userId	ObjectId	Yes	Owning user
sessionId	String	Yes	Conversation/session identifier
messages	Array	Yes	Conversation messages
metadata	Object	No	Context metadata
createdAt	Date	Yes	Creation time
updatedAt	Date	Yes	Last update
36. AI Message

Each message contains:

role
content
createdAt

Possible roles:

user
assistant

Internal system instructions should not be persisted as ordinary user-visible conversation messages.

37. AI Metadata

Conversation metadata may contain:

contextType
model
tokenUsage
responseId

Only metadata required for application functionality, monitoring, or auditing should be stored.

38. AI Context Isolation

AI conversation history must not become financial source data.

For example:

User says:
"I spent ₹5,000 on food."

does not automatically create a Transaction.

The user must explicitly create or import a transaction through the appropriate financial workflow.

39. Common Metadata

Where appropriate, entities may contain:

createdAt
updatedAt

These timestamps represent database record lifecycle events.

They should not be confused with financial event timestamps.

40. Financial Event Time vs Record Time

For transactions:

date

means:

When the financial event occurred.

While:

createdAt

means:

When WealthWise stored the record.

These values can differ.

Example:

Transaction Date:
10 August

Imported At:
11 August
41. Ownership Model

Every user-owned financial entity must contain:

userId

Initial user-owned entities:

Transaction
Goal
Budget
Insight
Scenario
FinancialContext
AIConversation

This enables consistent authorization.

42. Ownership Rule

Every database operation involving user-owned data must follow:

Authenticated User
        ↓
Authenticated userId
        ↓
Query filter
        ↓
Database

Never trust a client-provided userId as the authorization source.

43. Reference Rules

References should be used for independent entities.

Example:

Transaction
    ↓
userId

and:

Scenario
    ↓
relatedGoalIds

The referenced entity must remain independently authoritative.

44. Denormalization Rules

Limited denormalization is permitted for:

Read performance
Snapshot generation
AI context
Dashboard summaries

However, duplicated financial values must be clearly identified as derived.

45. Derived Data Marker

Where useful, derived structures should expose metadata such as:

generatedAt
sourcePeriod
calculationVersion

This allows WealthWise to determine whether a derived result is stale.

46. Calculation Version

Future financial calculations may change.

Therefore, derived data may optionally contain:

calculationVersion

Example:

{
  calculationVersion: 2
}

This can help identify derived data produced using an older algorithm.

47. Schema Evolution

Changes to the data model must be documented.

Examples:

Adding a field
Removing a field
Renaming a field
Changing an enum
Changing monetary representation
Changing category taxonomy

Existing records must be considered before changing a field's meaning.

48. Required vs Optional Data

The principle is:

Required fields represent information necessary for the entity to be meaningful.

Optional fields represent:

enrichment,
user preferences,
metadata,
future extensibility.

Do not make a field optional simply to avoid validation.

49. Monetary Fields

Monetary fields include:

Transaction.amount
Goal.targetAmount
Goal.currentAmount
Budget.amount
FinancialContext.metrics
Scenario results

The final implementation must use a consistent monetary representation.

Precision must be preserved.

50. Percentage Fields

Percentage values must use a consistent representation.

For example:

0.25

may represent:

25%

or:

25

may represent:

25%

The project must select one convention and use it consistently.

The final convention will be established during implementation.

51. Null vs Missing

The system should distinguish between:

field not applicable

and:

field applicable but currently unknown

Avoid unnecessary null values.

Use missing fields where the field is genuinely optional.

52. Data Dictionary Summary
USER
 └── Identity + Preferences

TRANSACTION
 └── Financial Source Event

GOAL
 └── Financial Objective

BUDGET
 └── Spending Plan

INSIGHT
 └── Financial Observation

SCENARIO
 └── Hypothetical Projection

FINANCIAL CONTEXT
 └── Derived Financial Snapshot

AI CONVERSATION
 └── Human-AI Interaction History
53. Source vs Derived Data
AUTHORITATIVE
─────────────
User
Transaction
Goal
Budget


DERIVED
─────────────
Financial Metrics
Category Summaries
Behaviour Signals
Financial Context
Scenario Results
Insights


GENERATED
─────────────
AI Explanations
AI Recommendations
AI Conversations
54. Final Data Model Principle

The WealthWise database must preserve a strict hierarchy:

             SOURCE OF TRUTH
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
 Transactions     Goals       Budgets
       │            │            │
       └────────────┼────────────┘
                    ▼
             FINANCIAL MODEL
                    │
                    ▼
             INTELLIGENCE
                    │
                    ▼
                 AI LAYER

The central rule is:

AI can interpret WealthWise data, but AI cannot define WealthWise's financial truth.

55. Status

Data Dictionary: Defined

This document establishes the logical data contract.

The exact implementation of:

Mongoose schemas
Indexes
Validators
Repositories
Aggregation queries

will be finalized alongside the API and backend implementation.

The next database-level work will therefore focus on MongoDB indexing, query patterns, and data lifecycle, before moving into the API layer.

