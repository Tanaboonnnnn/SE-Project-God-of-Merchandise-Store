# F-02 — Seeded catalog, variants and available stock

**Status:** PROPOSED, unapproved/unimplemented. **Traceability:** FR-03/04; BR-04/05/06; NFR-03/08/09; DEC-014/015/020/021/022. **Dependencies:** product/option/inventory migration and seed; F-01 proposed auth policy. [Shared contract](README.md).

## Workflow and UI contract

Catalog loading → six product cards → option selector (T-shirt size S/M/L/XL; all others one option) → quantity/cart → checkout. Display DB-derived price and currently available stock; handle loading/empty/DB failure and stale cart. Catalog editing, stock reset and restocking are excluded in S1; Add to Cart never reserves stock. Keyboard choice/quantity selection required.

## API requests, responses and errors (candidate)

`GET /api/products` (session) → `200` `{ "items": [{ "id":"uuid", "code":"SHIRT_A", "name":"T-shirt design A", "unitPriceSatang":25000, "options":[{"id":"uuid", "code":"SHIRT_A_S", "size":"S", "available":10}] }] }`. Product list includes all six products, and sizes are separate sellable-option IDs. No client-supplied prices/stock accepted at checkout. `401 UNAUTHENTICATED` for unauthenticated under proposed catalog policy; `503 DEPENDENCY_UNAVAILABLE` on failed DB read. A cart row is `{ "optionId":"uuid", "quantity":1 }` (client only; backend F-04 validates all raw lines).

## Database schema and seeded fixtures

`Product(id UUID, code UNIQUE, name, priceSatang>=0, active)`; `SellableOption(id UUID, productId FK, code UNIQUE, size nullable)`; `Inventory(optionId PK/FK, available>=0, reserved>=0, sold>=0)`. Two shirts × S/M/L/XL = eight options, four single-option items. Shirt A all sizes 250 THB, Shirt B all sizes 290 THB, bag 180, notebook 70, pen 25, bottle 220. Each T-shirt-size option 10 Available; each other option 20: **6 products, 12 options, 160 Available, zero Reserved and Sold**. All product names/SKUs are simulated/unlicensed. Seed in disposable DB only; repeat operation must not rewrite live inventory.

## Acceptance criteria and STS (not executed)

| AC and TEST | Preconditions / test inputs | Observable expected result |
|---|---|---|
| AC-F02-01 / TEST-F02-01 | Fresh seeded DB; load catalog through API/UI; query persisted seed | Exactly six product records, correct six prices 250/290/180/70/25/220 THB and server-derived values |
| AC-F02-02 / TEST-F02-02 | Inspect options/stock and choose Shirt A S then M, single-option bag | Eight independent shirt options @10, four single options @20; 12 option IDs, total Available 160; option-specific size displays |
| AC-F02-03 / TEST-F02-03 (additional) | In isolated test DB successfully reserve one A/M via F-04; reload Catalog and attempt checkout with stale UI availability | A/M Available becomes 9; A/L remains 10; stale UI does not bypass server stock checks |

## Feature Definition of Done

Co-reviewed variants/schema/seed and API/UI contract, reproducible migration and seed, persistence/API/UI/keyboard tests and stale-inventory test pass with logged evidence; no S1 catalog write endpoints. **Current state: documentation only.**
