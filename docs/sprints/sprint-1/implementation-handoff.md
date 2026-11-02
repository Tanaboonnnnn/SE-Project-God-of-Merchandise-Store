# Campus Store Sprint 1 — Pre-Implementation Handoff (No Code)

> **Status:** Planning handoff, 2026-11-02. **Not execution-ready.** Business scope FRZ-S1 is approved; actual group repository/`AGENTS.md`, implementation stack, exact dispatch-origin coordinates, feature-level contracts and teammate review have not been verified in this documentation-only task. No application code, GitHub Issues/PRs, build or tests were created or executed.
>
> **Supersedes:** `docs/superpowers/plans/2026-11-01-ecommerce-sprint-1.md`, an outdated, pre-freeze plan. Do not follow its code steps until its conflicts are resolved and this handoff is converted into a repo-grounded implementation plan.

## 1. Authoritative source order

1. Instructor's current authoritative project handouts (reconfirm against LEB2/TA; provided `group_work_specs_v1.pdf` carries *Draft for instructor review*).
2. Frozen team business decisions: `docs/group-project/requirements-log.md` DEC-001..034, MAP-COST-001, FRZ-S1.
3. Terminology: `docs/group-project/CONTEXT.md`.
4. Derived engineering drafts: `docs/srs.md`, `docs/features.md`, `docs/traceability.md`, `docs/system-sds.md`, `docs/sprints/plan.md`.
5. After actual repository inspection: versioned F-01..F-06 feature contracts with signed-off API/UI/data, AC, STS and DoD. Code changes only after this gate.

## 2. Audit of the older plan vs frozen scope

| Old Nov 1 plan (observed) | Why it is wrong under FRZ-S1 | New required direction | Reference |
|---|---|---|---|
| Campus **counter pickup** and preorders (§0) | DEC-002 explicitly excludes pickup | **Delivery only** for customers across Thailand | DEC-002/003 |
| **No delivery fee** in S1 (§0, §11) | Mandatory map/distance shipping in first deliverable | Required pin/map, server great-circle distance, 40/80/150 THB fee per Order | DEC-004..011/013/029..031 |
| Initial `SUBMITTED` state (§0, BR-05, demo) | Canonical first status is **Pending** | `PENDING`, `ACCEPTED`, `REJECTED`, `CANCELLED` | DEC-016..019 |
| All cancellation deferred to Sprint 2 (§0, FR-07 row) | Owning Customer may cancel **Pending** in S1 | Customer cancel, reservation release exactly once; no Accepted cancel | DEC-018/019/032 |
| Operator only accepts (§0, F-05) | Operator must also **Reject with nonblank reason** and release reservation atomically | Operator shared queue with Accept and reasoned Reject | DEC-017/027/033 |
| Catalogue/ordering five-feature split (§2, Issues) | Missing dedicated geo, idempotency, cancellation and reject contracts | Six S1 features F-01..06; nine proposed Issues in `docs/sprints/plan.md` | DEC-008/026 and feature inventory |
| No duplicate whole-Order retry protection | Request retries risk overselling/duplicates | Idempotency Key on backend and frontend button lock | DEC-026 |
| Quantity only `>= 1` in older plan | Per-option **1–10** and sum duplicate lines first | Raw-line validation, normalized one line per option, all-or-nothing | DEC-023..025 |
| No map/recipient address | Missing confirmed delivery contact, Thai address, pin and confirmation | Structured fields, phone/postcode rules, province list, map outage fallback | DEC-013/029..031 |
| Stock deduct simplification | Reservation vs sale vs release needs full lifecycle | Available→Reserved on submit; Reserved→Sold on Accept; Reserved→Available on Reject/Cancel | DEC-016..019 |
| Future simulated payment labels mixed with S1 | S1 explicitly **has no Payment Status** | Plan F-09 simulated payment for S2 only | DEC-028 |
| Catalog and price data unspecific | Team approved seed fixture | 6 products, 12 sellable options, per-unit prices 250/290/180/70/25/220 THB, 160 Available | DEC-014/015/020..022 |

The older plan also refers to a **greenfield proposed React/Express/Prisma structure**, not to inspected code. Do **not** assume those paths or scripts exist; inspect the real group repository before file-level implementation planning.

## 3. Documentation-only artifacts created in this phase

- `docs/srs.md`: 21 FR, 26 BR, 11 proposed measurable NFR targets; stable IDs and decisions.
- `docs/features.md`: F-01..F-11 inventory; F-01..F-06 planned AC and TEST IDs (not executed).
- `docs/traceability.md`: complete FR/BR/NFR and 34 DEC/MAP-COST mappings, current execution status.
- `docs/system-sds.md`: proposed architecture, relational entities, role matrix, state machine, API inventory, data integrity, GCP plan, risks.
- `docs/sprints/plan.md`: 3-sprint allocation, instructor deadlines, 9 planned work packages, dependencies, role/review process, evidence gates.
- `docs/group-project/requirements-log.md`: record of business-scope freeze approval and change control.

## 3.1. Sprint 1 feature-to-contract checklist

