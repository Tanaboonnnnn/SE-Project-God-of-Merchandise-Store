# Campus Store — Software Requirements Specification (Part A)

> **Status:** Draft v0.1 (2026-11-02). **Business scope is frozen via FRZ-S1**, but this engineering artifact has not been reviewed by the second team member or compared with the actual group repository. No application code or tests were executed in preparing this draft.
> **Normative business sources:** [`group-project/requirements-log.md`](group-project/requirements-log.md) DEC-001..034 + MAP-COST-001; [`group-project/CONTEXT.md`](group-project/CONTEXT.md). **Instructor references:** `group_work_specs_v1.pdf` §§5–8; `project_deliverable_1_v1.pdf` Parts A–D (the former is labelled *Draft for instructor review*).

## 1. Product purpose, stakeholders and actors

**Stakeholder problem.** A fictional one-store campus merchandise business must take orders with quantity/price integrity and a verifiable Customer-to-Operator workflow. This is a *simulation*, not an officially licensed KMUTT retail service or a claim of actual courier fulfillment. Customers need to select merchandise, a delivery address and a map pin, place an order, and check its status. Operators need one shared queue and controls to accept or reject Pending orders.

**Actors.** **Customer**: seeded authenticated account that browses the catalog, enters a nationwide Thai delivery destination, submits orders, reads only its own orders, and cancels its own Pending orders. **Operator**: seeded authenticated staff account that sees every order for this one store and Accepts/Rejects Pending orders. **External mapping service**: OpenFreeMap public styles/tiles, which render the map but do not determine charges or confirm postal correctness.

**Primary Sprint 1 journey.** Customer login → six-item seeded catalog → select one or more exact sellable options → enter recipient/structured address → select destination pin on MapLibre/OpenFreeMap → acknowledge displayed address and pin → backend validates, computes distance-based shipping and reserves stock in one persisted Pending order → Customer views it after refresh → Operator logs in and either Accepts (reserved → sold) or Rejects with mandatory reason (reserved → available) → Customer sees current status. Eligible Customer may Cancel Pending instead.

## 2. Scope baseline and exclusions

**Sprint 1 in scope:** FR-01..FR-17, BR-01..BR-26; nationwide **delivery only**; map-pin selection and straight-line, server-calculated shipping fee; 6 products/12 options; fixed initial catalog prices/stocks; real login/logout/current identity; all-or-nothing multi-item submission, immutable snapshots, idempotency; current status, Customer cancellation, Operator acceptance/rejection and role/owner restrictions. The exact business rules below come from FRZ-S1 decisions.

**Not in Sprint 1:** pickup, real carrier/tracking, geocoding/address-to-pin matching, self-registration/password reset, catalog administration, payment status or payment action, real money, post-acceptance refunds/returns, automated Pending expiry, unsolicited notifications, advanced analytics, GCP deployment or completed CI/CD (Lab 5). Sprint 2 includes catalog editing, fulfillment and simulated payments; Sprint 3 includes reporting and hardening. Their *specific* transitions and rules are not silently invented here.

**Business assumptions:** the origin is a simulated fixed point at the approximate center of KMUTT Bangmod. **Exact origin coordinates are not yet source-verified and are an engineering-release input.** Distances are great-circle/straight-line, not driving route estimates. Shipping bands (0–50km=฿40; >50–300km=฿80; >300km=฿150) are invented demo tariffs, not carrier quotations. A customer's confirmation of address and pin does not verify geographic correspondence.

## 3. Functional requirements (FR)

FR means observable application capability. Sprint 1 entries are **approved business behavior**; later Sprint entries are **planned instructor-theme coverage** with detailed contracts deferred until their sprints.

