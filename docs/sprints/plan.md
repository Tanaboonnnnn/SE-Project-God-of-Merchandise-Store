# Campus Store — Three-Sprint Delivery Plan (Part D)

> **Status:** Draft v0.1, 2026-11-02. Business scope of **Sprint 1 is frozen by FRZ-S1 (DEC-001..034 and MAP-COST-001)**. Actual code/repository state, team capacity estimates and review are **not verified** in this document. No implementation or test execution was performed for this planning update.
>
> **Instructor source:** `group_work_specs_v1.pdf` §§4,7–9 and `project_deliverable_1_v1.pdf` §3.4. Source master is labelled *Draft for instructor review*. Check for latest authoritative version before final submission.

## 1. Product baseline and course milestones

The product is a **one-store delivery-only Campus Store**. Sprint 1 must show an authenticated Customer creating and retrieving a persisted multi-item order and Operator Accept/Reject, with authoritative price/quantity/inventory rules and backend role/ownership checks. The **approved additions** are nationwide delivery-address/map-pin selection, no-fee public tiles, server-side straight-line shipping bands, immutable order snapshots, stock reservation and release, Customer Pending cancellation, Idempotency Key and map-error recovery. These additions are **not optional** unless an explicit post-freeze scope change is approved.

| Date (Asia/Bangkok, 23:59 deadline where given) | Checkpoint | Expected deliverable / evidence |
|---|---|---|
| **8 Nov 2026** | Project Deliverable 1 (80 pts) | A–D system baseline, Part E Sprint 1 contracts, Part F running UI→API→DB release on `main` with real tests/PR review, self-contained PDF |
| **15 Nov 2026** | Group Lab 5 | Docker packaging, CI/CD, GCP deployment of accepted Sprint 1 increment (course resource access permitting) |
| **22 Nov 2026** | Group Lab 6 | Cross-team UAT and recorded defects against integrated deployed product |
| **29 Nov 2026** | Project Deliverable 2 (70 pts) + Group Lab 7 | All Sprint 2 features complete; Sprint 3 contracts and demonstrable progress; integrated deployed candidate; maintenance/regression evidence |
| **13 Dec 2026** | Final project delivery (100 pts) | Complete integrated product, QA/release evidence, final PDF, deployed app and demonstration video per final handout |

The **course theme** states: later sprints cover **catalog editing**, **cancellation/fulfillment**, **simulated payment** and **reporting**. Because DEC-018 brought Customer Pending cancellation to Sprint 1, Sprint 2 focuses on further lifecycle/fulfillment instead of deferring the already-approved cancellation. Real payments remain out of scope throughout the project.

## 2. Sprint goals, feature allocation and DoD

| Sprint | Goal | Features and boundaries | Dependencies | Completion criterion |
|---|---|---|---|---|
| **1: by 8 Nov** | Small local releasable domain slice, authenticated and tested end-to-end | **F-01** seeded auth/roles, **F-02** six-product catalog/variants/stock, **F-03** destination/map/fee, **F-04** immutable atomic ordering/idempotency, **F-05** owner order list/status/cancel, **F-06** shared Operator Accept/Reject + reason | 1→2→3→4→5/6; verifiable origin/pin policy and DB transactions | Correct S1 contracts, AC/STS/DoD, working UI→API→persistent DB, negative/race tests, local setup, teammate-reviewed PRs, accepted `main` SHA, Part A–F PDF |
| **2: by 29 Nov** | Complete core commerce expansion and Lab 5 deployment | **F-07** Operator catalog maintenance, **F-08** fulfillment / delivery state transitions, **F-09** simulated payment statuses (no money); keep F-01..06 regression green | S1 release accepted; GCP access via Lab 5; agree S2 state/permission contracts; UAT issues triaged | All planned S2 capabilities contract-first, implemented and tested; deployed integrated candidate on `main`; Sprint 3 contracts and real started F-10/F-11 work shown in Deliverable 2 |
| **3: progress by 29 Nov; complete by 13 Dec** | Reporting, quality hardening and final release | **F-10** order/sales summaries and authorized aggregates; **F-11** UX, failure handling, UAT/maintenance fixes, regression/security/performance/accessibility, final QA | Fulfillment/payment/catalog state definitions and persistent history from S2; UAT & defects | Source-grounded reporting and reliability tests, no high-severity unresolved defect without disclosure, final deployed tagged release, evidence PDF + ≤10min final video per instructor handout |

