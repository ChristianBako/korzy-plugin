# Korzy REST API

The REST API at `https://api.korzy.co/v1` answers the same parcel workflow as the MCP tools. Send an API key with the `api:read` scope in the `Authorization: Bearer <key>` header; create keys at https://app.korzy.co/account. Never put a key in a URL. Send JSON bodies (up to 64 KB) with `Content-Type: application/json`. The full reference is at https://korzy.co/docs/reference/.

## Routes

| Request | Does | Credits |
| --- | --- | --- |
| `POST /v1/lookup` | Parcels at an address or point, plus optional sections | 1, plus 1 per section (2 for `terrain`) |
| `POST /v1/enrich` | Selected fields for up to 25 addresses, points or parcel IDs | 1 per item; 2 with the owner group |
| `GET /v1/records/{id}` | One record by the `id` a lookup or enrich returned (URL-encode it) | 1 |
| `POST /v1/ask` | One question about one place, answered from Korzy data with citations | 10 per completed answer |
| `POST /v1/ask/stream` | The same answer as server-sent events | 10 per completed answer |
| `POST /v1/support` | Files a feature, data or bug ticket with Korzy | Free |
| `GET /v1/fields` | Sections, field groups, presets, record ID formats, prices and attribution | Free |

Every successful JSON response includes `request_id` and `credits` (`charged`, `refunded`). Errors return `{"detail": ..., "code": ...}`.

## Lookup

```json
{"address": "714 Mordecai Towne Pl, Raleigh, NC", "sections": ["utilities", "environment"]}
```

Give exactly one location: `address` (3–160 characters, exact matches only; otherwise `404 address_not_found`) or `longitude` and `latitude` (WGS84). The response has `location`, `parcels.records` (the parcels covering the point, up to 20, each with `id` and `korzy_id`) and one entry per requested section with `status` (`ok`, `unavailable` or `error`), `data` and `provenance`.

| Section | Returns |
| --- | --- |
| `utilities` | Water and sewer service areas at the point and treatment plants within 50 km |
| `environment` | Flood zones, wetlands, protected areas, critical habitat, air quality areas, brownfields and Superfund sites within 10 km, with mapped coverage |
| `logistics` | Roads, rail, ports, airports and warehouses within 25 km |
| `datacenters` | Data center projects within 10 km |
| `power` | Power plants within 50 km, with nameplate MW and primary fuel |
| `grid` | Published load capacity records and transmission lines near the point |
| `terrain` | Elevation and slope across the first parcel |

Sections that aren't `ok` are refunded. Lookups never return owner fields.

## Enrich

```json
{"items": [{"address": "714 Mordecai Towne Pl, Raleigh, NC"}, {"longitude": -78.6270, "latitude": 35.7999}, {"korzy_id": "37183-676db8ae68362b7a3685"}], "preset": "basic"}
```

Send `fields` (group or field names) or `preset` (`basic`, `standard`, `owner`; default `standard`), not both. Groups: `identity`, `address`, `location`, `land`, `building`, `value`, `sale` and `owner`. Each result has `index`, `status` (`ok` or `error`), `records` and its own `credits`; failed items are refunded. Owner names and mailing addresses come only from the `owner` group, which doubles each item's cost; request it only when ownership is needed.

## Records

`GET /v1/records/37183-676db8ae68362b7a3685` returns `id`, `type`, `record` and `provenance`. IDs round-trip exactly as returned: a parcel `korzy_id`, or `plant:`, `datacenter:`, `utility:`, `energy:`, `capacity:`, `property:` and `poi:` forms from lookup sections. `GET /v1/fields` lists the formats.

## Ask

```json
{"question": "Is this parcel in a mapped flood zone?", "address": "714 Mordecai Towne Pl, Raleigh, NC"}
```

`question` is 3–1000 characters, with `address` or `longitude` and `latitude`. `/v1/ask` returns `answer`, `citations` (`ref`, `id`, `field`, `source`, `as_of`), `model` and `usage`. `/v1/ask/stream` sends `start`, `step`, `delta` and `done` events, or `error`; a stream that ends without `done` is refunded. Answers never use owner fields.

## Support

```json
{"type": "data", "message": "Please add parcel coverage for Loving County, TX.", "context": {"request_id": "..."}}
```

`type` is `feature`, `data` or `bug`; `message` is up to 4000 characters; optional `context` takes `request_id`, `korzy_id`, `longitude`, `latitude` and `endpoint`. It creates a ticket and returns `201` with `ticket_id` and `status`. Send it only when the user asks.

## Credits

API and MCP requests share the account's credits. Requests spend the plan's monthly credits first, then extra credits bought on the account page. Monthly credits reset at the start of each calendar month (UTC), and unused monthly credits carry over once. Any failed request is refunded. When credits run out, requests return `429`; nothing is charged automatically.

## Errors

| Status | Action |
| --- | --- |
| `401` | Check the key; it may be revoked or expired |
| `402` | `subscription_required`: the account needs a plan |
| `403` | The key lacks `api:read`, or owner data isn't available to the organization (`owner_data_unavailable`) |
| `404` | `address_not_found` or `record_not_found`: check the address or ID; don't guess a replacement |
| `422` | `invalid_request` or `invalid_id`: correct the field the body names |
| `429` | Wait for `Retry-After`; monthly exhaustion also returns `resets_at`; `ask_rate_limited` and `support_rate_limited` are per-organization limits |
| `503` | Temporarily unavailable (`ask_capacity`, `ask_unavailable`); retry after `Retry-After` with backoff |