| Feature | Core contract ownership | Planned acceptance anchors |
|---|---|---|
| F-01 | Authentication/session and server authorization | AC-F01-01..03; TEST-F01-01..03 |
| F-02 | Six catalog products, variants, seeded inventory and prices | AC-F02-01..02; TEST-F02-01..02 |
| F-03 | Address validation, map pin, server shipping tariff and failure/reconfirmation | AC-F03-01..05; TEST-F03-01..05 |
| F-04 | Atomic checkout, snapshots, duplicate-line normalization, Idempotency Key | AC-F04-01..05; TEST-F04-01..05 |
| F-05 | Owner My Orders, status/reason, Pending cancellation and immutable contents | AC-F05-01..04; TEST-F05-01..04 |
| F-06 | Shared Operator queue and Pending Accept/Reject + reasons | AC-F06-01..04; TEST-F06-01..04 |

The rows are **handoff anchors**. Concrete request/response schemas, edge cases, STS preconditions and expected outputs must be reviewed in individual Part E documents.

## 4. Engineering inputs to resolve next (without changing frozen business scope)

| Gate | Required evidence before implementation |
|---|---|
| Course authority | Confirm the currently assigned Theme #4 is accepted for the team and current handout version is authoritative; preserve source/date |
| Repository | Actual group repo URL, branch convention, `AGENTS.md`, existing source/build/test scripts, dependencies, DB, test baseline; observe only until code work approved |
| Source-verified origin | Fixed latitude and longitude around KMUTT Bangmod campus center, exact reference and date (do not invent coordinates) |
| Shipping | Verified origin, deterministic Haversine convention, fee boundaries, non-Thai pin policy, numeric validity, backend computation failure behavior |
| Thai address | Province dataset/list and validation, field max lengths, phone/postal digits, cart/form persistence on map failure and explicit reconfirmation UX |
| Auth | Chosen session/cookie/token lifecycle, logout/expiry, password hashing, CSRF/CORS and local seed account handoff without committed secrets |
| Inventory and checkout | DB isolation/locking, idempotency key scope/retention/in-flight/changed-payload responses, query error structure, immutable snapshots, exact transitions |
| Feature contracts | Each S1 F-01..06 needs versioned Feature SDS + UI/API/data specs + AC + STS with test inputs/preconditions/expected output and DoD, reviewed *before* application code |
| Team review | Second member independently reviews scope/architecture/contract and participates across coding/tests/PR reviews |

## 5. Next-agent work order (planning only; no app code permitted in this handoff)

1. **Read the five derived docs and Decision Log;** confirm 34 frozen decisions and MAP-COST-001, never copy rejected assumptions from old plan.
2. **Read-only inspect the actual group repo and `AGENTS.md`;** identify file paths and existing code, tests, commands, current branch/working tree. Report discrepancies with proposed SDS rather than unilaterally migrating the stack.
3. **Verify the fixed geographic origin and local mapping policy,** capture auditable provenance and unit-test values. If provider/instructor constraints changed, raise a dated change request.
4. **Review Part A–D with teammate** and resolve engineering design gates; record review notes and version.
5. **Produce Part E contracts** for F-01..F-06 with API, UI, DB migrations, permissions, error cases, AC/STS/DoD and checkable tests, before implementation.
6. **Replace this handoff with a repo-grounded, task-by-task code implementation plan** matching actual file paths, TDD commands, test expectations and review checkpoints. Obtain team approval; do not execute within the documentation-only task.
7. When coding is separately authorized, use the instructor's feature branch → teammate-reviewed PR → sprint staging → tested main release process, and retain real TDD/test/PR evidence for Part F.

## 6. Minimal verification matrix for contract reviewers

| Capability | Planned test outcome |
|---|---|
| Auth/ownership | Customer B cannot inspect Customer A's Order or rejection reason; Customer cannot accept/reject |
| Catalog | Exactly 6 products / 12 options; starting 160 Available; shirt sizes independent; correct seeded prices |
| Delivery | Missing/unconfirmed pin blocked; invalid phone/postcode rejected; map outage persists form; fee at 50/300km boundaries |
| Order | Duplicate 3+2 notebooks persists one line qty 5; 6+5 rejected; invalid line rolls back all; price tamper ignored/rejected |
| Concurrency | Two requests for last unit cannot both reserve; two retry requests with same key cannot create duplicate order |
| Status | Accept Reserved→Sold once; Reject with nonblank reason releases once; owner Cancel Pending releases once; terminal state retry no extra mutation |
| Persisted truth | Customer refresh shows latest state; order snapshots persist across restart; frontend-only success is not sufficient |

## 7. No-claim / stop conditions

**Stop before code** until the actual repository is identified, engineering gaps and Part E contracts are reviewed, and team authorizes implementation. **Do not** mark Part A–D as instructor-approved; do not invent the codebase, PRs, tests, Git release SHAs, deployment or LEB2 submission. If six-calendar-day time pressure requires fewer features, formally request and approve a scope change; never drop DEC-008 map/shipping or other frozen features silently.