| ID | Requirement / observable outcome | Sprint | Responsible feature(s) | Evidence planned |
| --- | --- | --- | --- | --- |
| FR-01 | Allow seeded Customer/Operator accounts to log in, log out, and retrieve the current authenticated identity. | 1 | F-01 | AC-F01-01..03 |
| FR-02 | Enforce authentication, Customer record ownership and Operator role rights at the backend; reject forbidden requests without leaking other customers' data. | 1 | F-01,F-05,F-06 | AC-F01-02; AC-F05-03; AC-F06-04 |
| FR-03 | Display six seeded merchandise products from persistent storage, including availability and authoritative unit prices. | 1 | F-02 | AC-F02-01 |
| FR-04 | Allow selection of one of four sizes for either shirt design and a single sellable option for the other four products. | 1 | F-02 | AC-F02-02 |
| FR-05 | Collect recipient name, ten-digit leading-zero Thai mobile, structured address, five-digit postal code, and a province selected from a Thai list; perform server-side validation. | 1 | F-03 | AC-F03-01 |
| FR-06 | Let Customer choose destination map pin; preserve form/cart and offer Retry if map fails; require reconfirmation of saved pin when map remains unavailable. | 1 | F-03 | AC-F03-02; AC-F03-05 |
| FR-07 | Calculate shipping distance/fee using backend and display item subtotal, shipping fee and full order total before final order confirmation; persist server-calculated amounts. | 1 | F-03,F-04 | AC-F03-03; AC-F04-01 |
| FR-08 | Present full current recipient/contact/address and selected pin and require explicit Customer acknowledgement before every checkout submission; invalidate it when any confirmed destination field or pin changes, then require reconfirmation. No automatic written-address-to-pin matching. | 1 | F-03 | AC-F03-04 |
| FR-09 | Accept one or more selectable order lines only when all lines pass validation and inventory checks, without partially creating an Order. | 1 | F-04 | AC-F04-01; AC-F04-03 |
| FR-10 | Persist the complete Order, normalized Order Items and price/address/pin/charge snapshots in the database. | 1 | F-04 | AC-F04-01; AC-F04-02 |
| FR-11 | Prevent duplicate Order and duplicate stock reservation on retries of one logical checkout using backend Idempotency Key and frontend submission lock. | 1 | F-04 | AC-F04-04 |
| FR-12 | Let Customer view only their own persisted order list/detail, current status and any recorded rejection reason on page open or refresh. | 1 | F-05 | AC-F05-01; AC-F05-03 |
| FR-13 | Let Customer cancel only their own Pending Order and immediately release its reserved inventory exactly once. | 1 | F-05 | AC-F05-02; AC-F05-03 |
| FR-14 | Let any Operator read the shared Campus Store order queue and view all orders. | 1 | F-06 | AC-F06-01 |
| FR-15 | Let Operator accept a Pending Order once and record the status while converting its already-reserved units to Sold. | 1 | F-06 | AC-F06-02 |
| FR-16 | Let Operator reject a Pending Order with a mandatory nonblank reason and atomically release all its reserved units. | 1 | F-06 | AC-F06-03; AC-F06-04 |
| FR-17 | Reject attempts to modify customer-submitted Order contents after creation; Pending corrections require cancellation followed by a new checkout. | 1 | F-04,F-05 | AC-F05-04 |
| FR-18 | Allow an authorized Operator to maintain catalog entries, their prices, active availability and stock through a documented workflow. | 2 | F-07 | AC-F07 (define in S2 contract) |
| FR-19 | Provide authorized order fulfillment transitions and operational delivery state tracking; choose permitted transitions in the S2 contract. | 2 | F-08 | AC-F08 (define in S2 contract) |
| FR-20 | Represent simulated payment status and valid transitions without processing real money, separate from Order Status. | 2 | F-09 | AC-F09 (define in S2 contract) |
| FR-21 | Provide order/sales summaries over persisted orders with authorized access and defined aggregation rules. | 3 | F-10 | AC-F10 (define in S3 contract) |

## 4. Business rules (BR)

BR means a domain invariant, permission or lifecycle rule. Business rules are authoritative across UI, API and database; client validation alone never establishes compliance.

