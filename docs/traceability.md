# Campus Store — Requirements Traceability Matrix (Part B)

> **Status:** Draft v0.1 (2026-11-02). **Business scope is frozen via FRZ-S1**, but this engineering artifact has not been reviewed by the second team member or compared with the actual group repository. No application code or tests were executed in preparing this draft.
> **Normative business sources:** [`group-project/requirements-log.md`](group-project/requirements-log.md) DEC-001..034 + MAP-COST-001; [`group-project/CONTEXT.md`](group-project/CONTEXT.md). **Instructor references:** `group_work_specs_v1.pdf` §§5–8; `project_deliverable_1_v1.pdf` Parts A–D (the former is labelled *Draft for instructor review*).

## 1. Coverage status rules

- **Planned:** requirement is linked to a feature and verification design, but no evidence of execution exists in this documentation-only phase.
- **Deferred:** later-sprint requirement has a feature and sprint but its acceptance/test contract is intentionally written nearer the sprint.
- **Approved:** the business requirement is backed by FRZ-S1 / specific DEC, **not** an application verification result.
- **Verified:** reserved for real test/run evidence tied to a release SHA; **no rows currently qualify**.

## 2. FR → feature → sprint → acceptance/test mapping
| FR ID | Feature(s) | Sprint | AC mapping | Verification status |
| --- | --- | --- | --- | --- |
| FR-01 | F-01 | 1 | AC-F01-01..03 | Planned (not executed) |
| FR-02 | F-01,F-05,F-06 | 1 | AC-F01-02; AC-F05-03; AC-F06-04 | Planned (not executed) |
| FR-03 | F-02 | 1 | AC-F02-01 | Planned (not executed) |
| FR-04 | F-02 | 1 | AC-F02-02 | Planned (not executed) |
| FR-05 | F-03 | 1 | AC-F03-01 | Planned (not executed) |
| FR-06 | F-03 | 1 | AC-F03-02; AC-F03-05 | Planned (not executed) |
| FR-07 | F-03,F-04 | 1 | AC-F03-03; AC-F04-01 | Planned (not executed) |
| FR-08 | F-03 | 1 | AC-F03-04 | Planned (not executed) |
| FR-09 | F-04 | 1 | AC-F04-01; AC-F04-03 | Planned (not executed) |
| FR-10 | F-04 | 1 | AC-F04-01; AC-F04-02 | Planned (not executed) |
| FR-11 | F-04 | 1 | AC-F04-04 | Planned (not executed) |
| FR-12 | F-05 | 1 | AC-F05-01; AC-F05-03 | Planned (not executed) |
| FR-13 | F-05 | 1 | AC-F05-02; AC-F05-03 | Planned (not executed) |
| FR-14 | F-06 | 1 | AC-F06-01 | Planned (not executed) |
| FR-15 | F-06 | 1 | AC-F06-02 | Planned (not executed) |
| FR-16 | F-06 | 1 | AC-F06-03; AC-F06-04 | Planned (not executed) |
| FR-17 | F-04,F-05 | 1 | AC-F05-04 | Planned (not executed) |
| FR-18 | F-07 | 2 | AC-F07 (define in S2 contract) | Deferred: contract not yet authored |
| FR-19 | F-08 | 2 | AC-F08 (define in S2 contract) | Deferred: contract not yet authored |
| FR-20 | F-09 | 2 | AC-F09 (define in S2 contract) | Deferred: contract not yet authored |
| FR-21 | F-10 | 3 | AC-F10 (define in S3 contract) | Deferred: contract not yet authored |

## 3. BR → feature → sprint → acceptance/test mapping

| BR ID | Feature(s) | Sprint | AC mapping / verification method | Verification status |
| --- | --- | --- | --- | --- |
| BR-01 | F-01,F-05 | 1 | AC-F05-03 | Planned (not executed) |
| BR-02 | F-06 | 1 | AC-F06-01; AC-F06-04 | Planned (not executed) |
| BR-03 | F-05,F-06 | 1 | AC-F05-02; AC-F06-02..04 | Planned (not executed) |
| BR-04 | F-02 | 1 | AC-F02-01; AC-F02-02 | Planned (not executed) |
| BR-05 | F-02,F-04 | 1 | AC-F02-01; AC-F04-02 | Planned (not executed) |
| BR-06 | F-02 | 1 | AC-F02-02 | Planned (not executed) |
| BR-07 | F-04 | 1 | AC-F04-03 | Planned (not executed) |
| BR-08 | F-04 | 1 | AC-F04-02 | Planned (not executed) |
| BR-09 | F-03,F-04 | 1 | AC-F04-03 | Planned (not executed) |
| BR-10 | F-04 | 1 | AC-F04-01; AC-F04-02 | Planned (not executed) |
| BR-11 | F-04 | 1 | AC-F04-01; AC-F04-05 | Planned (not executed) |
| BR-12 | F-06 | 1 | AC-F06-02; AC-F06-04 | Planned (not executed) |
| BR-13 | F-06 | 1 | AC-F06-03; AC-F06-04 | Planned (not executed) |
| BR-14 | F-05 | 1 | AC-F05-02; AC-F05-03 | Planned (not executed) |
| BR-15 | F-04,F-05 | 1 | AC-F05-04 | Planned (not executed) |
| BR-16 | F-03 | 1 | AC-F03-01; AC-F03-04 | Planned (not executed) |
| BR-17 | F-03 | 1 | AC-F03-01 | Planned (not executed) |
| BR-18 | F-03,F-04 | 1 | AC-F03-03 | Planned (not executed) |
| BR-19 | F-04 | 1 | AC-F04-04 | Planned (not executed) |
| BR-20 | F-04,F-06 | 1 | Scope review | Planned (not executed) |
| BR-21 | F-05 | 1 | AC-F05-01 | Planned (not executed) |
| BR-22 | F-03 | 1 | AC-F03-02; Scope review | Planned (not executed) |
| BR-23 | F-01 | 1 | AC-F01-01 | Planned (not executed) |
| BR-24 | F-03 | 1 | AC-F03-02 | Planned (not executed) |
| BR-25 | F-04,F-05,F-06 | 1 | AC-F05-02; AC-F06-02..03 | Planned (not executed) |
| BR-26 | F-03 | 1 | AC-F03-05 | Planned (not executed) |

