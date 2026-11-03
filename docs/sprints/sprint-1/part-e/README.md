# Part E — Sprint 1 feature contracts (GP-03, Issue #4)

**Status 2026-11-03: PROPOSED FOR PEER REVIEW.** This is documentation only. No API, app, migration, integration test or CI result is claimed. Frozen baseline: FRZ-S1; DEC-001..034 and MAP-COST-001 in `../../../group-project/requirements-log.md`. Earlier AC/TEST anchors are in `../../../features.md`; system design in `../../../system-sds.md`; GP-01 assumptions in `../gp-01-readiness.md`.

## Shared proposed wire and persistence rules

- Candidate stack: React/Vite/TypeScript UI; Express/TypeScript API; native PostgreSQL and Prisma-managed schema/migrations; npm workspaces; Vitest/Supertest/targeted Playwright. Exact versions and DB execution still need verification. MapLibre + OpenFreeMap is frozen. No Docker for current local setup.
- JSON camelCase; opaque UUID order/option IDs; monetary values integer **satang**; times RFC3339 UTC; statuses `PENDING`, `ACCEPTED`, `REJECTED`, `CANCELLED`. Response: `{ "data": {...} }` for one entity or `{ "items": [...] }` for lists; error: `{ "error": { "code":"...", "message":"...", "fields":[{"path":"...","code":"..."}] } }` (fields optional). Never leak SQL, stack or another customer's details.
- Opaque DB-backed cookie session, salted adaptive password hashes; HttpOnly and Secure on HTTPS; proposed SameSite=Lax and anti-CSRF checks on writes. Actor/role always obtained from session. API uses 401 unauthenticated, 403 forbidden role, 404 hidden/not-owned order, 409 stock/state/replay conflicts, 422 invalid input, 503 unavailable dependency. Session expiry/CSRF design and exact stack versions need peer approval.
- Prisma migrations exclusively own tables `User`, `Session`, `Product`, `SellableOption`, `Inventory`, `Province`, `Order`, `OrderItem`, `CheckoutAttempt`; `db/seeds/provinces.sql` is **data-only**, not schema DDL. Province ID FK with English name snapshot; 77 names in PostgreSQL. Seed 2 Customers, 1 Operator, 6 products/12 options/160 Available; do not reset real stock/orders upon reseeding.
- All inventory has nonnegative DB constraints. Keys unique `(customerId,idempotencyKey)`; lines unique `(orderId,optionId)`; all states and currency values server-authoritative. Candidate isolation `READ COMMITTED`, sorted `optionId` guarded conditional stock updates, protected `PENDING` order transition, **one DB transaction** per lifecycle action, rollback on every partial failure; retry whole transaction with unchanged key only for transient SQLSTATE `40P01`/`40001`, bounded attempts. Must prove on **real** PostgreSQL in implementation phase.

## Shared permissions

| Operation | Anonymous | Customer (owner) | Customer (other Order) | Operator |
|---|---|---|---|---|
| Login | allowed | allowed | allowed | allowed |
| `/auth/me`, logout, catalog, provinces | 401 (proposal) | allowed | allowed | allowed |
| Quote, submit order | 401 | allowed | allowed for own new order | 403 |
| My Orders / cancel | 401 | own / own Pending only | 404 other Order | 403 |
| Order detail | 401 | own | 404 | all store orders |
| Operator queue / accept / reject | 401 | 403 | 403 | Pending only |

## CR-ENG-001 mechanics across features

- **A — confirmation invalidation:** Any recipient, phone, addressLine, subdistrict, district, province, postcode, cart or pin edit clears the quote/confirmation and blocks submission until another server quote and explicit review/confirmation of the current address+pin. The backend binds a signed quote to canonical destination+cart and revision. No automatic verification that address matches pin.
- **B — last unit:** Validate each raw line, normalize identical exact option IDs by sum (1..10), sort options, and atomically update `available >= qty` and `reserved += qty` inside one Order transaction; partial failures roll back key/order/stock.
- **C — idempotency:** `(customerId, UUIDv4 key)` unique; canonical fingerprint of normalized lines, recipient/phone, address, province, postcode, pin (proposed six fixed decimal digits); no amounts or client role. First 201, identical replay 200 same Order ID/current status, changed payload 409 `IDEMPOTENCY_KEY_REUSED`, in-flight bounded wait then retryable 409 `CHECKOUT_IN_PROGRESS` with Retry-After; successful keys retained throughout S1 while Order exists, failed attempts leave no committed key. New logical Order always new key.
- **D — competing transitions:** Accept, Reject, Cancel require guarded `PENDING` status update; one winner moves **all** reserved units to Sold or Available and (Reject) saves trimmed nonblank reason. Losers receive 409 `ORDER_NOT_PENDING`; no partial writes, reason or duplicate transfers.
- **Hard F-03 blocker:** choose, verify and record version/license/checksum of **Thailand mainland and islands polygon**, border/sea/island test fixtures *before implementation*. Approximate simulated KMUTT Bangmod origin `(13.65178,100.49384)` is not a surveyed dispatch location. A global coordinate bounding box alone cannot establish Thai eligibility.

## Contracts and pending approval

| Document | Scope | Planned AC/TEST anchors |
|---|---|---|
| [F-01](f01-auth.md) | Auth/session/roles | AC-F01-01..03, TEST-F01-01..03 |
| [F-02](f02-catalog.md) | Catalog/options | AC-F02-01..02, TEST-F02-01..02 |
| [F-03](f03-delivery.md) | Map/address/shipping | AC-F03-01..05, TEST-F03-01..05 |
| [F-04](f04-checkout.md) | Atomic checkout/idempotency | AC-F04-01..05, TEST-F04-01..05 |
| [F-05](f05-orders.md) | Customer orders/cancel | AC-F05-01..04, TEST-F05-01..04 |
| [F-06](f06-operator.md) | Operator queue/decision | AC-F06-01..04, TEST-F06-01..04 |

**Approval gate:** Lead/author Chxtamos; co-author/reviewer Tanaboonnnnn must review and explicitly accept/revise the six contracts, stack/session/CSRF, atomic DB/key implementation and retry policy. Polygon dataset/license/islands, exact runtime versions, authenticated workflows and team availability remain open; neither peer approval nor running tests are claimed. Block GP-04..08 feature implementation until this gate, and do not mark Issue #4 Done merely for drafting.
