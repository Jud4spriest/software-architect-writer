# Feature Spec Template

```markdown
*Last Updated: YYYY-MM-DD*
---
# Feature Name

## Overview
One paragraph. What this feature does and why it exists. Include the business reason.

## Behavior
Describe every action this feature performs. For each action:
- **Input:** what is received
- **Output:** what is returned
- **Side effects:** any state changes, events emitted, or external calls triggered
- **Errors:** each possible error, its condition, and the response code/message

## Business Rules
Invariants and constraints that must always hold, regardless of input. These are the non-negotiable rules the implementation must never violate.
- Rule 1
- Rule 2

## Edge Cases
Non-obvious situations and how each is handled.

## Implementation Map
A map of where this feature lives in the codebase. Enough to orient an agent without reading the source.

**Components**
- `ComponentName` (`path/to/file.ts`) — Single-sentence responsibility.

**Database Tables**
- `table_name` — Purpose. Key fields: `field_1`, `field_2`.

**External Integrations**
- `ServiceName` — How and why it is used.

## Configuration
Environment variables and feature flags this feature depends on.
- `ENV_VAR_NAME` — What it controls. Required/optional, default value.

## Dependencies
Other features, services, or libraries this feature depends on.

## Diagrams
Link or embed a Mermaid.js diagram or PNG here.
```

---

# Worked Example: `auth-spec.md`

```markdown
*Last Updated: 2026-04-13*
---
# Authentication

## Overview
Handles user identity verification via JWT tokens. Supports email/password login and OAuth (Google). Designed to be stateless and horizontally scalable. Every protected route in the system depends on this feature.

## Behavior

### Login (email/password)
- **Input:** `email` (string), `password` (string)
- **Output:** `access_token` (JWT, 1h expiry), `refresh_token` (JWT, 7d expiry)
- **Side effects:** Logs the event to `audit_log` with `event_type: login`.
- **Errors:**
  - `401` — Credentials not found or password mismatch.
  - `429` — More than 5 failed attempts from the same IP within 10 minutes.

### Token Refresh
- **Input:** Valid, non-expired `refresh_token`
- **Output:** New `access_token` (JWT, 1h expiry)
- **Side effects:** None.
- **Errors:**
  - `401` — Token is expired or present in the revocation list.

### Logout
- **Input:** Valid `refresh_token`
- **Output:** `204 No Content`
- **Side effects:** Adds the `refresh_token` to the Redis revocation list.

## Business Rules
- A user may have active sessions on multiple devices simultaneously.
- A password reset must immediately invalidate all existing refresh tokens for that user.
- Access tokens are never revoked early — they expire naturally after 1 hour.
- Rate limiting is per IP, not per user account.

## Edge Cases
- Simultaneous login from multiple devices: each device receives its own independent token pair; revoking one does not affect the others.
- If Redis is unreachable during logout, the operation fails with `503`. The token remains technically valid until natural expiry.

## Implementation Map

**Components**
- `AuthService` (`src/auth/auth.service.ts`) — Core logic: credential validation, token generation, and revocation.
- `AuthGuard` (`src/auth/auth.guard.ts`) — Route middleware: validates `access_token` on every protected request.
- `AuthController` (`src/auth/auth.controller.ts`) — HTTP handlers for `/auth/login`, `/auth/refresh`, `/auth/logout`.

**Database Tables**
- `users` — Stores user credentials. Key fields: `id`, `email`, `password_hash`.
- `audit_log` — Records authentication events. Key fields: `user_id`, `event_type`, `ip_address`, `timestamp`.

**External Integrations**
- Redis — Token revocation list (SET with TTL matching token expiry) and rate-limit counters.
- Google OAuth 2.0 — Handles the third-party login flow via `passport-google-oauth20`.

## Configuration
- `JWT_SECRET` — Secret key used to sign tokens. Required.
- `JWT_ACCESS_EXPIRY` — Access token TTL (default: `1h`). Optional.
- `JWT_REFRESH_EXPIRY` — Refresh token TTL (default: `7d`). Optional.
- `RATE_LIMIT_MAX_ATTEMPTS` — Max failed attempts before lockout (default: `5`). Optional.
- `RATE_LIMIT_WINDOW_MS` — Rate limit window in milliseconds (default: `600000`). Optional.

## Dependencies
- `users` feature (for account lookup and password validation)
- Redis (must be running for rate limiting and revocation to work)
- Google OAuth 2.0 API (external, required for OAuth login)

## Diagrams
[auth-diagram.png](./auth-diagram.png)
```
