# F-03 — Thai delivery address, map pin, shipping quote and confirmation

**Status:** PROPOSED, not yet co-approved or tested. **Traceability:** FR-05/06/07/08; BR-09/16/17/18/22/24/26; NFR-07/09/10; DEC-003..011, 013, 029..031, MAP-COST-001. **Dependencies:** F-01/02, provinces and approved Thailand mainland/islands polygon. **F-03 implementation BLOCKED** until polygon source/license/version/checksum and islands/border fixture approved. [Shared contract](README.md).

## Vertical workflow / UI states

Cart → enter recipient, mobile, addressLine, subdistrict, district, province dropdown (English, from DB), postcode → select mandatory pin on MapLibre GL JS/OpenFreeMap → request server quote → review **complete current address + pin + subtotal + shipping + total** → explicitly confirm selected address/pin → submit via F-04. On change to any recipient/contact/address/province/postcode/pin or cart field, immediately invalidate earlier quote/confirmation; repeat review. No automated address-to-pin verification or manual coordinate guessing. On map outage with no previously valid pin, preserve cart/form, block checkout, offer Retry; with previously valid pin, display saved pin and require explicit reconfirmation after backend location validation. Never change a quote due to display rounding.

## API / request/response proposal

- `GET /api/provinces` (authenticated): `200 {"items":[{"id":1,"nameEn":"Bangkok"}, ...]}`; exactly 77 unique English province names read from `provinces` DB table populated via data-only SQL seed. Return `401` unauthenticated, `503` database failure.
- `POST /api/shipping/quote` Customer-only: request `{ "recipientName":"Example", "phone":"0812345678", "addressLine":"126 Pracha Uthit", "subdistrict":"Bang Mot", "district":"Thung Khru", "provinceId":1, "postalCode":"10140", "pin":{"latitude":13.65,"longitude":100.49}, "items":[{"optionId":"uuid","quantity":1}], "destinationRevision":"uuid" }`. Response `200 {"data":{"distanceKm":12.34,"subtotalSatang":25000,"shippingFeeSatang":4000,"totalSatang":29000,"destinationRevision":"uuid","quoteToken":"signed-opaque","expiresAt":"RFC3339"}}`. Auth/role: Customer only, server session; quote does not reserve stock. A proposed MAC-signed token binds authenticated Customer ID, canonical entire address/pin/cart, destination revision and expiry (proposed 10 min); never trust client revision alone. F-04 checks token and confirmed revision, independently reprices from persistent catalog and recomputes shipping and stock. Expired/changed token returns `409 QUOTE_STALE` without order/reservation; `422 VALIDATION_ERROR` invalid address, nonexistent province or foreign/ocean pin; `503` DB/quote failure. Additional errors follow shared contract.
- **Proposed field limits:** trim Unicode recipient `1..120`, addressLine `1..250`, district/subdistrict `1..120`; mobile ASCII `^0[0-9]{9}$`, postal code ASCII `^[0-9]{5}$` (leading zero retained). Province must be a valid DB ID; pin must have finite numeric WGS84 latitude [-90,90], longitude [-180,180] and be within licensed polygon that includes Thai islands. No district/province/postcode consistency validation, no automatic matching with pin. Boundaries/format choices need peer review.

## Database persistence / fee policy

Prisma-owned `Province(id, nameEn UNIQUE, regionEn)`, existing data-only `db/seeds/provinces.sql`; F-04 Order snapshots all entered structured fields, province ID **and English name**, phone/postcode as strings, pin coordinates, unrounded backend distance and authoritative fee. Origin is approximate simulated campus-center KMUTT Bangmod `(13.65178,100.49384)` from GP-01. Proposed great-circle Haversine with mean Earth radius 6371.0088 km. Use unrounded `d` for tariff: `0 <= d <=50 km` → 4000 satang, `50<d<=300` → 8000 satang, `d>300` → 15000 satang, **per Order**, not per item; display 2 decimal places only. Never derive shipping fee from client's `distanceKm` or `shippingFeeSatang`. Polygon geometry selection and checksum/license are NOT yet known. Quote should be refreshed on cart change and expiration.

## Acceptance Criteria + Software Test Specifications (all planned, none executed)

| AC / TEST | Preconditions / exact inputs | Expected result |
|---|---|---|
| AC-F03-01 / TEST-F03-01 | Fresh 77-province DB; quote blank name, whitespace address, phone `1234`, postcode `1234`, nonexistent province ID; then valid fields | Each invalid 422 field errors and zero Order/stock; valid fields accepted, chosen English province stored in quote |
| AC-F03-02 / TEST-F03-02 | Approved mainland+island geometry; pin Bangkok, Phuket/Samui, a foreign city, sea, NaN and out-of-range coordinate | Thai mainland and islands valid; foreign/sea/invalid values 422; map attribution rendered, selected coordinates preserved |
| AC-F03-03 / TEST-F03-03 | Fee function given `d=0, 50, 50.04, 300, 300.001` km and case where displayed rounding crosses boundary | Fees `4000,4000,8000,8000,15000` satang; rounding display does not change band; subtotal+shipping=total |
| AC-F03-04 / TEST-F03-04 | Confirm quote then change each of recipient, phone, addressLine, district, subdistrict, province, postcode, pin, cart; attempt old token/revision | Submit disabled in UI, backend `409 QUOTE_STALE`, **zero** Order/reservation; new quote + explicit confirmation needed |
| AC-F03-05 / TEST-F03-05 | Block map tiles with no pin; repeat with previously selected valid pin and valid backend | No pin: preserve cart/address and Retry, no submission; valid old pin: explicit reconfirmation and backend validation required, no guessed pin |

## Feature Definition of Done

Peer-approved polygon with mainland, islands and provenance/license/checksum; agreed input limits, quote claims/expiration, UI review and backend validation; all STS pass on real API/UI, including 50/300 boundary tests and map outage; actual run evidence attached to Part F. **Currently only specification draft; polygon and tests open.**
