# WealthWise — Use Cases

**Document Version:** 1.0  
**Status:** Requirements Discovery  
**Related Documents:** Product Bible, User Stories, User Journey & Feature Map

---

# 1. Purpose

This document defines the major interactions between WealthWise users and the system.

Use cases describe:

- who interacts with the system,
- what they want to accomplish,
- what must already be true,
- the normal interaction flow,
- alternate flows,
- exceptions,
- and the resulting system state.

The use cases provide the bridge between:

User Stories → Functional Requirements → Implementation → Testing.

---

# 2. Actors

## 2.1 Primary Actor — Registered User

The person using WealthWise to:

- manage transactions,
- analyze finances,
- create goals,
- receive insights,
- simulate decisions,
- and interact with the AI Advisor.

---

## 2.2 AI Service

The generative AI component used for:

- transaction classification where applicable,
- financial insight generation,
- explanation,
- recommendation,
- conversational interaction.

The AI Service does not own financial calculations.

---

## 2.3 Financial Analysis Engine

The deterministic application component responsible for:

- aggregations,
- financial metrics,
- trends,
- goal calculations,
- projections,
- scenario calculations,
- behavioural signals.

---

## 2.4 Transaction Processing Service

Responsible for:

- file parsing,
- normalization,
- validation,
- duplicate detection,
- transaction classification,
- merchant extraction.

---

# 3. Use Case Identification

| ID | Use Case | Priority |
|---|---|---|
| UC-AUTH-001 | Register Account | P0 |
| UC-AUTH-002 | Authenticate User | P0 |
| UC-DATA-001 | Import Transactions | P0 |
| UC-DATA-002 | Add Transaction | P0 |
| UC-DATA-003 | Manage Transactions | P0 |
| UC-INTEL-001 | Categorize Transactions | P0 |
| UC-ANALYTICS-001 | Generate Financial Baseline | P0 |
| UC-ANALYTICS-002 | Analyze Spending Behaviour | P0 |
| UC-GOAL-001 | Create Financial Goal | P0 |
| UC-GOAL-002 | Evaluate Goal Feasibility | P0 |
| UC-INSIGHT-001 | Generate Financial Insight | P0 |
| UC-INSIGHT-002 | View Financial Insights | P0 |
| UC-SCENARIO-001 | Run Financial Scenario | P0 |
| UC-SCENARIO-002 | Compare Scenarios | P1 |
| UC-AI-001 | Ask AI Advisor | P1 |
| UC-AI-002 | Explain Financial Insight | P1 |
| UC-BUDGET-001 | Create and Monitor Budget | P1 |
| UC-DATA-004 | Export Financial Data | P1 |
| UC-SET-001 | Manage User Preferences | P1 |

---

# 4. UC-AUTH-001 — Register Account

## Objective

Allow a new user to create a WealthWise account.

## Primary Actor

Registered User

## Preconditions

- User does not already have an account using the supplied credentials.
- Registration service is available.

## Main Flow

1. User opens the registration page.
2. User enters required information.
3. User submits the registration form.
4. System validates the input.
5. System checks whether the account already exists.
6. System securely processes the password.
7. System creates the user account.
8. System confirms successful registration.
9. User is redirected to authentication or onboarding.

## Alternate Flows

### A1 — Existing Account

1. System detects an existing account.
2. Registration is rejected.
3. User is informed that the account already exists.

### A2 — Invalid Input

1. System detects invalid input.
2. Relevant fields are identified.
3. User is asked to correct them.

## Postconditions

- A valid user account exists.
- User can authenticate.

---

# 5. UC-AUTH-002 — Authenticate User

## Objective

Allow an existing user to securely access WealthWise.

## Primary Actor

Registered User

## Preconditions

- User has a valid account.

## Main Flow

1. User enters credentials.
2. User submits login request.
3. System validates credentials.
4. System creates an authenticated session/token.
5. User is granted access to protected resources.
6. User is redirected to the appropriate application page.

## Alternate Flow

### A1 — Invalid Credentials

1. System rejects authentication.
2. User receives an appropriate error.
3. User may retry.

## Postconditions

- Authenticated session exists.
- User can access only their own financial data.

---

# 6. UC-DATA-001 — Import Transactions

## Objective

Allow a user to import multiple financial transactions without entering them individually.

## Primary Actor

Registered User

## Supporting Actors

- Transaction Processing Service
- Financial Analysis Engine
- AI Service, where AI categorization is used

## Preconditions

- User is authenticated.
- File is in a supported format.
- File upload service is available.

## Main Flow

1. User opens Transactions.
2. User selects Import.
3. User selects a supported file.
4. System validates the file.
5. System parses transaction records.
6. System normalizes fields.
7. System checks for malformed records.
8. System checks for duplicates.
9. System classifies transactions.
10. System assigns categories where possible.
11. System extracts merchant information where possible.
12. System stores valid transactions.
13. System recalculates relevant financial metrics.
14. System reports the import result.

## Import Result

Example:

```text
312 transactions processed

287 imported
19 categorized automatically
6 require review
0 system errors