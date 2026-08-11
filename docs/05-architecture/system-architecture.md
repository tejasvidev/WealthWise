# WealthWise — System Architecture

**Document Version:** 1.0  
**Status:** Architecture Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Technology Direction:** MERN + Generative AI

---

# 1. Purpose

This document defines the high-level technical architecture of WealthWise.

The architecture translates the requirements defined in the Product Bible, User Stories, Use Cases, Functional Requirements, and Non-Functional Requirements into a concrete system structure.

The architecture is designed around the central WealthWise principle:

> **Deterministic systems understand the numbers. AI helps the user understand what the numbers mean.**

Therefore, WealthWise shall not be designed as a simple CRUD expense tracker with an LLM attached to it.

The architecture separates:

- financial data management,
- deterministic financial computation,
- behavioural intelligence,
- goal intelligence,
- scenario simulation,
- insight generation,
- and generative AI.

---

# 2. Architectural Goals

The architecture shall achieve the following goals.

## 2.1 Financial Correctness

Financial calculations must be deterministic, reproducible, and testable.

---

## 2.2 Intelligence

The system must be capable of transforming raw transactions into meaningful financial signals.

---

## 2.3 Explainability

AI-generated explanations should be grounded in validated financial information.

---

## 2.4 Modularity

Major WealthWise capabilities should remain independently understandable and maintainable.

---

## 2.5 Security

User financial information must remain isolated and protected.

---

## 2.6 Scalability

The architecture should support growth beyond the initial MVP without requiring a complete redesign.

---

## 2.7 AI Independence

Core financial functionality must continue to operate even when the AI service is unavailable.

---

## 2.8 Testability

Financial calculations and business rules should be testable independently from the frontend and AI provider.

---

# 3. Architectural Principle

The fundamental architectural principle is:

