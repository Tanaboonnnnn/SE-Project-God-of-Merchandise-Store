# F-05 — Customer My Orders, immutable detail and Pending cancellation

**Status:** PROPOSED / not implemented, pending peer review. **Traceability:** FR-02/12/13/17; BR-01/03/14/15/21/25; NFR-01/03/04/08/09; DEC-018/019/032/033/034. **Dependencies:** F-01 auth, F-04 Orders and inventory. [Shared Part E](README.md).

## Vertical workflow / UI states

Customer logs in → My Orders loads persisted owner-only list, status PENDING/ACCEPTED/REJECTED/CANCELLED and latest values → opens immutable Order detail (all item/price/address/pin snapshots and server total); Rejected displays reason visible only to owner and Operators. Explicit refresh or page re-open fetches latest DB state; **no** push/notification/email in S1. Cancel button only for own PENDING order → confirmation dialog → pending → backend success shows persisted CANCELLED; stale race 409 shows updated status and Retry Refresh, never optimistic terminal claim. To change submitted content, cancel Pending and create a fresh order/key; accepted orders cannot cancel or edit.

## API / request-response-error contracts [PROPOSED]

- `GET /api/orders` Customer only: 200 `{ "items":[{"id":"uuid","status":"PENDING","totalSatang":29000,"createdAt":"RFC3339"}] }`; query constrained `customerId = session.user.id` in database; 401 unauth, 403 Operator (proposed Customer-only list). No other Customers' orders.
- `GET /api/orders/:id` Customer owner or Operator: 200 `{ "data":{"id":"uuid","status":"REJECTED","recipientName":"...","address":{"provinceNameEn":"Bangkok",...},"pin":{"latitude":13.6,"longitude":100.5},"items":[...],"subtotalSatang":...,"shippingFeeSatang":...,"totalSatang":...,"rejectionReason":"reason"} }`. Unavailable order or non-owner Customer → same `404 NOT_FOUND` (avoid ID enumeration); 401 unauth; reason only on Rejected to owner/Operator, no other Customer disclosure.
- `POST /api/orders/:id/cancel` authenticated owning Customer + anti-CSRF, body `{}`; valid PENDING → `200 { "data":{"id":"uuid","status":"CANCELLED"} }`; repeated/stale terminal order → `409 ORDER_NOT_PENDING`, Operator → 403, other Customer → 404, DB fault → safe 503. Any `PATCH`/edit of original address/items/pin is unsupported and rejected; do not silently amend saved Order.

## Persistence / transaction

Immutable Order owner, items, address/pin/price snapshots persisted. Cancel under `READ COMMITTED` begins a single transaction; conditional `UPDATE orders SET status='CANCELLED' WHERE id=:id AND customer_id=:sessionId AND status='PENDING' RETURNING id`; require one winner, then sorted stored Order Item option IDs each guarded `UPDATE inventory SET reserved=reserved-q, available=available+q WHERE option_id=:id AND reserved>=q`; on any failed update roll back status and **all** stock releases. Never release twice or expire pending reservations automatically. For unknown/not-own Order, same 404 with no reason/content leak. Persistence should survive API restart.

## Detailed acceptance criteria and STS (planned, no execution)

| AC / TEST | Preconditions / concrete test inputs | Expected results and evidence |
|---|---|---|
| AC-F05-01 / TEST-F05-01 | Customer A has PENDING and REJECTED orders, latter with reason; restart API, refresh My Orders and detail | Owner-only latest persisted statuses and appropriate reason; immutable addresses/items/prices survive restart, no push notification |
| AC-F05-02 / TEST-F05-02 | Own PENDING Order with two options reserved; POST Cancel twice, Cancel Accepted; inject failure on second release | First 200 CANCELLED and all Reserved→Available exactly once; repeated/Accepted 409 unchanged, failure causes full rollback of status/stock |
| AC-F05-03 / TEST-F05-03 | Customer B guesses A order ID for GET/Cancel; Operator tries Customer Cancel; anonymous calls | B receives indistinguishable 404, no payload/reason; Operator 403; anon 401; no stock mutations |
| AC-F05-04 / TEST-F05-04 | Try PATCH original item/recipient/price/pin; cancel own pending then create new order with fresh UUIDv4 key; try cancel Accepted | Edits not permitted, original snapshots unchanged; new independently validated order or failure, fresh ID/key; Accepted cannot cancel |

## Feature Definition of Done

Both collaborators approve owner privacy, exact errors, cancellation CAS and immutable snapshots; real database persistence, injected rollback, competing cancel/accept/reject tests, UI refresh, keyboard and negative ownership probes pass and are reported with real outputs/SHA. **Not met: proposal docs only.**
