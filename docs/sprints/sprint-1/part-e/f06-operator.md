# F-06 — Shared Operator queue, Accept and reasoned Reject

**Status:** PROPOSED / not implemented, pending peer review. **Traceability:** FR-02/14/15/16; BR-02/03/12/13/20/25; NFR-01/04; DEC-016/017/027/028/033/034. **Dependencies:** F-01 auth, F-04 Order snapshots, shared F-05 transitions. [Shared Part E](README.md).

## Vertical workflow / UI states

Operator logs in → shared all-store queue loads Orders newest-first (optional PENDING filter) → opens Order detail → chooses Accept or Reject. Reject requires nonblank reason and confirmation. Controls disabled during submission; success triggers fresh persisted status; stale 409/another Operator or Customer Cancel race shows actual status and no optimistic success. All Operators can act on **any** PENDING Order; no Operator assignment. No payment step, customer portal queue access, push notifications or default acceptance of unknown status in Sprint 1.

## API / request-response-error contracts [PROPOSED]

- `GET /api/operator/orders` Operator role: `200 {"items":[{"id":"uuid","customerId":"uuid","status":"PENDING","totalSatang":29000,"createdAt":"RFC3339"}]}`; optional validated `?status=PENDING|ACCEPTED|REJECTED|CANCELLED`, otherwise all. 401 anonymous, 403 Customer, 422 invalid filter, 503 DB fault. Operator detail via shared owner/Operator `GET /api/orders/:id` from F-05.
- `POST /api/operator/orders/:id/accept` Operator + anti-CSRF, `{}` → `200 {"data":{"id":"uuid","status":"ACCEPTED"}}`; only PENDING. 409 `ORDER_NOT_PENDING` repeated/terminal/stale, 404 unknown, 401 unauth/403 Customer; 503 DB fault. Never decrement Available a second time; no payment indicator.
- `POST /api/operator/orders/:id/reject` Operator + anti-CSRF, `{ "reason":"Out of stock" }`; trim and validate Unicode `1..500` chars (proposed) and reject whitespace-only with 422; on success `200 {"data":{"id":"uuid","status":"REJECTED","rejectionReason":"Out of stock"}}`; terminal/stale 409, missing role 401/403, nonexistent 404, unexpected DB fault 503. No `stockDelta` or client status accepted.

## DB / atomicity

Each status action starts **one** `READ COMMITTED` transaction. Guard status `WHERE id=:id AND status='PENDING' RETURNING ...` (Reject also saves trimmed reason) and require exactly one updated row. For all Order Items, sorted by option ID, Accept moves `reserved -= q; sold += q` (no Available change); Reject moves `reserved -= q; available += q` and saves reason in **same transaction**. Every stock UPDATE must guard `reserved >= q` and update one row. Error after first row or status/reason rolls all back. Concurrent Accept/Reject/own Customer Cancel must produce **one** winning terminal transition. A loser cannot alter status, reason or inventory. No automatic reservation expiry or out-of-scope payment fields.

## Detailed acceptance criteria and STS (planned, no execution)

| AC / TEST | Preconditions / exact inputs | Expected result |
|---|---|---|
| AC-F06-01 / TEST-F06-01 | Seed two Customer orders and Operator; Operator/Customer/anonymous GET queue | Operator sees both customer Orders; Customer 403, anonymous 401; PENDING filter only returns PENDING |
| AC-F06-02 / TEST-F06-02 | Operator Accept PENDING 2-line order, then Accept again; inject failure on second Inventory update | First 200 ACCEPTED, all Reserved→Sold exactly once, Available unchanged; repeat 409 no new writes; fault rolls whole transaction back |
| AC-F06-03 / TEST-F06-03 | Reject with absent/whitespace reason, then `"Out of stock"`; inject failure after status/reason but before last stock release | Invalid 422 no changes; valid 200 REJECTED + persisted reason and full Reserved→Available; failure rolls back status, reason and all stock; owner & Operator only see reason |
| AC-F06-04 / TEST-F06-04 | Send simultaneous Operator Accept and Reject and owning Customer Cancel on single PENDING Order, plus wrong-role requests | Exactly one transaction wins with corresponding final status and inventory transfer; losers 409, wrong role 403; no double release/sale or partial reason; balances nonnegative |

## Feature Definition of Done

Both teammates approve role/queue/reason/error/transaction contracts; real PostgreSQL race and fault-injection tests pass with snapshots and cross-feature authorization; UI reconfirmation/stale-result checks and peer review recorded. **Not met: only documentation proposal.**
