# Campus Store — Feature Inventory (Part B)

> **Status:** Draft v0.2 (2026-11-02), refined for teammate's **CR-ENG-001** review. **Business scope remains frozen via FRZ-S1**. The peer review requested changes; these testable technical proposals await renewed approval. The documentation-only group repository was inspected; no application code or tests have been executed.
> **Normative business sources:** [`group-project/requirements-log.md`](group-project/requirements-log.md) DEC-001..034 + MAP-COST-001; [`group-project/CONTEXT.md`](group-project/CONTEXT.md). **Instructor references:** `group_work_specs_v1.pdf` §§5–8; `project_deliverable_1_v1.pdf` Parts A–D (the former is labelled *Draft for instructor review*).

## 1. Feature and sprint allocation

The F-IDs are stable proposed work-package identities derived from frozen decisions; they do not imply completed code. Sprint 2/3 contracts are intentionally not described as approved business rules.

| Feature | Capability | Sprint | FR coverage | BR coverage | NFR coverage | Dependencies | Proof of completion / verification |
| --- | --- | --- | --- | --- | --- | --- | --- |
| F-01 | Identity and server-side authorization | 1 | FR-01/02 | BR-01/23 | NFR-01/02/07 | None; DB seed account fixture | Auth, role, ownership negative tests |
| F-02 | Seeded catalog, variants and inventory read | 1 | FR-03/04 | BR-04/05/06 | NFR-03/08 | Data schema | Six products, twelve options, exact prices/stock |
| F-03 | Delivery checkout, pin, shipping calculation | 1 | FR-05/06/07/08 | BR-16/17/18/22/24/26 | NFR-10 | F-02; fixed origin coordinates verified | Address validation, map outage, 50/300 boundaries |
| F-04 | Atomic order submission, snapshots and idempotency | 1 | FR-07/09/10/11/17 | BR-07/08/09/10/11/15/19/20/25 | NFR-03/04/05 | F-01/02/03 | Multi-item happy path, tampering, races/replay |
| F-05 | Customer My Orders, status and Pending cancellation | 1 | FR-02/12/13/17 | BR-01/03/14/15/21/25 | NFR-01/03/04 | F-01/04 | Owner-only detail, refresh, cancellation race |
| F-06 | Operator queue, Accept and reasoned Reject | 1 | FR-02/14/15/16 | BR-02/03/12/13/20/25 | NFR-01/04 | F-01/04 | Role-denied, transition race, atomic stock |
| F-07 | Catalog management | 2 | FR-18 | Later contract | NFR-06 | F-02; Operator authorization | Catalog edit/price/stock history tests |
| F-08 | Fulfillment and delivery workflow | 2 | FR-19 | Later contract | NFR-06 | F-05/06 | State-transition, permissions, persisted history |
| F-09 | Simulated payment status | 2 | FR-20 | Later contract | NFR-06 | F-04/06; status model specified | No real payment; role/state consistency tests |
| F-10 | Order and sales summaries | 3 | FR-21 | Later contract | NFR-06 | F-07/08/09 | Aggregate rules + access tests |
| F-11 | UX, regression and release hardening | 3 | Cross-cutting | Cross-cutting | NFR-06/08/09/11 | F-01..10 | Cross-sprint regression, UAT defects, release DoD |


## 2. Sprint 1 contract boundaries

- **F-01 (identity):** seeded Customer/Operator, login/logout/me; permission middleware and ownership checks reused by F-04..F-06.
- **F-02 (catalog):** six products, twelve sellable options, size-level inventory, prices; no editing by customers/operators in Sprint 1.
- **F-03 (checkout/geo):** Thai address, MapLibre/OpenFreeMap map and pin, explicit confirmation of the **current** address + pin, invalidation and **reconfirmation on every destination-field edit**, backend distance fee/breakdown; no carrier and no automatic address/pin geocoding. Proposed server-validated quote revision must match submitted destination.
- **F-04 (order write):** raw-line validation, duplicate normalization, **guarded per-option stock updates in one DB transaction**, snapshots and `(customer_id, Idempotency-Key)` uniqueness. Proposed immutable successful-key retention throughout Sprint 1, canonical payload fingerprint, same-key replay, mismatch conflict and in-flight handling are detailed in `system-sds.md` section 6. **Never create a partial order or duplicate reservation.**
- **F-05 (owner read/cancel):** My Orders/Order Detail latest persisted state and reason; only own Pending can Cancel; contents immutable.
- **F-06 (operator):** one shared queue, Accept or reasoned Reject only Pending, exactly-once inventory transition under competing operations.

**Cross-feature race invariant (CR-ENG-001 B/D):** Acceptance/Reject/Cancel must serialize or conflict safely for one Pending Order via a guarded status update and conditional Reserved movements. Every stock movement and any rejection reason must commit in the **same database transaction** as the Order Status change; a losing transition or any injected DB failure must roll back **all** effects. These tests belong to F-04/F-05/F-06, not an unowned later feature.

## 3. Planned Sprint 1 acceptance criteria and software tests (Part E handoff, NOT executed)

AC/TEST IDs below are planned anchors for the **future** versioned feature contracts in `docs/sprints/sprint-1/`. They are **not** a substitute for those contracts and **not** a test execution report.

### F-01 — Identity and server-side authorization