| ID | Normative business rule | Sprint | Feature(s) | Evidence |
| --- | --- | --- | --- | --- |
| BR-01 | A Customer can read and cancel only their own eligible Orders, never another Customer's data. | 1 | F-01,F-05 | AC-F05-03 |
| BR-02 | All Operators share one-store order visibility; only Operators can Accept/Reject Pending Orders. | 1 | F-06 | AC-F06-01; AC-F06-04 |
| BR-03 | Sprint 1 Order lifecycle: Pending -> Accepted OR Rejected OR Cancelled, each at most once; no transition from a terminal status. | 1 | F-05,F-06 | AC-F05-02; AC-F06-02..04 |
| BR-04 | Seed exactly six catalog products and twelve sellable options (8 shirt design-size choices plus four single-option items). | 1 | F-02 | AC-F02-01; AC-F02-02 |
| BR-05 | Initial unit prices THB (shipping excluded): shirt A 250, shirt B 290, bag 180, notebook 70, pen 25, bottle 220; shirt sizes share their design price. | 1 | F-02,F-04 | AC-F02-01; AC-F04-02 |
| BR-06 | Initial Available Stock: each of 8 shirt size options 10; each other option 20; total 160 units. Reserved/Sold begin at zero. | 1 | F-02 | AC-F02-02 |
| BR-07 | Per sellable option and Order, request an integer aggregate of 1..10 and at most current Available Stock; validate every raw line before aggregation. | 1 | F-04 | AC-F04-03 |
| BR-08 | Persist exactly one Order Item for each unique sellable option with the sum of its valid duplicate request lines. | 1 | F-04 | AC-F04-02 |
| BR-09 | If any item/quantity/address/pin condition fails, create no Order and reserve no stock for any line (all-or-nothing). | 1 | F-03,F-04 | AC-F04-03 |
| BR-10 | Backend authoritative prices determine item subtotals and total; snapshots survive future catalog changes; never accept a client-supplied price/total. | 1 | F-04 | AC-F04-01; AC-F04-02 |
| BR-11 | Submitting a valid Order reserves requested units for the exact sellable option atomically, preventing oversell. | 1 | F-04 | AC-F04-01; AC-F04-05 |
| BR-12 | Operator Accept converts Reserved -> Sold once; it must not deduct Available Stock a second time. | 1 | F-06 | AC-F06-02; AC-F06-04 |
| BR-13 | Operator Reject requires a nonblank customer-visible reason; reason, Pending -> Rejected and release of reservation happen atomically exactly once. | 1 | F-06 | AC-F06-03; AC-F06-04 |
| BR-14 | Owning Customer may Cancel only Pending; cancellation releases all reserved quantities exactly once; Accepted cannot be cancelled in S1. | 1 | F-05 | AC-F05-02; AC-F05-03 |
| BR-15 | Submitted order items, quantities, contact/address/pin and prices remain immutable; correction means a newly validated Order with new submission key. | 1 | F-04,F-05 | AC-F05-04 |
| BR-16 | Every checkout requires valid written delivery destination, selected map pin and explicit displayed-address/pin acknowledgement; no automated address-to-pin matching. | 1 | F-03 | AC-F03-01; AC-F03-04 |
| BR-17 | Contact is 10 numeric digits beginning 0; postal code exactly five digits; all required text nonblank; province from the approved Thai list. | 1 | F-03 | AC-F03-01 |
| BR-18 | For nationwide delivery only, shipping uses great-circle unrounded distance from fixed simulated campus-center origin to pin; fee 40 THB for d<=50km, 80 for 50<d<=300, 150 for d>300; round distance only for display. | 1 | F-03,F-04 | AC-F03-03 |
| BR-19 | Retrying a logical checkout with the same Idempotency Key cannot create another Order/reservation; an intentional new checkout uses a new key. | 1 | F-04 | AC-F04-04 |
| BR-20 | No payment status, payment method or action exists in S1; Operator acceptance is not proof of payment. | 1 | F-04,F-06 | Scope review |
| BR-21 | No proactive status notifications in S1; owner reads status/rejection reason by opening or refreshing My Orders/Detail. | 1 | F-05 | AC-F05-01 |
| BR-22 | Delivery-only, eligible destinations within Thailand; no counter pickup or real carrier integration in S1. | 1 | F-03 | AC-F03-02; Scope review |
| BR-23 | Customer/Operator access uses seeded password accounts; no public self-registration in S1. | 1 | F-01 | AC-F01-01 |
| BR-24 | Destination map uses MapLibre GL JS + OpenFreeMap without fee-based mapping APIs or credit-card/key requirement as approved; show required attribution. | 1 | F-03 | AC-F03-02 |
| BR-25 | No automatic Pending reservation expiry in S1; reservation ends only at an approved terminal transition. | 1 | F-04,F-05,F-06 | AC-F05-02; AC-F06-02..03 |
| BR-26 | Map unavailable with no valid prior pin blocks checkout and retains cart/address; a valid prior pin must be explicitly reconfirmed before continuing. | 1 | F-03 | AC-F03-05 |

