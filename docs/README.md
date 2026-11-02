# Campus Store — CPE334 Engineering Documents

**Documentation snapshot:** 2026-11-02 · **Business scope frozen:** FRZ-S1, DEC-001 through DEC-034 + MAP-COST-001.

**Document status:** These are substantive **DRAFTS FOR TEAM REVIEW**, not instructor-approved specifications or a working software release. No application code, automated test, Issue, PR or release was produced by this documentation-only session.

| Instructor Part | Primary file | Status and purpose |
|---|---|---|
| Business decision baseline | [`group-project/requirements-log.md`](group-project/requirements-log.md) | FRZ-S1 approved; 34 frozen DEC, mapping-cost constraint, known gaps, change control |
| Domain terminology | [`group-project/CONTEXT.md`](group-project/CONTEXT.md) | Previously confirmed domain vocabulary, unchanged by scope freeze |
| **A** — SRS | [`srs.md`](srs.md) | Draft: 21 FR, 26 BR, 11 NFR; actors, scope, rules and measurable verification proposals |
| **B** — Feature inventory | [`features.md`](features.md) | Draft: F-01..11 with assigned sprint, dependencies and 23 planned S1 AC/TEST pairs |
| **B** — Traceability | [`traceability.md`](traceability.md) | Draft: FR/BR/NFR→features/sprint/verifications; DEC-001..034 and MAP-COST-001→requirements |
| **C** — System SDS | [`system-sds.md`](system-sds.md) | Draft: architecture, domain state, DB shape, APIs, role matrix, configs, GCP plan, risks |
| **D** — 3-Sprint Plan | [`sprints/plan.md`](sprints/plan.md) | Draft: Sprint 1 by 8 Nov, Sprint 2 by 29 Nov, Sprint 3 by 13 Dec, dependencies and nine proposed Issues |
| Implementation readiness | [`sprints/sprint-1/implementation-handoff.md`](sprints/sprint-1/implementation-handoff.md) | Read-only handoff: mismatch audit of old plan, gating work before code, feature contract checklist |

## Instructor-source provenance

- `group_work_specs_v1.pdf`: §§5–8, particularly Theme #4 E-Commerce/Ordering and Sprint 1 example. The supplied version is labelled *Draft for instructor review*, issued 6 Oct 2026.
- `project_deliverable_1_v1.pdf`: Parts A–F, required feature contracts/evidence and 8 Nov 2026 at 23:59 deadline.
- `project_deliverable_2_v1.pdf`: 29 Nov 2026 deadline, Sprint 2 complete and Sprint 3 contracts + progress.
- `project_final_delivery_v1.pdf`: 13 Dec 2026 final checkpoint and release/demo expectations.
- Source PDFs were supplied in `group_project_spec_files.zip`; they are **not duplicated** in this documentation pack. Confirm with current instructor/LEB2 notices before submission.

## How to proceed without falsely claiming readiness

1. Obtain teammate review and inspect the **actual group repository** and its `AGENTS.md` (not done by this task); verify the shipping-origin coordinates and any revised course instructions.
2. Accept/revise the **proposed engineering decisions** in System SDS and measurable NFR targets without silently changing FRZ-S1 business rules.
3. Author **Part E** (F-01..06 Feature-Level SDS, UI/API/data contract, numbered AC/STS and DoD) and preserve spec-before-code history.
4. Only after authorization, implement and execute tests, stage/review/release and collect **Part F real evidence**. Package a **self-contained PDF** with complete Parts A–E and actual Part F evidence for LEB2; these Markdown drafts **are not that PDF**.

No code, build, test run, GitHub Issue/PR, deployment or submission is implied by a document existing.
> Historical November 1 plan is intentionally excluded from this initial repository import because its implementation instructions conflict with the frozen business scope. See `sprints/sprint-1/implementation-handoff.md` for the conflict audit.
