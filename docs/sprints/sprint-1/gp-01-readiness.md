# GP-01 — Engineering readiness audit and proposed decisions

**Issue:** #3 (GP-01)
**Date:** 2026-11-02
**Status:** Local working draft; peer approval required. Not authorization to implement F-01..F-06.

## 1. Repository and local-machine evidence (verified)

- Source: `https://github.com/Tanaboonnnnn/SE-Project-God-of-Merchandise-Store.git`.
- Local working copy: `C:\Users\mos28\Desktop\project\SE-Project-God-of-Merchandise-Store`.
- Fetched `origin/sprint/1-staging` and branched locally as `docs/issue-3-gp01-readiness` from merge commit `32e489f` (PR #1). The base checkout was clean.
- Root contains `.gitignore`, `README.md` and `docs/`; no app manifests, executable source, migrations, tests or `AGENTS.md` at the inspected commit. Current docs remain design proposals.
- Windows Git 2.48.1, Node v24.14.0, npm 11.9.0 and Docker CLI 29.2.1 are present. `psql` CLI was not found in the command-path check. Docker Engine is **not running** (`docker info` could not connect to Docker Desktop Linux Engine); PostgreSQL service and build/test commands are **not verified**.
- GitHub issue #3 is assigned to `Chxtamos`; team project `CPE334 Campus Store - Group Project` has been moved from `Specified` to `Started`.

## 2. Suggested stack — NOT YET APPROVED

| Layer | Proposal | Reason / remaining check |
|---|---|---|
| Web | React + Vite + TypeScript | Fits the existing SDS proposal; pin UI via MapLibre GL JS |
| API | Node.js + Express + TypeScript | One codebase language; explicit server-side auth/ownership validation |
| DB | PostgreSQL + Prisma migrations | Relational transactions, constraints and controlled seeded fixtures |
| Tests | Vitest + Supertest; targeted Playwright E2E | Exercise request validation, permissions, DB races and user flows |
| Package management | npm workspaces, lockfile committed | npm verified locally; versions must be pinned when Part E is reviewed |
| Local database | PostgreSQL via Docker Compose (proposal) | Verify Docker daemon, images and reproducible migrations before deciding |

**Proposed database transaction policy:** Use guarded atomic inventory updates (`available >= q`) and `PENDING` guarded state updates, with all order/items/idempotency/stock changes in single PostgreSQL transactions. Set/test explicit isolation level and deadlock retry policy in GP-03; do not assume Prisma defaults solve concurrency. Unique `(customer_id, idempotency_key)` and nonnegative DB constraints required. Confirm exact approach with teammate in Part E.

## 3. Shipping origin: auditable candidate, awaiting explicit team confirmation

- Official campus identity/address: **KMUTT Bangmod**, 126 Pracha Uthit Rd., Bang Mod, Thung Khru, Bangkok 10140. Source: https://www.kmutt.ac.th/en/about-kmutt/contact-us/ (checked 2026-11-02).
- **Candidate approximate campus-center pin**: latitude `13.65178`, longitude `100.49384` (WGS84 decimal degrees), from OpenStreetMap-based Mapcarta campus feature, OSM way `420312611`: https://mapcarta.com/W420312611 (checked 2026-11-02).
- This is an approximate point representing the OSM campus feature, **not an official university dispatch coordinate**, surveyed parcel centroid or validated vehicle gate. It is suitable only as a simulated fixed-origin candidate under DEC-010; obtain team confirmation before locking configuration.
- Once approved, store fixed numbers in **server-side configuration** (`SHIPPING_ORIGIN_LAT`, `SHIPPING_ORIGIN_LON`) and include a fixture for precise 50 km/300 km boundary testing using unrounded great-circle distance. Client may display origin but cannot supply the authoritative one.

## 4. Destination pin, address, map outages (proposed)

- Pin eligibility: finite latitude/longitude, bounds `-90..90` and `-180..180`, then verify that a selected destination is within Thailand using a documented mainland/island polygon GeoJSON reference. Do **not** use a rough bounding rectangle as the sole Thailand test; record polygon dataset license/version before shipping. Backend applies identical validation at quote and order submission.
- Province selector: pin a documented, versioned 77-province Thai reference dataset; validate province against the list on backend, not arbitrary user text. Verify its license/source and postal-code consistency policy during GP-03.
- DEC-030 deliberately excludes automated address/pin matching and paid geocoding. Confirm visible address + pin before submit; invalidate confirmation and quote revision on either edit.
- Map stack: MapLibre GL JS with OpenFreeMap public instance as frozen. Show attribution required by tile data/provider; record actual style URL/license and test offline tiles/retry. No map tiles + no valid pin blocks submit and keeps form/cart; prior valid pin can be explicitly reconfirmed after outage. No paid key or payment card required for Sprint 1.

## 5. Environment, seeded fixtures, and Git process (proposed)

- Commit **only** `.env.example` with placeholders. Real `.env`, secrets, passwords, local databases, screenshots containing personal details and API keys remain untracked/private. Current `.gitignore` already ignores `.env*` except examples and common generated artifacts.
- Suggested variables: `DATABASE_URL`, `SESSION_SECRET`, `PORT`, `WEB_ORIGIN`, `SHIPPING_ORIGIN_LAT`, `SHIPPING_ORIGIN_LON`, `MAP_STYLE_URL`. No real credential values in docs or Git.
- Seed deterministically: 2 Customers, 1 Operator, 6 products / 12 options, initially 160 Available; securely hashed seed passwords provided privately, no destructive inventory reset once orders exist.
- Proposed local commands **after scaffolding**: `npm ci`, `docker compose up -d db`, `npm run db:migrate`, `npm run db:seed`, `npm run test`, `npm run build`. These commands **do not yet exist or pass**; Part E/scaffold must define exact package scripts and smoke-test them.
- Branch protocol: always `git fetch origin --prune`, inspect `git status`, verify the `origin/sprint/1-staging` base is current, create an issue-specific local branch, edit and test locally, inspect `git diff --check` and `git status`, commit locally, then push only the issue branch and open PR targeting `sprint/1-staging`. Teammate reviews before merge; `main` only via reviewed release PR. Never force-push or commit directly to `main`/staging.

## 6. Decision and evidence gates still open

1. Peer approval of stack, exact tool versions, package strategy and confirmed real machine/database execution.
2. Confirm the chosen approximate KMUTT shipping-origin coordinate with teammate and record any alternative source.
3. Choose and license-check Thailand polygon, province reference data and precise API validation rules.
4. Confirm exact cookie-session/CSRF policy, secret delivery and local fixtures; inspect deployment assumptions for Lab 5.
5. Define PostgreSQL isolation/timeout/error semantics, idempotency key canonicalization, quote confirmation token and fault-injection tests in GP-03 Part E.
6. Agree actual contributor availability and updated timeline. Run real install/migrate/test commands after code scaffolding, not before.

**DoD for GP-01:** Recorded source evidence + technical choices signed off by both contributors, updated docs with actual decisions, reviewed PR to staging. The present file is a **reviewable local proposal**, not completed GP-01 or working application evidence.
