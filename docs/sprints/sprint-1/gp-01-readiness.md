# GP-01 — Engineering readiness audit and proposed decisions

**Issue:** #3 (GP-01)
**Date:** 2026-11-02
**Status:** Local working draft; peer approval required. Not authorization to implement F-01..F-06.

## 1. Repository and local-machine evidence (verified)

- Source: `https://github.com/Tanaboonnnnn/SE-Project-God-of-Merchandise-Store.git`.
- Local working copy: `C:\Users\mos28\Desktop\project\SE-Project-God-of-Merchandise-Store`.
- Fetched `origin/sprint/1-staging` and branched locally as `docs/issue-3-gp01-readiness` from merge commit `32e489f` (PR #1). The base checkout was clean.
- Root contains `.gitignore`, `README.md` and `docs/`; no app manifests, executable source, migrations, tests or `AGENTS.md` at the inspected commit. Current docs remain design proposals.
- Windows Git 2.48.1, Node v24.14.0 and npm 11.9.0 are present. `psql` CLI was not found in the command-path check; PostgreSQL service and build/test commands are **not verified**.
- GitHub issue #3 is assigned to `Chxtamos`; team project `CPE334 Campus Store - Group Project` has been moved from `Specified` to `Started`.

## 2. Updated team direction (2026-11-02)

| Layer | Proposal | Reason / remaining check |
|---|---|---|
| Web | React + Vite + TypeScript | Fits the existing SDS proposal; pin UI via MapLibre GL JS |
| API | Node.js + Express + TypeScript | One codebase language; explicit server-side auth/ownership validation |
| DB | PostgreSQL + Prisma migrations | Relational transactions, constraints and controlled seeded fixtures |
| Tests | Vitest + Supertest; targeted Playwright E2E | Exercise request validation, permissions, DB races and user flows |
| Package management | npm workspaces, lockfile committed | npm verified locally; versions must be pinned when Part E is reviewed |
| Local database | PostgreSQL installed directly on Windows | Use native PostgreSQL for now; verify installation and local migrations before building. |

**Database decision (requested by Chxtamos; awaiting Tanaboonnnnn peer approval):** Store the full list of 77 provinces in a PostgreSQL `provinces` reference table; the user-facing province names are English. Use Prisma migrations as the **sole owner** of the `provinces` table schema, then a version-controlled **data-only** SQL seed and a backend read endpoint; do not depend on live external APIs during checkout. The chosen government source is the Ministry of Commerce provincial directory (76 named entries in four regions) plus Bangkok, which is independently confirmed as the 77th province in the Digital Government Development Agency list. Source: https://mpdc.moc.go.th/en/province and https://data.go.th/en/dataset/proviceandregionthailand (checked 2026-11-02). SQL seed: `db/seeds/provinces.sql` (data only; not executed against a database yet).

**Proposed minimum transaction decision for peer approval (GP-01):** PostgreSQL `READ COMMITTED` isolation with guarded atomic `UPDATE ... WHERE available >= quantity` (verify exactly one affected row) for each option, deterministic ascending option-ID update order for multi-line orders, and one database transaction for order, lines, idempotency record and stock reservations. Guard each `PENDING` status transition with a compare-and-set condition; inventory movement and status/reason share the same transaction. Failure/insufficient stock rolls back **all** writes. Enforce DB unique `(customer_id, idempotency_key)` and nonnegative inventory constraints. For transient deadlock (`40P01`) or serialization (`40001`) failures, retry the **entire** transaction with bounded attempts/backoff and unchanged idempotency key; never retry a partially committed operation or blindly retry constraint/business errors. Exact retry limit/timeouts, fingerprint/replay behavior and actual `READ COMMITTED` concurrency safety must be verified with **real PostgreSQL tests in GP-03**, including last-unit race, multi-line rollback, double transitions and injected write failure. This is a reviewable technical choice, **not** a claim of teammate agreement or passed tests.

## 3. Shipping origin: KMUTT Bangmod selected by team

- Official campus identity/address: **KMUTT Bangmod**, 126 Pracha Uthit Rd., Bang Mod, Thung Khru, Bangkok 10140. Source: https://www.kmutt.ac.th/en/about-kmutt/contact-us/ (checked 2026-11-02).
- **Selected simulated campus-center pin**: latitude `13.65178`, longitude `100.49384` (WGS84 decimal degrees), from OpenStreetMap-based Mapcarta campus feature, OSM way `420312611`: https://mapcarta.com/W420312611 (checked 2026-11-02).
- This is an approximate point representing the OSM campus feature, **not an official university dispatch coordinate**, surveyed parcel centroid or validated vehicle gate. The team selected KMUTT Bangmod as the fixed simulated origin; these approximate coordinates are the currently documented implementation values, subject to correction if a more authoritative campus pin is supplied.
- Store fixed numbers in **server-side configuration** (`SHIPPING_ORIGIN_LAT`, `SHIPPING_ORIGIN_LON`) and include a fixture for precise 50 km/300 km boundary testing using unrounded great-circle distance. Client may display origin but cannot supply the authoritative one.

## 4. Destination pin, address, map outages (proposed)

- **F-03 blocker ? Thailand polygon dataset not selected:** Before F-03 starts, both contributors must choose and archive a reproducible source URL, dataset release/version or download checksum, license/attribution terms and polygon geometry including coastal islands (e.g. Phuket/Samui), with test points for land, islands, sea and borders. Do not claim that a geometry or license has already been verified. This gate is **open**.
- Pin eligibility: finite latitude/longitude, bounds `-90..90` and `-180..180`, then verify that a selected destination is within Thailand using a documented mainland/island polygon GeoJSON reference. Do **not** use a rough bounding rectangle as the sole Thailand test; record polygon dataset license/version before shipping. Backend applies identical validation at quote and order submission.
- Seed verification (not yet executable): Prisma migration creates the table first; run `db/seeds/provinces.sql` twice, assert `SELECT COUNT(*) = 77` and `COUNT(DISTINCT name_en) = 77`, verify no schema drift via `prisma migrate status` / migration diff. Database is not installed locally, so no runtime claim.
- Province selector: read the **English names** from a **PostgreSQL reference table**, populated by the versioned 77-row seed in `db/seeds/provinces.sql`. Backend validates selected province IDs against this table. The source directory is public government information; confirm reuse/attribution requirements and postal-code consistency in GP-03.
- DEC-030 deliberately excludes automated address/pin matching and paid geocoding. Confirm visible address + pin before submit; invalidate confirmation and quote revision on either edit.
- Map stack: MapLibre GL JS with OpenFreeMap public instance as frozen. Show attribution required by tile data/provider; record actual style URL/license and test offline tiles/retry. No map tiles + no valid pin blocks submit and keeps form/cart; prior valid pin can be explicitly reconfirmed after outage. No paid key or payment card required for Sprint 1.

## 5. Environment, seeded fixtures, and Git process (proposed)

- Commit **only** `.env.example` with placeholders. Real `.env`, secrets, passwords, local databases, screenshots containing personal details and API keys remain untracked/private. Current `.gitignore` already ignores `.env*` except examples and common generated artifacts.
- Suggested variables: `DATABASE_URL`, `SESSION_SECRET`, `PORT`, `WEB_ORIGIN`, `SHIPPING_ORIGIN_LAT`, `SHIPPING_ORIGIN_LON`, `MAP_STYLE_URL`. No real credential values in docs or Git.
- Seed deterministically: 2 Customers, 1 Operator, 6 products / 12 options, initially 160 Available; securely hashed seed passwords provided privately, no destructive inventory reset once orders exist.
- Proposed local commands **after scaffolding**: `npm ci`, start locally installed PostgreSQL service, `npm run db:migrate`, `npm run db:seed`, `npm run test`, `npm run build`. These commands **do not yet exist or pass**; Part E/scaffold must define exact package scripts and smoke-test them.
- Branch protocol: always `git fetch origin --prune`, inspect `git status`, verify the `origin/sprint/1-staging` base is current, create an issue-specific local branch, edit and test locally, inspect `git diff --check` and `git status`, commit locally, then push only the issue branch and open PR targeting `sprint/1-staging`. Teammate reviews before merge; `main` only via reviewed release PR. Never force-push or commit directly to `main`/staging.

## 6. Decision and evidence gates still open

**Review state:** Chxtamos selected native PostgreSQL, English provinces-in-DB, and KMUTT Bangmod approximate pin. Stack versions, isolation proposal above, actual DB startup, geographic polygon and both contributors' availability still need explicit teammate approval/evidence; they must not be labelled approved. Target implementation stack remains React/Vite/TypeScript + Express/TypeScript + PostgreSQL/Prisma, npm workspaces, Vitest/Supertest and targeted Playwright; pin major versions in lockfiles after scaffold and confirm via peer review.

1. Peer approval of the remaining stack, exact tool versions, package strategy and confirmed real machine/database execution (native PostgreSQL for now).
2. KMUTT Bangmod is the team-selected shipping origin. The approximate fixed coordinate is recorded; review its precision if an authoritative coordinate becomes available.
3. Government English province names and PostgreSQL storage are selected; verify source attribution, Thailand polygon and precise API validation rules.
4. Confirm exact cookie-session/CSRF policy, secret delivery and local fixtures; inspect deployment assumptions for Lab 5.
5. Define PostgreSQL isolation/timeout/error semantics, idempotency key canonicalization, quote confirmation token and fault-injection tests in GP-03 Part E.
6. **Capacity gate OPEN:** Neither `Chxtamos` nor `Tanaboonnnnn` has supplied confirmed available hours for 3?8 Nov. The 67?111 person-hour estimate is NOT a capacity commitment. Each member must record realistic daily hours and the pair must rebaseline scope/deadlines with the instructor if insufficient; see `docs/sprints/plan.md`. Install native PostgreSQL and run real migration/seed/tests after scaffolding, not before.

**DoD for GP-01:** Recorded source evidence + technical choices signed off by both contributors, updated docs with actual decisions, reviewed PR to staging. The present file is a **reviewable local proposal**, not completed GP-01 or working application evidence.
