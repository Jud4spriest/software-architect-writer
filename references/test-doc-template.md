# Test Document Template

```markdown
*Last Updated: YYYY-MM-DD*
---
# Feature Name — Test Coverage

Test suite for the [Feature Name] feature. See spec: [feature-name-spec.md](./feature-name-spec.md).

## Coverage Summary

| Area | Type | Status |
|------|------|--------|
| Action 1 — success | Integration | ✅ Covered |
| Action 1 — error condition | Unit | ✅ Covered |
| Action 2 — success | Integration | ✅ Covered |
| Edge case 1 | Integration | ✅ Covered |

## Test Cases

### [Action / Area Name]

**`FEAT-001` — Descriptive test name**
- **Type:** Unit | Integration | E2E
- **Input:** What is passed in
- **Expected:** Exact expected outcome — response code, return value, state change, side effect.

**`FEAT-002` — Descriptive test name**
- **Type:** Integration
- **Input:** What is passed in
- **Expected:** Exact expected outcome.

### Edge Cases

**`FEAT-NNN` — Descriptive test name**
- **Type:** Integration
- **Scenario:** Description of the situation (use "Scenario" instead of "Input" when the test requires multiple setup steps)
- **Expected:** Exact expected outcome.
```

**Naming convention for test IDs:** Use a short prefix derived from the feature name (e.g., `AUTH-`, `NOTIF-`, `BILL-`) followed by a zero-padded number. IDs must be stable — never reuse or renumber them.

**Test types:**
- `Unit` — Tests a single function or class in isolation. No real DB, no real external calls.
- `Integration` — Tests a full flow through multiple layers (service + DB + cache). May call real infrastructure in a test environment.
- `E2E` — Tests the entire system from the user's perspective (e.g., HTTP request through to DB response).

---

# Worked Example: `auth-test.md`

```markdown
*Last Updated: 2026-04-13*
---
# Authentication — Test Coverage

Test suite for the Authentication feature. See spec: [auth-spec.md](./auth-spec.md).

## Coverage Summary

| Area                          | Type        | Status      |
|-------------------------------|-------------|-------------|
| Login — success               | Integration | ✅ Covered  |
| Login — invalid credentials   | Integration | ✅ Covered  |
| Login — unknown email         | Integration | ✅ Covered  |
| Login — rate limiting         | Integration | ✅ Covered  |
| Token refresh — success       | Unit        | ✅ Covered  |
| Token refresh — expired token | Unit        | ✅ Covered  |
| Token refresh — revoked token | Unit        | ✅ Covered  |
| Logout — token revocation     | Integration | ✅ Covered  |
| Logout — Redis unavailable    | Unit        | ✅ Covered  |
| Concurrent device sessions    | Integration | ✅ Covered  |
| Password reset invalidation   | Integration | ✅ Covered  |

## Test Cases

### Login

**`AUTH-001` — Successful login**
- **Type:** Integration
- **Input:** Valid `email` and matching `password`
- **Expected:** `200 OK`. Response contains `access_token` (JWT) and `refresh_token` (JWT). Event recorded in `audit_log` with `event_type: login`.

**`AUTH-002` — Invalid password**
- **Type:** Integration
- **Input:** Valid `email`, wrong `password`
- **Expected:** `401 Unauthorized`. No tokens issued. Nothing written to `audit_log`.

**`AUTH-003` — Unknown email**
- **Type:** Integration
- **Input:** Email not present in `users` table
- **Expected:** `401 Unauthorized`. Response is identical to AUTH-002 (no user enumeration).

**`AUTH-004` — Rate limiting triggered**
- **Type:** Integration
- **Input:** 6 consecutive failed login attempts from the same IP within 10 minutes
- **Expected:** First 5 attempts return `401`. 6th attempt returns `429 Too Many Requests`. Counter resets after the 10-minute window.

### Token Refresh

**`AUTH-005` — Successful token refresh**
- **Type:** Unit
- **Input:** Valid, non-expired `refresh_token`
- **Expected:** `200 OK`. New `access_token` returned.

**`AUTH-006` — Expired refresh token**
- **Type:** Unit
- **Input:** A `refresh_token` past its TTL
- **Expected:** `401 Unauthorized`.

**`AUTH-007` — Revoked refresh token**
- **Type:** Unit
- **Input:** A `refresh_token` present in the Redis revocation list
- **Expected:** `401 Unauthorized`.

### Logout

**`AUTH-008` — Successful logout**
- **Type:** Integration
- **Input:** Valid `refresh_token`
- **Expected:** `204 No Content`. Token added to Redis revocation list. Subsequent refresh attempt with same token returns `401`.

**`AUTH-009` — Logout with Redis unavailable**
- **Type:** Unit
- **Input:** Valid `refresh_token`, Redis connection mocked as unavailable
- **Expected:** `503 Service Unavailable`. Token is NOT revoked.

### Edge Cases

**`AUTH-010` — Concurrent sessions on multiple devices**
- **Type:** Integration
- **Scenario:** Same user logs in from two devices simultaneously
- **Expected:** Each device receives an independent token pair. Logging out device A does not invalidate device B's tokens.

**`AUTH-011` — Password reset invalidates all refresh tokens**
- **Type:** Integration
- **Scenario:** User has an active session, then triggers a password reset
- **Expected:** All existing `refresh_token`s for that user are revoked. Subsequent refresh attempts with pre-reset tokens return `401`.
```