**Part E preparation:** Before coding any S1 feature, a versioned Feature-Level SDS must define workflows, UI/API/data, business rules, auth, errors/edge cases, stable AC, corresponding STS/TEST, DoD. The feature inventory currently lists **planned** AC/TEST names; these are not completed Part E documents. The same discipline applies to F-07..F-11 before their implementation.

## 3. Sprint 1 dependency and release path

```mermaid
flowchart TD
    Gate[Repo/AGENTS + origin coordinate + course revision check] --> Docs[A-D review and stable FR/BR/NFR/F IDs]
    Docs --> Contracts[Part E: F-01..F-06 SDS, UI/API/data + AC/STS/DoD]
    Contracts --> Auth[F-01 Auth/roles]
    Contracts --> Catalog[F-02 Catalog/stock]
    Auth --> Geo[F-03 Map/delivery/fee]
    Catalog --> Geo
    Geo --> Submit[F-04 Atomic order submission/idempotency]
    Submit --> Customer[F-05 My Orders/Cancel]
    Submit --> Operator[F-06 Operator Accept/Reject]
    Customer --> Verify[Integration, negative/race tests, review, staging]
    Operator --> Verify
    Verify --> Release[Reviewed staging->main release SHA]
    Release --> PDF[Part A-F PDF + real evidence + LEB2]
```

**Hard gates before application edits:** identify actual group repository and its `AGENTS.md`; compare existing stack and conventions; verify exact shipping origin; finalize engineering decisions that cannot be safely guessed; prepare and review F-01..06 Part E contracts. The earlier plan dated 1 Nov is **superseded** because it says counter pickup, no shipping and later cancellation. Do not follow it for code execution.

## 4. Short-window Sprint 1 schedule (planning targets, not promises)

| Date | Work and validation gate | Assignment / reviewer intent |
|---|---|---|
| **2 Nov** | FRZ-S1 approved; draft Part A–D; record plan conflict, decision trace, evidence gaps | Both review requirements and sign off the baseline; no code in this documentation session |
| **3 Nov** | Inspect group repo + `AGENTS.md`, confirm origin/datasets/stack; finalize S1 F-01..F-06 contracts and STS; create/link scoped Issues | Both contribute contracts; each reviews the other's proposed design |
| **4 Nov** | Build/test identity and catalog foundation with migrated/seeded DB (only after contract gate) | Split vertical slices; reciprocal PR review |
| **5 Nov** | Implement/test map/checkout distance/fee and transactional order submission + snapshots/idempotency | Shared review of backend cost/stock test cases |
| **6 Nov** | Complete Customer order list/cancel and Operator Accept/Reject; cover permission failures, invalid input and races | Both write tests and inspect each other's status changes |
| **7 Nov** | Integration/staging, regression/E2E, review release PR, record SHA, gather real evidence | Both verify and review; feature scope hard stop, fix defects |
| **8 Nov** | Reproduce setup/build/tests on release SHA; compile *self-contained* Parts A–F PDF including actual counts/PRs/screenshots; submit through LEB2 before 23:59 | Both sign off what is actually passing, disclose failures/skips |

**Risk assessment:** **HIGH**: six dependent Sprint 1 feature groups plus a new map/shipping subsystem and multi-actor stock concurrency are ambitious for two students in six calendar days. The schedule is a tentative critical path; **do not assert it is feasible without checking existing code and actual team availability**. If it fails, request a formal FRZ-S1 scope change (with impacts) rather than silently omitting approved features.

## 5. Ownership and cross-review (proposed, not assigned in GitHub)

| Workstream | Student A lead (proposed) | Student B lead (proposed) | Shared verification |
|---|---|---|---|
| Requirements & design | SRS, F-01/05/06 contracts | SDS, traceability, F-02/03/04 contracts | Both co-review, both can explain any FR/AC/TEST |
| Implementation | F-01, F-05, F-06 | F-02, F-03, F-04 | Pair on integration and concurrent inventory behavior |
| Test and review | Own auth/ownership, critique stock rules | Own pricing/stock, critique operator lifecycle | Each reviews other's PR; neither self-approves |
| Release, evidence, defect triage | Both | Both | Tag SHA, test counts, PDF evidence and AI-use statement |