## 4. NFR → feature → sprint → evidence mapping

| NFR ID | Responsible feature(s) | Checkpoint sprint | Test or inspection | Status |
| --- | --- | --- | --- | --- |
| NFR-01 | F-01,F-05,F-06 | 1 | TEST-F01-02; TEST-F05-03; TEST-F06-04 | Planned; no measured results |
| NFR-02 | F-01 | 1 | TEST-F01-01; security inspection | Planned; no measured results |
| NFR-03 | F-04,F-05 | 1 | TEST-F04-01; TEST-F05-01 | Planned; no measured results |
| NFR-04 | F-04,F-05,F-06 | 1 | TEST-F04-05; TEST-F06-04 | Planned; no measured results |
| NFR-05 | F-04 | 1 | TEST-F04-04 | Planned; no measured results |
| NFR-06 | F-01,F-02,F-03,F-04,F-05,F-06 | 1 | release evidence check (Part F) | Planned; no measured results |
| NFR-07 | F-01,F-03,F-04 | 1 | configuration review | Planned; no measured results |
| NFR-08 | F-02,F-05 | 1 | TEST-PERF-01 (planned) | Planned; no measured results |
| NFR-09 | F-01,F-02,F-03,F-05 | 1 | TEST-UX-01 (manual planned) | Planned; no measured results |
| NFR-10 | F-03 | 1 | TEST-F03-05 | Planned; no measured results |
| NFR-11 | F-01,F-04,F-11 | 2 | SDS review S1; Lab5 pipeline verification S2 | Planned; no measured results |

## 5. Approved decisions → normative requirement → feature

Every confirmed business DEC and MAP-COST-001 is mapped; this is the scope-change audit trail. A reference to a BR does not assert that software satisfies it.

| Frozen decision | Controlling FR/BR | Primary responsible feature |
| --- | --- | --- |
| DEC-001 | BR-04 | F-02 |
| DEC-002 | BR-22 | F-03 |
| DEC-003 | BR-22 | F-03 |
| DEC-004 | BR-18 | F-03 |
| DEC-005 | BR-18 | F-03 |
| DEC-006 | BR-18 | F-03 |
| DEC-007 | BR-18 | F-03 |
| DEC-008 | FR-07 | F-03 |
| DEC-009 | BR-24 | F-03 |
| DEC-010 | BR-18 | F-03 |
| DEC-011 | BR-18 | F-03 |
| DEC-012 | FR-01; BR-23 | F-01 |
| DEC-013 | FR-05; BR-16 | F-03 |
| DEC-014 | BR-04 | F-02 |
| DEC-015 | FR-04; BR-04 | F-02 |
| DEC-016 | BR-11; BR-12 | F-04/F-06 |
| DEC-017 | BR-13 | F-06 |
| DEC-018 | BR-14 | F-05 |
| DEC-019 | BR-14; BR-15 | F-05 |
| DEC-020 | BR-05 | F-02 |
| DEC-021 | BR-05 | F-02 |
| DEC-022 | BR-06 | F-02 |
| DEC-023 | BR-07 | F-04 |
| DEC-024 | BR-09 | F-04 |
| DEC-025 | BR-08 | F-04 |
| DEC-026 | BR-19 | F-04 |
| DEC-027 | BR-02 | F-06 |
| DEC-028 | BR-20 | F-04/F-06 |
| DEC-029 | BR-26 | F-03 |
| DEC-030 | BR-16 | F-03 |
| DEC-031 | BR-17 | F-03 |
| DEC-032 | BR-15 | F-04/F-05 |
| DEC-033 | BR-13 | F-06 |
| DEC-034 | BR-21 | F-05 |
| MAP-COST-001 | BR-24; NFR-10 | F-03 |
## 6. Coverage review and known gaps

- **Trace inventory:** 21 FR, 26 BR, 11 NFR, 11 features (F-01..F-11); all 34 DEC + MAP-COST-001 map to a controlling requirement and responsible feature.
- **Sprint 1:** every approved behavioral FR/BR points to a named F-01..F-06 and a planned AC or a bounded scope review; test execution remains **Not Run**.
- **Instructor theme later-sprint baseline:** FR-18..21 (catalog administration, fulfillment, simulated payment, reporting) map to F-07..F-10 with explicit sprint placement. Their precise business transitions are **not yet confirmed**.
- **NFR verification:** proposed targets, not measured performance, security, persistence or accessibility evidence. Need tested release SHA and actual result rows before course submission.
- **Engineering unknowns:** fixed shipping origin coordinates, actual repo conventions, pin geofence, session/auth lifecycle, idempotency in-flight and payload-mismatch semantics, API schemas, precise province dataset and field limits, map service outage and fee-compute failure cases. Track them in SDS and per-feature contracts, not as invented instructor requirements.
- **Part E/F missing:** the feature contract files and executed software evidence are not created by this matrix; they must be produced before and during implementation respectively.
