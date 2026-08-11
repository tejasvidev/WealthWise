# WealthWise — Component Architecture

**Document Version:** 1.0  
**Status:** Architecture Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Technology Direction:** MERN + Generative AI  
**Architecture Style:** Modular Monolith

---

# 1. Purpose

This document defines the internal component architecture of WealthWise.

The System Architecture document established the major architectural layers.

This document goes one level deeper and defines:

- backend modules,
- frontend modules,
- services,
- repositories,
- intelligence components,
- AI boundaries,
- component responsibilities,
- communication patterns,
- dependencies,
- and major data flows.

The architecture is designed to preserve the central WealthWise principle:

> **The deterministic financial system calculates and understands the numbers; the AI layer interprets those validated results and communicates them to the user.**

---

# 2. Architectural Style

The initial WealthWise implementation will use a:

> **Modular Monolith**

The backend will be deployed as a single application initially, but its internal responsibilities will be divided into clearly defined modules.

```text
                         WealthWise Backend
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  Auth      Transactions      Analytics      Behaviour        │
│                                                              │
│  Goals     Budgets           Scenarios      Insights         │
│                                                              │
│  Money Model                AI Advisor                      │
│                                                              │
└──────────────────────────────────────────────────────────────┘
                              │
                              ▼
                           MongoDB

4. Component Responsibility Principle

Each component should have one clearly defined primary responsibility.

For example:

Transaction Service
        ↓
Manages transactions

Analytics Service
        ↓
Calculates financial metrics

Behaviour Service
        ↓
Detects behavioural patterns

Goal Service
        ↓
Evaluates financial goals

Scenario Engine
        ↓
Simulates hypothetical changes

Insight Engine
        ↓
Determines meaningful financial events

AI Advisor
        ↓
Explains and communicates financial context

No component should become a general-purpose "do everything" service.

5. Frontend Component Architecture

The React frontend should be organized by business capability rather than by generic technical type alone.

Recommended structure:

frontend/src/
│
├── app/
│
├── components/
│   ├── ui/
│   ├── charts/
│   ├── forms/
│   └── layout/
│
├── features/
│   ├── auth/
│   ├── dashboard/
│   ├── transactions/
│   ├── analytics/
│   ├── goals/
│   ├── budgets/
│   ├── insights/
│   ├── scenarios/
│   ├── advisor/
│   └── settings/
│
├── services/
│   └── api/
│
├── hooks/
├── utils/
├── types/
└── assets/
6. Frontend Feature Modules

Each major feature should own its:

UI components,
state,
API interactions,
validation,
feature-specific utilities.

Example:

features/
└── transactions/
    ├── components/
    ├── pages/
    ├── hooks/
    ├── transaction.api.ts
    ├── transaction.types.ts
    └── transaction.utils.ts

This keeps feature logic localized.

7. Frontend Shared Components

Shared components should contain reusable presentation elements.

Examples:

components/
├── ui/
│   ├── Button
│   ├── Input
│   ├── Modal
│   ├── Card
│   ├── Badge
│   └── Dropdown
│
├── charts/
│   ├── LineChart
│   ├── BarChart
│   ├── DonutChart
│   └── TrendChart
│
└── layout/
    ├── Navbar
    ├── Sidebar
    ├── PageContainer
    └── LoadingState

Shared components should not contain WealthWise-specific financial business rules.

8. Backend Component Architecture

Recommended backend structure:

backend/
│
├── src/
│
│   ├── config/
│   │
│   ├── middleware/
│   │
│   ├── routes/
│   │
│   ├── modules/
│   │   ├── auth/
│   │   ├── transactions/
│   │   ├── analytics/
│   │   ├── behaviour/
│   │   ├── goals/
│   │   ├── budgets/
│   │   ├── scenarios/
│   │   ├── insights/
│   │   ├── money-model/
│   │   └── advisor/
│   │
│   ├── infrastructure/
│   │   ├── database/
│   │   ├── ai/
│   │   ├── files/
│   │   └── logging/
│   │
│   ├── shared/
│   │
│   └── app.ts
│
└── server.ts
9. Module Internal Structure

Each backend business module should follow a consistent internal structure.

Example:

transactions/
│
├── transaction.controller.ts
├── transaction.service.ts
├── transaction.repository.ts
├── transaction.model.ts
├── transaction.routes.ts
├── transaction.validation.ts
├── transaction.types.ts
└── transaction.utils.ts

Not every module needs every file.

The structure should remain pragmatic rather than rigid.

10. Controller Layer

Controllers are responsible for translating HTTP requests into application operations.

A controller should:

receive the request,
extract validated input,
identify the authenticated user,
call the appropriate service,
format the response.

Controllers should not contain complex financial calculations.

Example:

HTTP Request
     ↓
Controller
     ↓
Transaction Service
     ↓
Repository
11. Service Layer

Services contain business operations.

For example:

TransactionService
AnalyticsService
BehaviourService
GoalService
BudgetService
ScenarioService
InsightService
AdvisorService

Services coordinate repositories and other domain components.

12. Repository Layer

Repositories isolate database operations.

Example:

TransactionService
        ↓
TransactionRepository
        ↓
MongoDB

The service should not need to know how MongoDB queries are constructed.

13. Model Layer

Models define persistence structures.

Initial MongoDB models are expected to include:

User
Transaction
Goal
Budget
Insight
Scenario
FinancialContext
AIConversation

The final schema will be defined in:

docs/03-architecture/database-architecture.md
14. Authentication Module

The Authentication Module manages:

registration,
login,
logout,
session/token management,
password handling,
user identity.

Architecture:

Auth Controller
      ↓
Auth Service
      ↓
User Repository
      ↓
User Model
      ↓
MongoDB

The Auth Module should not contain financial business logic.

15. Transaction Module

The Transaction Module manages the user's raw financial records.

Responsibilities:

create,
read,
update,
delete,
search,
filter,
import,
normalize,
classify,
enrich.

Architecture:

Transaction Controller
          ↓
Transaction Service
          ↓
┌─────────┼───────────┐
↓         ↓           ↓
Parser   Classifier  Duplicate Detector
          │
          ▼
Transaction Repository
          │
          ▼
       MongoDB
16. Transaction Import Components

The import process should be divided into logical components.

File Upload
    ↓
File Validator
    ↓
Parser
    ↓
Normalizer
    ↓
Duplicate Detector
    ↓
Transaction Classifier
    ↓
Category Enricher
    ↓
Transaction Service
    ↓
Repository
17. File Validator

Responsible for:

file type validation,
file size validation,
structural validation,
required-column validation.

It should reject invalid files before expensive processing occurs.

18. Parser

The Parser converts the uploaded file into an internal record representation.

Example:

CSV Row
   ↓
Parser
   ↓
{
  date,
  description,
  amount,
  type
}

The Parser should not decide whether the transaction is financially meaningful.

19. Normalizer

The Normalizer converts inconsistent external data into WealthWise's internal representation.

Example:

"12/08/26"
     ↓
2026-08-12

Possible normalization tasks:

dates,
amount formats,
descriptions,
transaction types,
merchant strings.
20. Duplicate Detector

The Duplicate Detector identifies potentially repeated transactions.

Conceptual process:

Imported Transaction
        ↓
Generate Comparison Signature
        ↓
Search Existing Records
        ↓
Potential Duplicate?
      /      \
    YES       NO
     ↓         ↓
 Review /     Accept
 Exclude

The exact duplicate algorithm will be specified later.

21. Classification Component

The classification component determines:

income,
expense,
transfer,
refund.

Classification should preferably use deterministic rules first.

AI may be used where deterministic rules are insufficient.

22. Category Component

The category component assigns supported financial categories.

Example taxonomy:

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

The final taxonomy will be defined in the domain specification.

23. Analytics Module

The Analytics Module is responsible for deterministic financial calculations.

It contains components such as:

analytics/
│
├── analytics.service.ts
├── income.calculator.ts
├── expense.calculator.ts
├── savings.calculator.ts
├── trend.calculator.ts
├── category.calculator.ts
├── merchant.calculator.ts
└── analytics.repository.ts

The calculations should remain independently testable.

24. Analytics Dependency
Transactions
     ↓
Analytics Service
     ↓
┌────┼────┬────────┬──────────┐
↓    ↓    ↓        ↓          ↓
Income Expense Savings Trends Categories
25. Behaviour Intelligence Module

The Behaviour module converts financial metrics into behavioural signals.

Suggested structure:

behaviour/
│
├── behaviour.service.ts
├── baseline.service.ts
├── drift.detector.ts
├── spike.detector.ts
├── trend.detector.ts
├── significance.service.ts
└── behaviour.types.ts
26. Behaviour Pipeline
Financial Metrics
       ↓
Historical Baseline
       ↓
Comparison
       ↓
Pattern Detection
       ↓
Significance Evaluation
       ↓
Behaviour Signal
27. Behaviour Signal Structure

Conceptually:

{
  "type": "SPENDING_INCREASE",
  "category": "Food",
  "currentValue": 6200,
  "baselineValue": 3900,
  "difference": 2300,
  "percentageChange": 58.97,
  "period": "2026-08",
  "confidence": 0.91
}

The actual implementation schema will be defined separately.

28. Goals Module

The Goals Module manages financial objectives.

Suggested structure:

goals/
│
├── goal.controller.ts
├── goal.service.ts
├── goal.repository.ts
├── goal.model.ts
├── goal.calculator.ts
├── goal.feasibility.ts
├── goal.validation.ts
└── goal.routes.ts
29. Goal Calculation Components

The module should separate:

Goal Progress
Goal Gap
Required Contribution
Savings Capacity
Goal Feasibility
Goal Status
Goal Risk

This prevents one large goal service from becoming difficult to maintain.

30. Budget Module

Suggested structure:

budgets/
│
├── budget.controller.ts
├── budget.service.ts
├── budget.repository.ts
├── budget.model.ts
├── budget.calculator.ts
├── budget.trajectory.ts
├── budget.validation.ts
└── budget.routes.ts

Responsibilities:

budget creation,
tracking,
usage,
remaining amount,
trajectory,
status.
31. Scenario Module

The Scenario Module is one of WealthWise's most important differentiators.

Suggested structure:

scenarios/
│
├── scenario.controller.ts
├── scenario.service.ts
├── scenario.engine.ts
├── scenario.calculator.ts
├── scenario.validation.ts
├── scenario.repository.ts
└── scenario.types.ts
32. Scenario Engine Boundary

The Scenario Engine receives:

Current Financial State
+
Scenario Parameters
+
Relevant Goal Context

and produces:

Scenario Result

It must not modify:

Transactions
Goals
Budgets
Actual Financial Context

unless a future explicit user-confirmation workflow is introduced.

33. Scenario Engine Example

Input:

{
  "type": "CATEGORY_REDUCTION",
  "category": "Food",
  "changePercentage": -20
}

Current:

Food spending = ₹6,200

Simulation:

₹6,200 × 0.80 = ₹4,960

Potential difference:

₹1,240/month

The calculation is deterministic.

34. Insight Module

The Insight Module is responsible for determining what financial events deserve user attention.

Suggested structure:

insights/
│
├── insight.controller.ts
├── insight.service.ts
├── event.detector.ts
├── significance.engine.ts
├── insight.context.ts
├── insight.generator.ts
├── insight.repository.ts
└── insight.types.ts
35. Insight Engine Responsibilities

The Insight Engine should:

receive financial signals,
evaluate significance,
identify relevant context,
connect the event to goals/budgets,
construct structured insight context,
optionally request AI interpretation,
store the resulting insight.
36. Insight Significance Boundary

The Insight Engine decides:

"Is this worth showing?"

The AI decides:

"How should this be explained?"

This separation is fundamental.

Behaviour Signal
       ↓
Significance Engine
       ↓
Worth showing?
       ↓
      YES
       ↓
Insight Context
       ↓
AI
       ↓
Explanation
37. Personal Money Model Module

The Personal Money Model acts as a structured aggregation layer.

Suggested structure:

money-model/
│
├── money-model.service.ts
├── context.builder.ts
├── context.refresh.ts
├── context.repository.ts
└── money-model.types.ts

It may aggregate:

Transactions
Analytics
Behaviour Signals
Goals
Budgets
Financial Events

into:

Personal Financial Context
38. Money Model Dependency Graph
Transactions
      │
      ├──────────────┐
      ↓              ↓
 Analytics       Behaviour
      │              │
      └──────┬───────┘
             ↓
          Goals
             ↓
          Budgets
             ↓
      Personal Money Model
39. AI Advisor Module

Suggested structure:

advisor/
│
├── advisor.controller.ts
├── advisor.service.ts
├── context.selector.ts
├── prompt.builder.ts
├── response.processor.ts
├── advisor.types.ts
└── advisor.repository.ts
40. AI Provider Abstraction

The application should avoid calling an AI provider directly from controllers.

Instead:

Advisor Controller
       ↓
Advisor Service
       ↓
AI Provider Interface
       ↓
AI Provider Implementation

Conceptually:

AIProvider
   │
   ├── generate()
   ├── explain()
   └── chat()

A future provider can therefore replace the current provider without changing the rest of the application.

41. AI Context Selector

The Context Selector determines which financial information is relevant to a particular question.

Example:

User asks:

"Why did my savings fall this month?"

The selector may retrieve:

Current Income
Current Expenses
Current Savings
Previous Savings
Major Spending Changes
Relevant Goals

It should not automatically retrieve every transaction.

42. Prompt Builder

The Prompt Builder converts structured application data into a controlled AI request.

Conceptually:

System Instructions
        +
User Question
        +
Validated Financial Context
        +
Relevant Rules
        ↓
Prompt

The prompt builder should keep system instructions separate from untrusted user input.

43. Response Processor

The Response Processor handles AI output before returning it to the user.

Responsibilities may include:

response validation,
formatting,
metadata handling,
safety checks,
error handling.

AI output should be treated as untrusted external content.

44. AI Conversation Component

If conversational history is persisted, it should be isolated from the core financial data model.

Possible structure:

AI Conversation
│
├── userId
├── sessionId
├── messages
├── createdAt
└── metadata

The conversation should not automatically become part of the user's financial source of truth.

45. API Component Architecture

The API layer should follow:

Route
 ↓
Middleware
 ↓
Controller
 ↓
Service
 ↓
Repository
 ↓
Database

Example:

POST /api/v1/goals
        ↓
Auth Middleware
        ↓
Goal Controller
        ↓
Goal Service
        ↓
Goal Repository
        ↓
MongoDB
46. Middleware Components

Common middleware may include:

middleware/
│
├── authenticate.ts
├── authorize.ts
├── validate.ts
├── error-handler.ts
├── rate-limit.ts
└── request-logger.ts

Responsibilities:

authentication,
authorization,
request validation,
error normalization,
rate limiting,
safe logging.
47. Shared Domain Components

Some functionality is shared across modules.

Potential shared components:

shared/
│
├── errors/
├── constants/
├── types/
├── utils/
├── validation/
├── dates/
└── money/

The shared directory should remain small.

Business logic should not be placed here merely because it is convenient.

48. Money Utility

Because WealthWise deals with monetary values, common money operations should be centralized.

Potential responsibilities:

Money Parsing
Money Rounding
Currency Validation
Percentage Calculation
Amount Formatting

This reduces inconsistent monetary calculations across modules.

49. Date Utility

Financial calculations depend heavily on time periods.

A shared date utility may handle:

month boundaries,
date ranges,
period comparisons,
goal deadlines,
recurring periods.

All date calculations should use a consistent timezone strategy.

50. Component Dependency Rules

The following dependency rules should be maintained.

Rule 1

Controllers may call services.

Rule 2

Services may call repositories and domain components.

Rule 3

Repositories may access persistence.

Rule 4

Frontend components may call API clients.

Rule 5

Frontend components shall not directly access MongoDB.

Rule 6

AI components shall not directly access MongoDB without going through controlled application services.

Rule 7

The AI layer shall not become the source of truth for financial calculations.

Rule 8

Scenario calculations shall not mutate actual financial state.

51. Dependency Direction

The preferred dependency direction is:

Frontend
   ↓
API
   ↓
Application Services
   ↓
Domain / Intelligence
   ↓
Repositories
   ↓
Database

External services such as AI providers should be accessed through infrastructure abstractions.

52. Cross-Module Communication

Modules should communicate through explicit service interfaces rather than directly manipulating each other's database models.

Example:

Insight Service
      ↓
Analytics Service
      ↓
Structured Metrics

rather than:

Insight Service
      ↓
Direct MongoDB query into analytics collections

The first approach preserves business boundaries.

53. Example — Insight Generation

The full component interaction is:

Transaction Service
        ↓
Analytics Service
        ↓
Behaviour Service
        ↓
Goal Service
        ↓
Insight Service
        ↓
Significance Engine
        ↓
Context Builder
        ↓
Advisor / AI Service
        ↓
AI Provider
        ↓
Response Processor
        ↓
Insight Repository
54. Example — "Why Did I Spend More?"

User asks:

Why did I spend more this month?

Component flow:

React Advisor
      ↓
Advisor Controller
      ↓
Advisor Service
      ↓
Context Selector
      ↓
Analytics Service
      ↓
Behaviour Service
      ↓
Structured Context
      ↓
Prompt Builder
      ↓
AI Provider
      ↓
Response Processor
      ↓
React
55. Example — "Can I Afford This?"

User asks:

Can I afford a ₹10,000 phone?

The system should not simply ask the LLM.

Instead:

User Question
      ↓
Advisor
      ↓
Identify Purchase Scenario
      ↓
Scenario Engine
      ↓
Current Financial State
      ↓
Goal Impact
      ↓
Deterministic Result
      ↓
AI Explanation
      ↓
User

This is a key example of WealthWise's architecture being different from a generic chatbot.

56. Example — Spending Increase

Suppose:

Food Average = ₹4,000
Current Food = ₹6,000

The component flow is:

Transactions
      ↓
Analytics
      ↓
Behaviour Detector
      ↓
+50% Spending Signal
      ↓
Significance Engine
      ↓
Relevant Goal?
      ↓
Insight Context
      ↓
AI
      ↓
"Your food spending increased..."
57. Event-Driven Direction — Future

The MVP may use synchronous service calls.

Future versions could introduce domain events.

Example:

TransactionImported
        ↓
AnalyticsRefreshRequested
        ↓
BehaviourAnalysisRequested
        ↓
InsightEvaluationRequested

This should only be introduced when scale or processing complexity justifies it.

58. Current vs Future Architecture
MVP
React
  ↓
Express Modular Monolith
  ↓
MongoDB
  ↓
AI Provider
Future
Frontend
    ↓
API Gateway
    ↓
┌──────────┬───────────┬───────────┐
│Transaction│ Analytics │ AI        │
│Service    │ Service   │ Service   │
└──────────┴───────────┴───────────┘
             ↓
        Shared / Event Layer
             ↓
          Databases

The MVP should not prematurely implement the future architecture.

59. Component Failure Behaviour
Component	Failure Impact
Authentication	User cannot access protected system
Transaction Service	Transaction operations unavailable
Analytics	Financial summaries may be unavailable
Behaviour	Behaviour insights unavailable
Goals	Goal operations unavailable
Budgets	Budget features unavailable
Scenario Engine	Scenario analysis unavailable
Insight Engine	New insights unavailable
AI Service	AI explanations unavailable
MongoDB	Most persistent operations unavailable

Where possible, failure of one non-critical component should not unnecessarily disable unrelated functionality.

60. Observability Boundaries

Each major module should expose sufficient operational information to diagnose failures.

Example:

Request
 ↓
Controller
 ↓
Service
 ↓
Repository

Failures should be traceable without logging sensitive financial information.

61. Testing Boundaries

Each module should be testable independently.

Unit Tests
   ↓
Service Tests
   ↓
Repository Tests
   ↓
API Integration Tests
   ↓
End-to-End Tests

Financial calculators should have especially strong unit-test coverage.

62. Component Testing Strategy
Component	Primary Testing
Auth	Unit + Integration
Transactions	Unit + Integration
Import	Unit + Integration
Analytics	Extensive Unit Tests
Behaviour	Unit + Dataset Tests
Goals	Unit + Integration
Budgets	Unit + Integration
Scenario Engine	Extensive Unit Tests
Insight Engine	Unit + Evaluation
AI Advisor	Integration + Evaluation
API	Integration
Frontend	Component + E2E
63. Critical Architectural Boundaries

The following boundaries are considered critical:

                    ┌─────────────────┐
                    │    FRONTEND     │
                    └────────┬────────┘
                             │
                         API Boundary
                             │
                    ┌────────▼────────┐
                    │ APPLICATION     │
                    │ SERVICES        │
                    └──────┬───┬───────┘
                           │   │
              Deterministic│   │AI Boundary
                           │   │
                  ┌────────▼┐ ┌▼───────────┐
                  │ FINANCE │ │ AI SERVICE │
                  │  CORE   │ │            │
                  └────┬────┘ └────────────┘
                       │
                       ▼
                    MongoDB

The most important boundary is between the Financial Core and AI Service.

64. Financial Core

The Financial Core consists of:

Transactions
Analytics
Behaviour
Goals
Budgets
Scenarios

The Financial Core is responsible for authoritative financial state and calculations.

65. Intelligence Layer

The Intelligence Layer consists primarily of:

Behaviour Intelligence
Insight Engine
Personal Money Model
Scenario Engine

It transforms raw financial records into higher-level financial understanding.

66. AI Layer

The AI Layer consists of:

AI Advisor
Prompt Builder
Context Selector
Response Processor
AI Provider Adapter

It transforms structured financial understanding into natural-language interaction.

67. Complete Component Model
                              USER
                                │
                                ▼
                     ┌────────────────────┐
                     │   REACT FRONTEND   │
                     └─────────┬──────────┘
                               │
                               ▼
                     ┌────────────────────┐
                     │    REST API        │
                     └─────────┬──────────┘
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                    │
          ▼                    ▼                    ▼
   Transaction            Analytics            Auth
     Module                Module              Module
          │                    │
          │                    ▼
          │              Behaviour Module
          │                    │
          │             ┌──────┴──────┐
          │             ▼             ▼
          │          Goals         Budgets
          │             │             │
          └─────────────┴──────┬──────┘
                               ▼
                       Personal Money Model
                               │
                    ┌──────────┴──────────┐
                    ▼                     ▼
              Insight Engine        Scenario Engine
                    │                     │
                    └──────────┬──────────┘
                               ▼
                         AI Advisor
                               │
                               ▼
                         AI Provider

                               │
                               ▼
                           MongoDB
68. Architectural Summary

The WealthWise component architecture is intentionally designed around the product's unique intelligence loop.

The system progresses from:

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

The architecture ensures that each stage has a clear owner.

Most importantly:

WealthWise is not an LLM wrapped around an expense tracker.

The LLM sits at the end of a structured financial intelligence pipeline.