The instructor requires two people to participate in requirements, design, implementation, testing and review; permanent role segregation into 'docs only' versus 'code only' is **not** acceptable.

## 6. Candidate GitHub Issues (9 work packages; none created by this document)

| Issue | Scope/feature references | Prerequisite | Acceptance gate and evidence |
|---|---|---|---|
| GP-01 | Repo readiness, stack, origin coordinate, env/seed strategy | None | Repo/`AGENTS.md` audit and provenance; branch workflow recorded |
| GP-02 | Part A–D source-grounded baseline | FRZ-S1 | Both review and agree stable FR/BR/NFR/F/DEC trace |
| GP-03 | Part E all F-01..F-06 contracts | GP-01/02 | Versioned SDS/UI/API/data/AC/STS/DoD before implementation |
| GP-04 | F-01 auth/roles | GP-03 | Valid/invalid login, logout, unauth/role tests |
| GP-05 | F-02 catalog seed/stock | GP-03 | 6 products, 12 options, 160 initial units + prices |
| GP-06 | F-03 map/delivery/fee | GP-04/05 | Mandatory pin+confirmation, free map attribution, bands/boundaries and outage tests |
| GP-07 | F-04 order write/idempotency | GP-06 | Real DB transaction, snapshots, normalization, replay and oversell tests |
| GP-08 | F-05/06 Customer and Operator transitions | GP-07 | Owner/cancel and Operator accept/reason reject races verified |
| GP-09 | Integration and Part F release/PDF | GP-04..08 | Reviewed PRs into staging then main, SHA, real test/evidence index and LEB2 PDF |

IDs `GP-01..09` are **planning labels**. Do not claim actual GitHub Issue numbers or URLs. Each implementation Issue should link Feature, requirements, approved DEC, contract, AC and TEST IDs and identify author/reviewer.

## 7. Sprint 2 and 3 design questions (not reasons to unfreeze Sprint 1)

- F-07: authorized catalog changes, constraints for price changes vs immutable Order Item price snapshots, restocking audit and stock reset prevention.
- F-08: fulfillment lifecycle states after acceptance, courier simulation vs physical delivery proof, any post-acceptance cancellation/return policy (explicitly undecided under DEC-019).
- F-09: simulated payment status values, actor permitted to change them, relation to Accepted/Cancelled/Rejected, and audit; **never** request actual money.
- F-10: sales summary treatment of Pending/Rejected/Cancelled vs Accepted, time-zone/window, inventory/sales aggregation and query authorization.
- F-11: accessibility, map-error/resilience, performance evidence, privacy minimization, cross-sprint regressions, UAT findings and release readiness.

Draft each feature contract when the sprint is approaching, preserving FR/BR/NFR/F/AC/TEST trace and recording approved scope changes.

## 8. Required release evidence / status

| Evidence / required artifact | Current status (2 Nov) | Who must supply it |
|---|---|---|
| Business decisions DEC-001..034 + MAP-COST-001 and approved FRZ-S1 | **Approved; saved in Decision Log** | Team representative, teammate review requested |
| SRS, feature inventory, traceability, SDS and this plan (Parts A–D) | **Draft only; pending review** | Both |
| F-01..06 Feature SDS + UI/API/data contracts + AC/STS/DoD (Part E) | **Not authored as standalone contracts** | Both before code |
| Running app UI→API→DB, tests, negative/owner/race evidence (Part F) | **Not verified; not executed during this task** | Both during implementation |
| Actual group repo URL, branch/PR/issue and `main` release SHA | **Unknown / not verified** | Both once repo is inspected |
| Test command output with pass/fail/skip + red→green TDD evidence | **No execution evidence** | Both, cite exact release SHA |
| Self-contained A–F LEB2 submission PDF | **Not created** | Both before 8 Nov deadline |
| Docker/GCP and pipeline | **Lab 5 future checkpoint, not S1 requirement** | Both in later lab |

**Release gate:** Only claim Sprint 1 Done after actual code tests, persisted DB data, review trail and the PDF submission evidence exist. This file is a planning artifact, not proof of successful implementation.