## 5. Measurable non-functional requirements (NFR)

These test targets are **proposed engineering-level acceptance thresholds**, not additional approved commercial promises. Team review must accept the targets before Part E contracts are frozen. Where a check is manual, the report must label it manual and not count it as an automated pass.

| ID | Target and verification method | Sprint checkpoint | Feature(s) | Planned evidence |
| --- | --- | --- | --- | --- |
| NFR-01 | Access isolation: every planned negative auth/role/owner API case returns no protected payload; verify 401/403/404 policy using automated integration tests. | 1 | F-01,F-05,F-06 | TEST-F01-02; TEST-F05-03; TEST-F06-04 |
| NFR-02 | Credential safety: zero plaintext seeded password values in stored password fields, password verification against salted adaptive hashes, and no credentials in committed tracked files; inspect schema and test DB. | 1 | F-01 | TEST-F01-01; security inspection |
| NFR-03 | Persistence: a successfully submitted Order, line snapshots, selected pin and status survive an API process restart and are retrievable by the owner; verify on real test database. | 1 | F-04,F-05 | TEST-F04-01; TEST-F05-01 |
| NFR-04 | Transactional integrity: in at least 10 simultaneous 1-unit requests with stock=1, no more than one reservation succeeds; all-line rollback on stock failure, and competing Accept/Reject/Cancel never transfer/release stock twice. Inject a DB failure between status and stock updates and prove both roll back. | 1 | F-04,F-05,F-06 | TEST-F04-05; TEST-F06-04 |
| NFR-05 | Retry integrity: two same-key retries (including concurrent requests) yield at most one persisted Order and reservation; same-key changed-payload conflicts, incomplete attempts cannot generate duplicates, and failed transactions leave no committed key claim. Test on a real DB. | 1 | F-04 | TEST-F04-04 |
| NFR-06 | Release reproducibility: document commands to install, migrate, seed, build and run automated tests; for accepted S1 release, record exact commit SHA, counts of pass/fail/skip and zero failing required tests. | 1 | F-01,F-02,F-03,F-04,F-05,F-06 | release evidence check (Part F) |
| NFR-07 | Configuration hygiene: real secrets absent from Git/client bundles; env example names and local setup instructions documented; inspect tracked files and build artifacts. | 1 | F-01,F-03,F-04 | configuration review |
| NFR-08 | Local seeded-demo responsiveness (proposed target): p95 GET catalog and owner order detail <=2.0 seconds over 50 sequential requests after warm-up; report machine/test DB setup and actual measurement. | 1 | F-02,F-05 | TEST-PERF-01 (planned) |
| NFR-09 | Keyboard operability (proposed target): complete Login, catalog selection, checkout confirmation and My Orders navigation using only keyboard, with visible focus; record manual checklist. | 1 | F-01,F-02,F-03,F-05 | TEST-UX-01 (manual planned) |
| NFR-10 | Map failure resilience: in simulated map network failure, no-pin checkout is blocked, cart/address persist, Retry is available; with previously selected valid pin, explicit reconfirmation is required. | 1 | F-03 | TEST-F03-05 |
| NFR-11 | Deployment readiness: isolate runtime configuration, persistent DB setup and stateless API assumptions in SDS for later Docker+GCP; actual repeatable deployment/CI gates verified at Lab 5, not claimed at S1. | 2 | F-01,F-04,F-11 | SDS review S1; Lab5 pipeline verification S2 |

## 6. Fixed seeded data and state semantics

