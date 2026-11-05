# Korzy REST API

The REST API at `https://api.korzy.co` answers the same parcel workflow as the MCP tools. Send an API key with the `api:read` scope in the `Authorization: Bearer <key>` header; create keys at https://app.korzy.co/account. Never put a key in a URL. URL-encode query values. The full reference is at https://korzy.co/docs/reference/.

## Parcels

| Step | Request | Credits |
| --- | --- | --- |
| Find by address | `GET /api/search?q=714 Mordecai Towne Pl, Raleigh, NC` | 1; 2 with `include_owner=true` |
| Find by point | `GET /api/query/point?longitude=-78.6270&latitude=35.7999&limit=10` | 1 per 10 records requested; doubled with `include_owner=true` |
| Parcel record | `GET /api/parcel-record?korzy_id=37183-676db8ae68362b7a3685` | 1; 2 with `include_owner=true` |

- **Address search** (`q`, 3–160 characters) returns `results`, each with `korzy_id` where known, `address`, `city`, `postcode`, `county`, `longitude`, `latitude`, `match_type` (`exact`, `partial` or `approximate`) and the parcel's published fields. An exact match returns only the exact matches; otherwise up to 15 ranked candidates. Include the state for the most precise results.
- **Point** returns every parcel whose boundary covers the point, including overlapping condo units. `limit` is 1–100 (default 20) and `offset` stops at 1,000.
- **Parcel record** takes the `korzy_id` from a search or point result.
- Add `include_owner=true` to any of these for owner names and mailing addresses where the source publishes them. Leave it off unless ownership is needed.

## Point context

These routes take `lon` and `lat`.

| Request | Returns | Credits |
| --- | --- | --- |
| `GET /api/utilities/context?lon=-78.6270&lat=35.7999` | Water and sewer service areas at the point and nearby treatment plants | 1 |
| `GET /api/environment/context?lon=-78.6270&lat=35.7999&radius_km=10&limit=5` | Flood zones, wetlands, protected areas and cleanup sites; `radius_km` up to 10 | 1 per 10 records requested |
| `GET /api/logistics/context?lon=-78.6270&lat=35.7999&radius_km=25&limit=5` | Roads, rail, ports, airports and warehouses; `radius_km` up to 25; optional `layers` | 1 per 10 records requested |
| `GET /api/datacenters/context?lon=-77.4874&lat=39.0438&radius_km=10&limit=5` | Data center projects near the point; `radius_km` up to 10 | 1 per 10 records requested |

## Records by ID

| Request | Credits |
| --- | --- |
| `GET /api/datacenters/projects/{project_id}` (URL-encode the ID) | 1 |
| `GET /api/utilities/{layer}/record?feature_id=...` | 1 |
| `GET /api/power/plants/{plant_id}` | 1 |
| `GET /api/energy/plants/{plant_id}` | 1 per 10 records requested |
| `GET /api/energy/regions/{region}` | 1 per 10 records requested |
| `GET /api/energy/coal-mines/{mine_id}/shipments` | 1 per 10 records requested |
| `GET /api/grid/transfers?interface=...&date=YYYY-MM-DD` | 1 |

Free discovery: `GET /api/query/catalog` (coverage, dates, layer keys) and `GET /api/summary` (parcel counts by state).

When a page returns a `run_id`, pass it unchanged on later pages. Searching a whole area or listing many records isn't available through the API (`403 range_queries_unavailable`), and whole map layers are served to the Korzy map only (`403 map_only_route`): query by address, point or record ID.

## Credits

API and MCP requests share the account's credits. Catalogs and summaries are free; a record or address search costs 1 credit; record pages cost 1 credit per 10 records requested (the `limit`, or the route's default); `include_owner=true` doubles the cost. Requests spend the plan's monthly credits first, then extra credits bought on the account page. Monthly credits reset at the start of each calendar month (UTC), and unused monthly credits carry over once. When credits run out, requests return `429`; nothing is charged automatically.

## Errors

| Status | Action |
| --- | --- |
| `401` | Check the key; it may be revoked or expired |
| `402` | `subscription_required`: the account needs a plan |
| `403` | The key lacks the scope, or the route isn't available to the API |
| `404` | Check the record ID; don't guess a replacement |
| `409` | The data changed between pages; start again from the first page |
| `422` | Correct the parameter the body names |
| `429` | Wait for `Retry-After`; monthly exhaustion also returns `resets_at` |
| `503` | Temporarily unavailable; retry after `Retry-After` with backoff |
