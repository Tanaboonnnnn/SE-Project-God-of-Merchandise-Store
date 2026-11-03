# F-01 — Seeded authentication and backend authorization

**Status:** PROPOSED, unapproved/unimplemented. **Traceability:** FR-01/02; BR-01/23; NFR-01/02/07; DEC-012/027/034. **Dependencies:** PostgreSQL User/Session seed. Shared response/error/permissions: [Part E index](README.md).

## Workflow and UI contract

Login (idle → validation → pending → Customer catalog / Operator queue), generic invalid-credential error, reconnect/retry network failure. Refresh invokes `/me` instead of trusting cached browser roles. Logout revokes session, returns to login and clears privileged state. Expired session redirects to login; password never written to logs/localStorage. Visible keyboard focus and field-level errors.

## API requests, responses and failure policy (candidate)

| Endpoint / actor | Request | Success | Errors |
|---|---|---|---|
| `POST /api/auth/login` anonymous | JSON `{ "email":"c@example.test", "password":"user-provided" }` | `200` `{ "data": { "id":"uuid", "email":"c@example.test", "role":"CUSTOMER" }, "csrfToken":"opaque" }` and HttpOnly session cookie | `422 VALIDATION_ERROR` invalid shape; `401 INVALID_CREDENTIALS` invalid user/pass (same generic wording); `429 TOO_MANY_ATTEMPTS` pending rate-limit agreement |
| `GET /api/auth/me` session | No body | `200` same safe `data` identity/role | `401 UNAUTHENTICATED` for missing/expired/revoked session |
| `POST /api/auth/logout` session + anti-CSRF | `{}` | `204` empty; session token revoked | `401` invalid session (repeat behavior proposed 204 without restoring session); `403 CSRF_INVALID` |

Trim/lowercase email (proposed max 254 characters), bound password request size (proposed 128 characters); all endpoints validate JSON. Role and order ownership must be checked in middleware and database predicates, not client form or hidden buttons. Customer cannot submit Operator actions; Operator cannot submit Customer-only checkout/cancel.

## Database schema / persistence

`User(id UUID, email UNIQUE, role CUSTOMER|OPERATOR, passwordHash, active)`; `Session(id UUID, userId FK, tokenDigest UNIQUE, expiresAt, revokedAt, createdAt)`. Adaptive per-password salts/hashes, never store password plaintext or raw session tokens. Seed **2 Customers + 1 Operator** with demo credentials delivered privately; environment secrets outside tracked Git. Proposed session TTL/cookie origins/CSRF strategy must be peer confirmed before feature coding.

## Acceptance criteria and STS (not executed)

| AC and TEST | Preconditions / test inputs | Observable expected result |
|---|---|---|
| AC-F01-01 / TEST-F01-01 | Disposable DB with 2 Customer + 1 Operator seeded; POST login for all valid accounts, wrong password and nonexistent email; inspect DB hashes | Correct user/role, 200 and cookie; invalid 401 no active session, same error; only salted hashes in DB, never plaintext |
| AC-F01-02 / TEST-F01-02 | Unauthenticated GET private order; Customer GET operator queue; Customer B GET Customer A's Order | 401/403/404 respectively with no protected fields or existence leak |
| AC-F01-03 / TEST-F01-03 | Logged-in user POST logout then GET `/me` with original cookie; manually expire another Session | 204 and revoked token; old/expired `/me` returns 401; UI redirects without revealing user data |
| AC-F01-04 / TEST-F01-04 (additional) | Authenticated unsafe method missing/invalid CSRF value; malformed JSON and >limit credentials | Reject appropriately with no account/session corruption or secret leakage |

## Feature Definition of Done

Both authors review exact session/cookie/CSRF/seeding/role decisions; Prisma migration and real API implemented, secure hash inspection and all STS pass, keyboard/auth integration checks recorded with command/SHA evidence. **Current state: contract draft only.**