| Sellable choice | Unit price (THB) | Starting Available | Starting Reserved | Starting Sold |
| --- | ---: | ---: | ---: | ---: |
| T-shirt A S/M/L/XL | 250 each, any size | 10 per size (40 total) | 0 | 0 |
| T-shirt B S/M/L/XL | 290 each, any size | 10 per size (40 total) | 0 | 0 |
| Tote bag | 180 | 20 | 0 | 0 |
| Notebook | 70 | 20 | 0 | 0 |
| Pen | 25 | 20 | 0 | 0 |
| Water bottle | 220 | 20 | 0 | 0 |
| **All choices (12)** | — | **160** | **0** | **0** |

Each line counts against the total of **1–10 integer units per exact option** and actual Available Stock. Multiple entries for the same option are validated separately then combined before limit/stock checks and persisted as *one* Order Item.

**Order Status (Sprint 1):** `PENDING`, `ACCEPTED`, `REJECTED`, `CANCELLED`. Only `PENDING` can transition to another status. There is no Payment Status. Operator Accept converts inventory from Reserved to Sold once; Operator Reject requires a reason and releases Reserved to Available once; owning Customer Cancel releases Reserved to Available once. Submitted item/address/pin/price snapshots remain immutable through these status changes.

**Charge invariants:** `subtotal = sum(quantity × authoritative unit price snapshot)`; `order_total = subtotal + shipping_fee`; no amount from the browser is authoritative. Determine shipping-band eligibility from unrounded distance; round only the user-visible distance.

## 7. Explicit negative and boundary cases

- Login with invalid password and anonymous `/me`/orders; customer attempts Operator Accept/Reject; Customer B reads/cancels Customer A's Order.
- Blank address/recipient, invalid Thai phone/postcode, missing pin/acknowledgement, invalid province; **edit any confirmed destination field or pin and try to submit without reconfirmation**; stale confirmed revision/quote rejected with no Order. Map unavailable with/without retained pin; no geocoding-based inference.
- Raw line with zero, negative, fractional, nonnumeric or missing quantity; duplicated lines totaling 11; stock less than requested; invalid item; manipulated client prices/totals; no partial Order or reservation.
- Replayed same checkout key (including concurrent retries) must not create a second Order; changed payload with reused key must fail without changing the original. **Proposed** wire responses, key retention and in-flight semantics are in System SDS §6 and require Part E approval; they are not new frozen Business Rules.
- Boundary distance exactly 50 km and exactly 300 km versus immediately beyond thresholds; fee computed backend from unrounded distance.
- Two simultaneous claims for the last stock unit, multi-line transaction rollback, competing Accept/Reject/Cancel, injected failure after status write, duplicate release/deduction, Reject with whitespace reason, attempted edit of already submitted Order.

## 8. Decisions, open engineering inputs and change control

**Frozen:** DEC-001 through DEC-034, MAP-COST-001, approval FRZ-S1 (2026-11-02). They supersede the earlier pickup/no-shipping/late-cancellation assumptions. **Later-sprint functionality** is a plan allocation from the instructor theme, not a new frozen business design.

**Engineering inputs requiring explicit recorded resolution before corresponding contract/code:** verify exact fixed origin latitude/longitude from a citable map/source; inspect actual group repo and `AGENTS.md`; choose/confirm stack, passwords/sessions, idempotency key lifetime and changed-payload/in-flight behavior, pin/Thailand boundary validation, address field limits/digit classes, province dataset source, server error shapes, failure response on backend distance calculation, and migrations/seed methods. These are design/verification questions, not a reason to unfreeze approved scope.

**Change control:** a new scope decision after FRZ-S1 must have a dated entry, proposer/approver, reason, affected FR/BR/NFR/F/AC/TEST IDs, sprint/effort impact and migration/compatibility implications. Revise source and derived documents without overwriting decision history. Obtain second student review of these engineering drafts.

## 9. Instructor assessment and deliverable boundaries

`project_deliverable_1_v1.pdf` requires complete **A** SRS, **B** feature inventory/traceability, **C** System-Level SDS, **D** three-sprint plan, **E** versioned feature-level SDS/UI/API/data + AC/STS/DoD **before code**, and **F** a working UI→API→persistent DB slice with real test results, linked Issues and reviewed PRs, and a self-contained PDF by **2026-11-08 23:59**. **Only A–D are being drafted here**; E/F are pending. Lab 5 owns deployed Docker/GCP/CI/CD work; don't state that they are already delivered.