| AC ID | Planned TEST ID | Level | Observable expected outcome |
| --- | --- | --- | --- |
| AC-F01-01 | TEST-F01-01 | API/DB | Seed Customer/Operator authenticate; /me identifies correct role; invalid password denied. |
| AC-F01-02 | TEST-F01-02 | API | Anonymous/forbidden role protected calls reveal no private data. |
| AC-F01-03 | TEST-F01-03 | API/UI | Logout invalidates authenticated session; subsequent /me protected. |

### F-02 — Seeded catalog, variants and inventory read

| AC ID | Planned TEST ID | Level | Observable expected outcome |
| --- | --- | --- | --- |
| AC-F02-01 | TEST-F02-01 | DB/API/UI | Read six seeded products with prices A=250 B=290 bag=180 notebook=70 pen=25 bottle=220 THB. |
| AC-F02-02 | TEST-F02-02 | DB/API | Shirts each S/M/L/XL with 10 available per size; four other options each 20. |

### F-03 — Delivery checkout, pin, shipping calculation

| AC ID | Planned TEST ID | Level | Observable expected outcome |
| --- | --- | --- | --- |
| AC-F03-01 | TEST-F03-01 | API/UI | Invalid/blank contact, address or invalid province rejects checkout; valid forms proceed. |
| AC-F03-02 | TEST-F03-02 | UI/API | Select valid destination map pin, preserve selected coordinates and attribution. |
| AC-F03-03 | TEST-F03-03 | Unit/API | Backend straight-line tariff tests at 50, just over 50, 300 and just over 300 km; unrounded classification. |
| AC-F03-04 | TEST-F03-04 | UI/API | Confirm displayed **current** address and pin before submission; editing any recipient/contact/structured-address/province/postcode or pin field invalidates confirmation and demands re-review. A mismatched confirmed quote/destination revision fails checkout with no Order/stock change; no automatic address-to-pin matching. |
| AC-F03-05 | TEST-F03-05 | UI/E2E | Map fails -> no-pin blocked with preserved cart/form and Retry; existing valid pin needs reconfirmation. |

### F-04 — Atomic order submission, snapshots and idempotency

| AC ID | Planned TEST ID | Level | Observable expected outcome |
| --- | --- | --- | --- |
| AC-F04-01 | TEST-F04-01 | API/DB/E2E | Persist one-or-more items, price snapshots, address/pin, authoritative subtotal+shipping and Pending. |
| AC-F04-02 | TEST-F04-02 | API/DB | Duplicate options normalize, snapshot and zero double-reservation, reject client price/total tamper. |
| AC-F04-03 | TEST-F04-03 | API/DB | Raw invalid line, >10 aggregate or insufficient stock rejects entire Order with zero side effects. |
| AC-F04-04 | TEST-F04-04 | API/DB | Same customer+key+canonical payload replays same persisted Order ID (201 then 200), including concurrent retries and delayed retry; reused key/different payload returns 409, in-flight retry cannot double reserve; failed checkout leaves no committed key claim. A new logical Order needs a new key. |
| AC-F04-05 | TEST-F04-05 | API/DB | Two distinct customers race for last unit: at most one commits; guarded atomic stock update prevents oversell. Multi-item late failure, transaction failure or deadlock rolls back *all* Order, stock and key effects in a real DB. |

### F-05 — Customer My Orders, status and Pending cancellation

| AC ID | Planned TEST ID | Level | Observable expected outcome |
| --- | --- | --- | --- |
| AC-F05-01 | TEST-F05-01 | API/DB/UI | Owner retrieves persisted My Orders/Detail current status and rejection reason on refresh. |
| AC-F05-02 | TEST-F05-02 | API/DB | Owning Customer cancels Pending once; status and release commit together, duplicate/stale action changes nothing; injected DB failure rolls both back; cannot cancel Accepted. |
| AC-F05-03 | TEST-F05-03 | API | Other customer denied order/notes/cancel; no ownership inference. |
| AC-F05-04 | TEST-F05-04 | API/DB | Order contents immutable after submit; replacement requires new Order/key. |

### F-06 — Operator queue, Accept and reasoned Reject

| AC ID | Planned TEST ID | Level | Observable expected outcome |
| --- | --- | --- | --- |
| AC-F06-01 | TEST-F06-01 | API/UI | Operator sees all store orders, customer lacks queue access. |
| AC-F06-02 | TEST-F06-02 | API/DB | Accept Pending once; guarded status change and reserved -> sold commit atomically, without double deduction; simulated stock-update failure rolls both back. |
| AC-F06-03 | TEST-F06-03 | API/DB | Reject Pending requires nonblank reason; reason+status+release commit atomically; failure rolls all back and owner can view persisted reason after success. |
| AC-F06-04 | TEST-F06-04 | API/DB | Role/status and simultaneous Accept/Reject/Cancel race resolve to exactly one winning terminal transition. The losing action makes no changes to status, stock or reason; real-DB fault injection confirms no partial commits. |

## 4. Definition of Done and work prerequisites

A feature is **not Done** until its versioned SDS, UI/API/data shapes, validation/error/permission cases, AC, linked STS tests and DoD are reviewed **before** implementation; targeted tests show meaningful red→green history; integration tests prove persistence/ownership; teammate PR review is substantive; integration/staging release tests pass; docs and setup are current; and evidence references real commands and commit IDs. Do not fake test counts, commits or screenshots.

Before implementation, inspect actual group Git repository and `AGENTS.md` and adapt file paths/dependencies. The previous Nov 1 implementation plan is superseded and unsafe to execute unchanged.
