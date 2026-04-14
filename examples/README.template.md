# User Authentication

> **Status**: Draft | Review | Approved  
> **Last Updated**: YYYY-MM-DD  
> **Owner**: {Team or Author}

---

## Overview

The User Authentication module provides secure account lifecycle and access-control functionality for the application. It solves the problem of safely onboarding and identifying users by centralizing registration, login, logout, password reset, and session management while enforcing security constraints (password hashing, account lockout, token expiry). This module is consumed by client applications (web and mobile), API clients, and backend services that need a verified user context to authorize requests and personalize behavior.

---

## Actors

| Actor           | Role                                                     |
| --------------- | -------------------------------------------------------- |
| Guest           | Unauthenticated user interacting with the system         |
| Registered User | Authenticated user with a valid account                  |
| System          | Automated processes (email delivery, token expiry, etc.) |

---

## Use Cases (Summary)

| UC-ID | Name             | Primary Actor          | Short Description                                     |
| ----- | ---------------- | ---------------------- | ----------------------------------------------------- |
| UC-01 | Register Account | Guest                  | A guest creates a new account with email and password |
| UC-02 | Log In           | Guest, Registered User | A user authenticates and receives a session token     |
| UC-03 | Log Out          | Registered User        | A user invalidates their active session               |
| UC-04 | Reset Password   | Guest                  | A user requests a password reset link via email       |

---

## Use Case Descriptions

Provide a detailed table for each critical use case. This stores the precondition, main flow (2–6 steps), postcondition, and exceptions — information that cannot be represented inside `.puml` files.

| UC-ID | Name             | Actor(s) | Precondition                 | Main Flow                                                                                                           | Postcondition                      | Exceptions           |
| ----- | ---------------- | -------- | ---------------------------- | ------------------------------------------------------------------------------------------------------------------- | ---------------------------------- | -------------------- |
| UC-01 | Register Account | Guest    | Email not already registered | 1. User provides email and password.\n2. System validates input.\n3. System creates account and sends confirmation. | Account created, confirmation sent | Email already in use |

---

## Flows

Describe the main flows in plain language. One subsection per critical use case. Keep to the "what", not the "how".

### UC-02: Log In

1. User submits email and password.
2. System validates input format.
3. System checks credentials against the stored account.
4. On success, system issues a session token.
5. On failure, system returns an appropriate error.

---

## Domain Entities

| Entity        | Responsibility                             |
| ------------- | ------------------------------------------ |
| User          | Holds account identity and credential data |
| Session       | Represents an active authenticated session |
| PasswordReset | Tracks a pending password reset request    |

---

## Constraints & Rules

- Passwords must be hashed before storage. Plain-text passwords are never persisted.
- A session expires after a defined inactivity period.
- Accounts are temporarily locked after a threshold of consecutive failed login attempts.
- Password reset tokens are single-use and time-limited.

---

## Out of Scope

- OAuth / third-party social login
- Two-factor authentication
- User profile management

---

## Diagrams

| Diagram  | File                     |
| -------- | ------------------------ |
| Use Case | `diagrams/usecase.puml`  |
| Activity | `diagrams/activity.puml` |
| Sequence | `diagrams/sequence.puml` |
| ERD      | `diagrams/erd.puml`      |
| Class    | `diagrams/class.puml`    |