```text
                RAW DATA
                   ↓
          DETERMINISTIC LOGIC
                   ↓
          STRUCTURED INSIGHTS
                   ↓
              AI LAYER
                   ↓
        HUMAN-UNDERSTANDABLE
            EXPLANATION

5. Architectural Layers

WealthWise is divided into the following logical layers.

Presentation Layer
        ↓
API Layer
        ↓
Application Layer
        ↓
Domain / Intelligence Layer
        ↓
Data Access Layer
        ↓
Persistence Layer

External AI is accessed through
the AI Service boundary.
6. Presentation Layer
Technology
React

The frontend is responsible for:

rendering the user interface,
collecting user input,
displaying financial analytics,
presenting insights,
displaying scenario results,
communicating with the backend API.

The frontend shall not be responsible for authoritative financial calculations.

7. Frontend Modules

The React application should be organized into major feature areas.

frontend/
│
├── auth/
│
├── dashboard/
│
├── transactions/
│
├── analytics/
│
├── goals/
│
├── budgets/
│
├── insights/
│
├── scenarios/
│
├── advisor/
│
└── settings/

The exact implementation structure may evolve during frontend architecture.

8. API Layer

The API layer acts as the boundary between the frontend and backend application services.

Responsibilities include:

request routing,
authentication,
authorization,
request validation,
response formatting,
error handling.

The API layer should not contain large amounts of financial business logic.

9. Application Layer

The application layer coordinates business operations.

Major services include:

Transaction Service
Analytics Service
Behaviour Service
Goal Service
Budget Service
Scenario Service
Insight Service
AI Advisor Service
Personal Money Model Service

Each service should expose clear responsibilities.

10. Transaction Service

The Transaction Service manages financial transaction operations.

Responsibilities:

create transaction,
update transaction,
delete transaction,
retrieve transactions,
search transactions,
filter transactions,
import transactions,
validate transaction data,
normalize imported data,
detect potential duplicates.

It acts as the primary gateway for transaction-related business operations.

11. Transaction Processing Pipeline

Imported transactions follow:

Uploaded File
      ↓
File Validation
      ↓
Parsing
      ↓
Normalization
      ↓
Duplicate Detection
      ↓
Transaction Classification
      ↓
Category Assignment
      ↓
Validation
      ↓
Persistence
      ↓
Analytics Refresh
12. Analytics Service

The Analytics Service performs deterministic financial calculations.

Responsibilities include:

income calculation,
expense calculation,
savings calculation,
savings-rate calculation,
category spending,
category distribution,
merchant spending,
historical averages,
monthly trends.

Example:

Income
  +
Expenses
  ↓
Savings
  ↓
Savings Rate

The Analytics Service is one of the most important sources of truth in WealthWise.

13. Behaviour Intelligence Service

The Behaviour Intelligence Service converts historical financial data into structured behavioural signals.

Responsibilities include:

establishing behavioural baselines,
detecting spending drift,
detecting spending spikes,
detecting spending reductions,
comparing current behaviour with historical behaviour,
generating structured behavioural signals.

Example:

Historical Dining Average
          ↓
       ₹3,900
          │
          ▼
Current Dining Spending
          ↓
       ₹6,200
          │
          ▼
Behaviour Signal
          ↓
     +59% Increase

The service should produce structured data rather than natural-language explanations.

14. Goal Service

The Goal Service manages financial objectives.

Responsibilities:

create goals,
update goals,
delete goals,
calculate goal progress,
calculate remaining amount,
calculate required contribution,
evaluate goal feasibility,
determine goal status,
identify goal risk.

Example:

Goal
 ↓
Target Amount
 ↓
Current Amount
 ↓
Remaining Amount
 ↓
Required Contribution
 ↓
Savings Capacity
 ↓
Goal Feasibility
15. Budget Service

The Budget Service manages user-defined spending limits.

Responsibilities:

create budgets,
update budgets,
delete budgets,
calculate budget usage,
calculate remaining budget,
evaluate budget status,
estimate budget trajectory.
16. Scenario Engine

The Scenario Engine is a deterministic simulation component.

It answers:

"What could happen if this financial variable changed?"

Examples:

Reduce dining by 20%
Increase monthly savings by ₹2,000
Increase income by ₹5,000
Make a ₹10,000 purchase
Move a goal deadline

The Scenario Engine must not modify the user's actual financial state.

17. Scenario Architecture
                  CURRENT FINANCIAL STATE
                           │
                           ▼
                    Scenario Input
                           │
                           ▼
                  ┌─────────────────┐
                  │ Scenario Engine │
                  └────────┬────────┘
                           │
                           ▼
                  Temporary State
                           │
                           ▼
                 Recalculate Metrics
                           │
                           ▼
                  Goal Impact Analysis
                           │
                           ▼
                    Scenario Result
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
       User Interface             AI Explanation

The original financial state remains unchanged.

18. Insight Engine

The Insight Engine transforms structured financial signals into meaningful events.

It sits between financial analysis and generative AI.

Analytics
    ↓
Behaviour Signals
    ↓
Goal/Budget Context
    ↓
Insight Significance
    ↓
Insight Context
    ↓
AI Interpretation

The Insight Engine determines whether something is worth telling the user.

This distinction is important.

The AI should not independently decide that every unusual transaction deserves an insight.

19. Insight Generation Architecture
Transactions
     ↓
Analytics
     ↓
Behaviour Detection
     ↓
Financial Event
     ↓
Significance Evaluation
     ↓
Goal/Budget Impact
     ↓
Structured Insight Context
     ↓
AI Service
     ↓
Natural Language Insight
20. Personal Money Model

The Personal Money Model is a central WealthWise concept.

It represents the structured understanding WealthWise has about a user's financial behaviour.

It is not simply a database collection.

It is a derived representation built from multiple sources.

Transactions
     +
Analytics
     +
Behaviour
     +
Goals
     +
Budgets
     +
Scenarios
     ↓
Personal Money Model

The Personal Money Model becomes the context layer used by:

Insight Engine,
Scenario Engine,
AI Advisor,
Dashboard,
goal analysis.
21. Personal Money Model Components

The model may contain:

Financial Profile
      │
      ├── Income Pattern
      ├── Expense Pattern
      ├── Savings Pattern
      ├── Category Behaviour
      ├── Recurring Commitments
      ├── Behaviour Signals
      ├── Financial Goals
      ├── Budgets
      └── Relevant Financial Events
22. AI Advisor Service

The AI Advisor Service provides natural-language financial interaction.

Its responsibilities include:

receiving user questions,
identifying relevant financial context,
constructing AI context,
calling the AI provider,
processing the response,
returning an understandable answer.

The AI Advisor does not own financial calculations.

23. AI Architecture Principle

The AI architecture follows:

User Question
      ↓
Intent / Context Identification
      ↓
Relevant Data Retrieval
      ↓
Deterministic Financial Calculations
      ↓
Structured Context
      ↓
Prompt Construction
      ↓
AI Provider
      ↓
AI Response
      ↓
Validation / Processing
      ↓
User
24. AI Context Boundary

The system should avoid sending the complete user dataset to the AI provider for every question.

Instead:

User asks:

"Why did I save less this month?"
             ↓
Retrieve relevant metrics
             ↓
Income
Expenses
Savings
Historical savings
Major spending changes
Relevant goals
             ↓
AI Context

Only the relevant context should be provided.

25. AI Source-of-Truth Boundary

The following components shall remain deterministic:

Income Calculation
Expense Calculation
Savings Calculation
Savings Rate
Goal Progress
Goal Gap
Required Contribution
Scenario Calculation
Budget Usage
Historical Metrics

The AI may explain them.

It should not replace them.

26. Data Layer

The Data Access Layer isolates business logic from database-specific operations.

Conceptually:

Application Service
        ↓
Repository / Data Access Layer
        ↓
MongoDB

This prevents database queries from being scattered throughout the application.

27. Persistence Layer

MongoDB is the initial persistence technology.

Potential collections include:

users
transactions
goals
budgets
insights
scenarios
financialContexts
aiConversations

The final schema will be defined in the Database Architecture document.

28. Authentication Flow
User
 ↓
Login Form
 ↓
React
 ↓
POST /api/v1/auth/login
 ↓
Express API
 ↓
Authentication Service
 ↓
User Repository
 ↓
Credential Verification
 ↓
Authentication Token / Session
 ↓
React
 ↓
Authenticated Application
29. Transaction Creation Flow
User
 ↓
Add Transaction
 ↓
React
 ↓
POST /api/v1/transactions
 ↓
Authentication
 ↓
Validation
 ↓
Transaction Service
 ↓
Classification / Enrichment
 ↓
Transaction Repository
 ↓
MongoDB
 ↓
Analytics Refresh
 ↓
Response
 ↓
React
30. Transaction Import Flow
User
 ↓
Select CSV
 ↓
React
 ↓
POST /api/v1/transactions/import
 ↓
Authentication
 ↓
File Validation
 ↓
Parser
 ↓
Normalizer
 ↓
Duplicate Detection
 ↓
Classification
 ↓
Transaction Service
 ↓
MongoDB
 ↓
Analytics Refresh
 ↓
Import Summary
 ↓
React
31. Financial Insight Flow
Transaction Data
       ↓
Analytics Service
       ↓
Behaviour Service
       ↓
Goal Service
       ↓
Insight Engine
       ↓
Significance Evaluation
       ↓
Structured Insight Context
       ↓
AI Advisor Service
       ↓
AI Provider
       ↓
Generated Explanation
       ↓
Insight Validation
       ↓
Insight Repository
       ↓
React Dashboard
32. Scenario Flow
User
 ↓
Scenario Interface
 ↓
Scenario API
 ↓
Scenario Service
 ↓
Retrieve Financial Baseline
 ↓
Create Temporary Simulation State
 ↓
Scenario Engine
 ↓
Calculate Financial Impact
 ↓
Calculate Goal Impact
 ↓
Scenario Result
 ↓
React

Optional:

Scenario Result
      ↓
AI Advisor
      ↓
Explanation
33. AI Advisor Flow
User Question
      ↓
React
      ↓
POST /api/v1/advisor/chat
      ↓
Authentication
      ↓
AI Advisor Service
      ↓
Identify Required Context
      ↓
Retrieve Financial Data
      ↓
Deterministic Calculations
      ↓
Structured AI Context
      ↓
Prompt Construction
      ↓
AI Provider
      ↓
Response
      ↓
Response Processing
      ↓
React
34. Error Boundary Architecture

Errors should be handled at appropriate layers.

Frontend Error
      ↓
API Error
      ↓
Application Error
      ↓
Domain Error
      ↓
Database / External Service Error

Errors should be converted into safe, understandable API responses.

Internal implementation details shall not be exposed to users.

35. AI Failure Architecture

The AI service is intentionally treated as a non-critical dependency.

                 WealthWise
                    │
        ┌───────────┴───────────┐
        │                       │
 Deterministic Core          AI Layer
        │                       │
        ↓                       ↓
 Transactions              Explanation
 Analytics                 Recommendations
 Goals                     Conversation
 Scenarios

If AI becomes unavailable:

Transactions       ✓
Analytics          ✓
Goals              ✓
Budgets            ✓
Scenarios          ✓
Dashboard          ✓

AI Advisor         ✗
AI Explanation     ✗

The user should still be able to use the financial system.

36. Security Architecture

Security is enforced through multiple layers.

                    USER
                      ↓
                HTTPS / TLS
                      ↓
              Authentication
                      ↓
               Authorization
                      ↓
             API Validation
                      ↓
             Business Services
                      ↓
            User-Scoped Queries
                      ↓
                   MongoDB

No single security mechanism should be considered sufficient by itself.

37. User Data Isolation Architecture

Every protected resource must be scoped to the authenticated user.

Conceptually:

Request
   ↓
Authenticated User ID
   ↓
Service Layer
   ↓
Repository Query
   ↓
{
    userId: authenticatedUserId
}
   ↓
MongoDB

The system should avoid queries that retrieve financial resources without applying appropriate ownership constraints.

38. Financial Data Flow

The primary data flow is:

                  TRANSACTION DATA
                         │
                         ▼
                Transaction Service
                         │
                         ▼
                  MongoDB Storage
                         │
             ┌───────────┴───────────┐
             ▼                       ▼
       Analytics Service       Intelligence
             │                       │
             ▼                       ▼
       Financial Metrics       Behaviour Signals
             │                       │
             └───────────┬───────────┘
                         ▼
                 Personal Money Model
                         │
             ┌───────────┼────────────┐
             ▼           ▼            ▼
          Goals       Insights     Scenarios
             │           │            │
             └───────────┼────────────┘
                         ▼
                    AI Advisor
                         │
                         ▼
                  User Understanding
39. Architectural Separation of Concerns

The following boundaries shall be maintained.

Responsibility	Responsible Component
UI Rendering	React
Routing	Express
Authentication	Auth Service
Transaction CRUD	Transaction Service
Import Processing	Transaction Service / Import Processor
Financial Calculations	Analytics Service
Behaviour Detection	Behaviour Service
Goal Calculations	Goal Service
Budget Calculations	Budget Service
Scenario Calculations	Scenario Engine
Insight Detection	Insight Engine
Natural Language	AI Advisor
Persistence	Repository/Data Layer
Database	MongoDB
40. What the AI Should NOT Do

The AI layer should not directly:

calculate authoritative financial totals,
modify transactions,
modify goals,
modify budgets,
execute payments,
access unrestricted database records,
make autonomous financial transactions.

Instead:

AI
 ↓
Suggest / Explain
 ↓
Application Logic
 ↓
Validate
 ↓
User Action
 ↓
Persist
41. Architecture for User-Initiated Actions

AI-generated suggestions that lead to changes should follow:

AI Recommendation
       ↓
User Reviews
       ↓
User Explicitly Chooses Action
       ↓
Frontend Request
       ↓
API Validation
       ↓
Business Logic
       ↓
Database

This prevents the AI from becoming an uncontrolled application actor.

42. Technology Stack

The initial technology direction is:

Frontend
React
JavaScript / TypeScript
HTML
CSS
Backend
Node.js
Express
JavaScript / TypeScript
Database
MongoDB
AI
Generative AI / LLM

The specific provider and model will be selected during AI architecture.

API
REST
JSON
Version Control
Git
GitHub
43. Initial Deployment Model

The MVP can use a relatively simple deployment architecture.

                    Internet
                       │
             ┌─────────┴─────────┐
             │                   │
             ▼                   ▼
        Frontend Host        Backend Host
             │                   │
             │              Express API
             │                   │
             │          ┌────────┴────────┐
             │          │                 │
             │          ▼                 ▼
             │       MongoDB          AI Provider
             │
             └──────────────────────────────┘

The exact hosting providers will be selected later.

44. Future Scalability Direction

The initial architecture should allow eventual evolution toward:

                    API Gateway
                         │
       ┌─────────────────┼──────────────────┐
       │                 │                  │
 Transaction         Analytics           AI
 Service             Service             Service
       │                 │                  │
       └─────────────────┼──────────────────┘
                         │
                  Shared Data Layer
                         │
                      MongoDB

At larger scale, computationally expensive operations may be moved to asynchronous workers.

45. Asynchronous Processing — Future

Large operations may eventually use:

User Request
      ↓
Job Queue
      ↓
Background Worker
      ↓
Processing
      ↓
Database
      ↓
Notification / Status

Potential candidates:

large CSV imports,
historical analytics,
insight generation,
batch categorization,
scheduled financial analysis.

This is not required for the initial MVP unless performance testing demonstrates the need.

46. Architectural Decision Principles

The following principles guide technical decisions.

Decision 1 — Deterministic First

If a financial value can be calculated deterministically, use deterministic logic.

Decision 2 — AI for Interpretation

Use AI where natural-language understanding, explanation, synthesis, or recommendation provides genuine value.

Decision 3 — Database Is Not Business Logic

Database queries should not contain the majority of financial business rules.

Decision 4 — Frontend Is Not Source of Truth

The frontend shall not be the authoritative source for financial calculations or authorization.

Decision 5 — User Data Is Scoped

Every financial operation must operate within the authenticated user's scope.

Decision 6 — Scenario State Is Temporary

Hypothetical scenarios must not mutate actual financial records.

Decision 7 — AI Is Replaceable

The core system should not become structurally dependent on one AI provider.

47. Architectural Risks
Risk	Impact	Mitigation
AI hallucination	High	Deterministic financial source of truth
Financial calculation error	High	Centralized calculation services + tests
Data leakage	Critical	User-scoped authorization
AI provider outage	Medium	AI treated as non-core dependency
Large import performance	Medium	Streaming/background processing if required
Database growth	Medium	Indexing + modular data access
Over-engineering MVP	High	Start with modular monolith
Tight AI coupling	Medium	AI service abstraction
Complex behaviour detection	Medium	Start with explainable rules
Excessive AI cost	Medium	Deterministic preprocessing + selective AI calls
48. Recommended MVP Architecture

WealthWise should initially use a modular monolith rather than microservices.

┌─────────────────────────────────────────────┐
│              Express Backend                │
│                                             │
│  Auth Module                                │
│  Transaction Module                         │
│  Analytics Module                           │
│  Behaviour Module                           │
│  Goal Module                                │
│  Budget Module                              │
│  Scenario Module                            │
│  Insight Module                             │
│  AI Module                                  │
│                                             │
└──────────────────────┬──────────────────────┘
                       │
                    MongoDB
                       │
                  AI Provider

This provides modularity without introducing unnecessary distributed-system complexity.

49. Why Modular Monolith?

For the major-project MVP, a modular monolith provides:

simpler development,
simpler deployment,
easier debugging,
easier local development,
lower infrastructure complexity,
clear module boundaries,
easier testing.

The architecture can later extract individual modules into services if scale requires it.

50. Architectural Quality Boundary

The most important boundary in WealthWise is:

┌──────────────────────────────────────┐
│        DETERMINISTIC CORE            │
│                                      │
│ Transactions                         │
│ Analytics                            │
│ Goals                               │
│ Budgets                             │
│ Scenarios                           │
│ Behaviour Signals                   │
└──────────────────┬───────────────────┘
                   │
             Structured Context
                   │
                   ▼
┌──────────────────────────────────────┐
│             AI LAYER                 │
│                                      │
│ Explanation                          │
│ Natural Language                     │
│ Recommendation                       │
│ Conversation                         │
└──────────────────────────────────────┘

This is the architectural foundation of WealthWise.

51. Architecture Summary

The WealthWise architecture transforms:

Raw Transactions

into:

Structured Financial Data

then:

Financial Understanding

then:

Behavioural Intelligence

then:

Goal-Aware Context

then:

Scenario-Based Decision Support

and finally:

AI-Assisted Human Understanding

The system therefore operates as:

DATA
 ↓
ANALYSIS
 ↓
INTELLIGENCE
 ↓
CONTEXT
 ↓
SIMULATION
 ↓
EXPLANATION
 ↓
DECISION