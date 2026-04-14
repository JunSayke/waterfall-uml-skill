# {Module Name}

> **Status**: Draft | Review | Approved  
> **Last Updated**: YYYY-MM-DD  
> **Owner**: {Team or Author}

---

## Overview

One paragraph. What is this module? What problem does it solve? Who uses it?

---

## Actors

| Actor | Role |
|-------|------|
| Guest | Unauthenticated user interacting with the system |
| Registered User | Authenticated user with a valid account |
| System | Automated processes (email delivery, token expiry, etc.) |

---

## Use Cases

| UC-ID | Name | Primary Actor | Description |
|-------|------|---------------|-------------|
| UC-01 | Register Account | Guest | A guest creates a new account with email and password |
| UC-02 | Log In | Guest, Registered User | A user authenticates and receives a session token |
| UC-03 | Log Out | Registered User | A user invalidates their active session |
| UC-04 | Reset Password | Guest | A user requests a password reset link via email |

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

| Entity | Responsibility |
|--------|---------------|
| User | Holds account identity and credential data |
| Session | Represents an active authenticated session |
| PasswordReset | Tracks a pending password reset request |

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

| Diagram | File |
|---------|------|
| Use Case | `diagrams/usecase.puml` |
| Activity | `diagrams/activity.puml` |
| Sequence | `diagrams/sequence.puml` |
| ERD | `diagrams/erd.puml` |
| Class | `diagrams/class.puml` |
