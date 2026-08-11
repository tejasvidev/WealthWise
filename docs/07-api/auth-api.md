# WealthWise — Authentication API

**Document Version:** 1.0  
**Status:** API Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**API Style:** REST  
**API Version:** v1  
**Authentication Model:** Token-Based Authentication  

---

# 1. Purpose

This document defines the authentication API for WealthWise.

It establishes the contracts for:

- user registration,
- user login,
- authentication verification,
- current-user retrieval,
- logout,
- authentication failures,
- authorization boundaries,
- validation,
- and authentication-related security behaviour.

Authentication is the first security boundary of the WealthWise application.

---

# 2. Authentication Architecture

The authentication flow is:

```text
User
 ↓
React Frontend
 ↓
Auth API
 ↓
Auth Controller
 ↓
Auth Service
 ↓
User Repository
 ↓
MongoDB

For authenticated requests:

Client
 ↓
Authentication Token
 ↓
Auth Middleware
 ↓
Authenticated User
 ↓
Protected Route
3. Authentication Principles

WealthWise authentication follows these principles:

1. Passwords are never stored in plaintext.
2. User identity is established server-side.
3. Authentication and authorization are separate concerns.
4. Financial resources are always user-scoped.
5. Authentication secrets are never exposed in API responses.
6. Client-provided user IDs are never trusted for authorization.
4. Authentication Endpoints

The initial authentication API consists of:

Method	Endpoint	Purpose
POST	/api/v1/auth/register	Create account
POST	/api/v1/auth/login	Authenticate user
POST	/api/v1/auth/logout	End authenticated session
GET	/api/v1/auth/me	Retrieve authenticated user
5. Registration
Endpoint
POST /api/v1/auth/register
Purpose

Creates a new WealthWise account.

Request
{
  "name": "Ricky",
  "email": "user@example.com",
  "password": "secure-password"
}
6. Registration Fields
Field	Type	Required	Description
name	String	Yes	User's display name
email	String	Yes	Login email
password	String	Yes	Plaintext password sent over HTTPS

The plaintext password must never be stored.

7. Registration Validation

The API must validate:

name
email
password

Validation includes:

required fields,
valid email format,
password policy,
acceptable name length,
duplicate email detection.

The exact password complexity policy will be finalized during implementation.

8. Registration Flow
Registration Request
        ↓
Validate Input
        ↓
Normalize Email
        ↓
Check Existing User
        ↓
Hash Password
        ↓
Create User
        ↓
Generate Authentication State
        ↓
Return Safe User Data
9. Duplicate Email

If the email already exists:

HTTP 409 Conflict

Conceptual response:

{
  "success": false,
  "error": {
    "code": "EMAIL_ALREADY_EXISTS",
    "message": "An account already exists for this email."
  }
}
10. Registration Success

A successful registration returns:

HTTP 201 Created

Conceptual response:

{
  "success": true,
  "data": {
    "user": {
      "id": "user-id",
      "name": "Ricky",
      "email": "user@example.com",
      "currency": "INR"
    }
  }
}

Authentication credentials must not be included as ordinary user data.

11. Login
Endpoint
POST /api/v1/auth/login
Purpose

Authenticates an existing WealthWise user.

12. Login Request
{
  "email": "user@example.com",
  "password": "secure-password"
}
13. Login Flow
Login Request
      ↓
Validate Input
      ↓
Find User
      ↓
Verify Password
      ↓
Create Authentication State
      ↓
Return Authenticated User
14. Invalid Credentials

Invalid credentials should return:

HTTP 401 Unauthorized

Conceptual response:

{
  "success": false,
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Invalid email or password."
  }
}

The API should avoid revealing whether:

the email exists

or:

the password was incorrect

as separate information.

15. Authentication Token

The implementation may use a signed authentication token.

Conceptually:

Client
  ↓
Login
  ↓
Authentication Token
  ↓
Protected Requests

The exact token storage strategy will be finalized with the security implementation.

16. Token Payload

If JWT-based authentication is used, the payload should contain only the minimum required identity information.

Conceptually:

{
  "sub": "user-id",
  "iat": 1234567890,
  "exp": 1234567890
}

Sensitive financial information must never be embedded in the token.

17. Protected Request

Authenticated requests conceptually use:

Authorization: Bearer <token>

The authentication middleware:

Token
 ↓
Verify Signature
 ↓
Check Expiration
 ↓
Extract User Identity
 ↓
Attach Authenticated User
 ↓
Continue Request
18. Authentication Middleware

The middleware is responsible for:

extracting authentication credentials,
validating the token,
identifying the user,
rejecting invalid authentication,
attaching authenticated identity to the request.

It must not perform financial business logic.

19. Authentication Failure

Missing or invalid authentication:

HTTP 401 Unauthorized

Conceptual response:

{
  "success": false,
  "error": {
    "code": "AUTHENTICATION_REQUIRED",
    "message": "Authentication is required."
  }
}
20. Expired Authentication

An expired authentication token should produce:

HTTP 401 Unauthorized

The frontend can then initiate the appropriate re-authentication flow.

21. Current User
Endpoint
GET /api/v1/auth/me
Purpose

Returns the currently authenticated user's safe account information.

22. Current User Request

No request body is required.

Authentication is derived from the request credentials.

23. Current User Response
{
  "success": true,
  "data": {
    "user": {
      "id": "user-id",
      "name": "Ricky",
      "email": "user@example.com",
      "currency": "INR",
      "preferences": {
        "timezone": "Asia/Kolkata",
        "dateFormat": "DD/MM/YYYY"
      }
    }
  }
}
24. Sensitive User Fields

The following fields must never be included in normal user responses:

passwordHash
authentication secrets
internal security metadata
database credentials
25. Logout
Endpoint
POST /api/v1/auth/logout

The logout mechanism depends on the selected authentication strategy.

If server-managed sessions are used:

Session
 ↓
Invalidate

If stateless access tokens are used, the client removes the authentication state and any server-side refresh mechanism is invalidated where applicable.

The final mechanism will be established in the security architecture.

26. Logout Response

Conceptually:

HTTP 204 No Content

or:

{
  "success": true,
  "message": "Logged out successfully."
}

The final convention will be standardized during implementation.

27. Authorization

Authentication establishes:

Who is the user?

Authorization establishes:

What can the user access?

Example:

Authenticated User A
        ↓
Transaction ID
        ↓
Does Transaction belong to User A?
        ↓
Yes → Allow
No  → Reject
28. Ownership Enforcement

All financial resource queries must use the authenticated identity.

Correct:

{
  _id: resourceId,
  userId: authenticatedUserId
}

Incorrect:

{
  _id: resourceId
}
29. Client User ID Rule

The client must not determine the authenticated identity by sending:

{
  "userId": "another-user-id"
}

The server determines identity from authentication state.

30. User Isolation

The following resources are user-owned:

Transactions
Goals
Budgets
Insights
Scenarios
Financial Context
AI Conversations

A user must never be able to access another user's records.

31. Registration Security

Registration must protect against:

duplicate accounts,
malformed input,
weak credentials,
injection attempts,
excessive requests.

Rate limiting may be applied to registration.

32. Login Security

Login should include:

rate limiting,
secure password comparison,
generic invalid-credential responses,
secure transport,
protection against brute-force attempts.
33. Password Storage

Passwords must be processed as:

Plain Password
      ↓
Password Hashing Algorithm
      ↓
Password Hash
      ↓
MongoDB

Never:

Plain Password
      ↓
MongoDB

The specific hashing library and configuration will be finalized during implementation.

34. Password Reset

Password recovery is not required for the first documented MVP authentication contract.

If introduced later, it may include:

POST /api/v1/auth/forgot-password
POST /api/v1/auth/reset-password

These endpoints should be added only with a defined secure token and expiration mechanism.

35. Email Verification

Email verification is also optional for the initial MVP.

If introduced later:

POST /api/v1/auth/verify-email
POST /api/v1/auth/resend-verification

may be added.

36. Authentication Rate Limits

Authentication endpoints should have stricter rate limits than ordinary read endpoints.

Potentially protected operations:

/register
/login
/forgot-password
/reset-password

Exact limits will be defined during implementation.

37. Authentication Logging

Security-relevant events may be logged:

Registration attempt
Successful login
Failed login
Logout
Password reset

Logs must not contain:

Passwords
Tokens
Authentication secrets
38. Authentication Errors

Initial error codes:

AUTHENTICATION_REQUIRED
INVALID_CREDENTIALS
TOKEN_INVALID
TOKEN_EXPIRED
EMAIL_ALREADY_EXISTS
VALIDATION_ERROR
RATE_LIMIT_EXCEEDED
39. Authentication Flow

Complete login flow:

                 LOGIN
                   │
                   ▼
             Validate Input
                   │
                   ▼
             Find User
                   │
                   ▼
          Verify Password
                   │
              ┌────┴────┐
              │         │
            Valid     Invalid
              │         │
              ▼         ▼
        Create Auth    401
          State
              │
              ▼
       Return Safe User
              │
              ▼
          Frontend
40. Protected API Flow
Frontend Request
      ↓
Authentication Credential
      ↓
Auth Middleware
      ↓
Authenticated User
      ↓
Authorization
      ↓
Controller
      ↓
Service
      ↓
Repository
41. Authentication and AI

Authentication also protects AI functionality.

A request to:

POST /api/v1/advisor/chat

must be associated with the authenticated user.

The AI Advisor must never receive another user's financial context.

42. Authentication and Financial Data

Authentication is the first layer of financial-data protection.

The complete protection chain is:

Authentication
      ↓
Authorization
      ↓
User Ownership
      ↓
Service Validation
      ↓
Repository Query
      ↓
MongoDB
43. Security Boundary

The API must never expose:

Database credentials
AI provider credentials
Password hashes
Internal stack traces
Raw database errors
Authentication secrets
44. API Contract Summary
POST /api/v1/auth/register
    → Create account

POST /api/v1/auth/login
    → Authenticate account

POST /api/v1/auth/logout
    → End authentication state

GET /api/v1/auth/me
    → Retrieve current authenticated user