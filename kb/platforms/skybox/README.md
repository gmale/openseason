# Skybox: quick reference

**Purpose:** Everything a session needs to start working against the Skybox API, with pointers to the detail files.
**Sources:** [API docs (Swagger UI)](https://skybox.vividseats.com/api-docs/), [OpenAPI spec](https://skybox.vividseats.com/services/openapi.json), [Getting Started with Skybox APIs](https://skybox.zendesk.com/hc/en-us/articles/6769735238043-Getting-Started-with-Skybox-APIs), [SkyBox API Rate Limits](https://skybox.zendesk.com/hc/en-us/articles/5999881334427-SkyBox-API-Rate-Limits), [API terms](https://skybox.vividseats.com/api-terms)
**Last verified:** 2026-10-31

## What Skybox is

A free point-of-sale system run by Vivid Seats. Sellers keep inventory in Skybox; Skybox distributes listings to Vivid Seats and its integrated marketplaces and records sales. openseason's first platform adapter targets Skybox.

## Files in this folder

| File | Read it when |
|---|---|
| [`api-terms.md`](api-terms.md) | **Before designing anything** that stores, combines, or analyzes Skybox data |
| [`listing-flow.md`](listing-flow.md) | Implementing list, price, delist, or sale detection |
| [`webhooks.md`](webhooks.md) | Implementing push notifications or reconciliation |
| [`onboarding.md`](onboarding.md) | Setting up a seller account or getting support |

Vivid Seats marketplace rules (in-hand dates, masking, event-day behavior) are in [`../vivid-seats/marketplace-rules.md`](../vivid-seats/marketplace-rules.md).

## API at a glance

| | |
|---|---|
| **Base URL** | `https://skybox.vividseats.com/services` |
| **Spec** | OpenAPI 3, served at `/services/openapi.json`: about 170 paths and 180 schemas |
| **Format** | JSON in and out |
| **Auth** | Three headers on every request (below) |
| **Sandbox** | None public. The spec lists a "Stage" server on an internal Vivid Seats corporate domain. Test with real but harmless listings: `broadcast: false`, far-future events, or a single low-stakes game |
| **Paging** | List endpoints take `limit`, `pageNumber`, `sortDir`, `sortedBy`; responses are `{ rows, rowCount, totals }` |
| **Errors** | `ApiErrorResponse` schema; shape to be confirmed on first real error |

The spec is public developer documentation. To use it in a session, download it to a git-ignored path rather than committing it:

```sh
mkdir -p .cache && curl -sf https://skybox.vividseats.com/services/openapi.json -o .cache/skybox-openapi.json
```

## Authentication

| Header | What it identifies | How to get it |
|---|---|---|
| `X-Application-Token` | The application (openseason, as registered by the user) | Submit the [application request form](https://skybox.vividseats.com/application-sign-up). Review takes about 3 business days |
| `X-Api-Token` | The Skybox account's API access | After the application is approved: in Skybox, open the menu next to your name → **External Accounts** → **API Invitation**, enter an email and a description. The emailed download link works **once**; lose it and you must request a new token |
| `X-Account` | The Skybox account ID | Shown in Skybox; the spec also has `GET /account` |

All three are secrets. They come from environment variables or a git-ignored local config, never from the repository.

## Rate limits

Skybox enforces limits per endpoint pattern. The values below are from the official rate-limit article (calls per interval). Anything not listed falls under the default.

| Endpoint pattern | Limit |
|---|---|
| Default (anything not listed) | 50 calls / 1 s |
| `GET /inventory` with **no** event, inventory, POS, or venue ID filter | **1 call / 10 s** |
| `GET /inventory?...eventId=` (or `inventoryId`, `exchangePosId`, `venueId`) | 1000 calls / 1 s |
| `GET /inventory?...includeTickets=true` | 20 calls / 1 s |
| `GET /inventory?...purchaseId=` | 10 calls / 10 s |
| `GET /inventory/{id}` and `/price-history` | 1000 calls / 1 s |
| `PUT /inventory/...` | 50 calls / 1 s |
| `GET /inventory/export` | 1 call / 5 s |
| `GET /inventory/sold` with an invoice date range | **1 call / 30 s** |
| `GET /reports/...` | 1 call / 1 s |
| `POST /holds` | 10 calls / 1 s |

The API terms add overall caps: 600 requests per minute per IP and 2,500 per minute per token.

**Practical rule:** always filter inventory queries by event or inventory ID, and never poll `/inventory/sold` faster than every 30 seconds.

## Endpoint groups openseason needs

| Need | Endpoints |
|---|---|
| Find events | `GET /events` (filters: `event`, `venue`, `performerId`, `eventDateFrom`/`To`, `onlyMyEvents`), `GET /event-catalog/search`, `GET /events/{id}` |
| Create inventory | `POST /purchases` (inventory is created as lines on a purchase; there is no `POST /inventory`) |
| Vendor for purchases | `GET /vendors`, `GET /vendors/default`, `POST /vendors` |
| Read inventory | `GET /inventory` (always with `eventId` or `inventoryId`), `GET /inventory/{id}` |
| Change price or listing state | `PUT /inventory` (bulk: `id`, `listPrice`, `broadcast`), `PUT /inventory/{id}`, `PUT /inventory/bulk-update` |
| Remove inventory | `POST /inventory/actions/cancel` |
| Detect changes | `GET /inventory/delta` (`startTime`, `endTime`), `GET /events/{event_id}/deltas` |
| Detect sales | `GET /invoices` (`createdDateFrom`/`To`, `invoiceStatus`, `fulfillmentStatus`), `GET /invoices/{id}`, `GET /inventory/sold` |
| Push notifications | `/webhooks`; see [`webhooks.md`](webhooks.md) |
| Enumerations | `GET /enumerations/{enumtype}` |

## Key enums (from the spec)

| Enum | Values |
|---|---|
| `stockType` | `HARD`, `ELECTRONIC`, `FLASH`, `PAPERLESS`, `PAPERLESS_CARD`, `MOBILE_TRANSFER`, `MOBILE_SCREENCAP` |
| `seatType` | `CONSECUTIVE`, `ALTERNATING`, `GA`, `PIGGYBACK` |
| `splitType` | `DEFAULT`, `ANY`, `CUSTOM` (with `customSplit`), `NEVERLEAVEONE` |
| Inventory `status` | `AVAILABLE`, `ON_HOLD`, `DEPLETED` |
| Ticket status | `AVAILABLE`, `SOLD`, `ERROR`, `CANCELLED` |
| `invDeliveryMethod` | `ETICKET_DELIVERY`, `AXS`, `SHIPPING`, `MOBILE_DELIVERY`, `TRANSFER`, `WILL_CALL`, `SPECIAL_DELIVERY`, `LOCAL_PICKUP`, `BARCODE`, `TRANSFER_URL`, `TEXT_TRANSFER`, `PAPERLESS_GIFT_CARD`, `PAPERLESS_WALK_IN`, `ACCOUNT_SURRENDER` |
| Invoice `fulfillmentStatus` | `PENDING`, `COMPLETE` |
| Invoice `status` | `COMPLETED`, `UNFULFILLED_ORDER`, `MUTUAL_CANCELLATION`, `ORDER_VOID_CANCELLED`, `EVENT_CANCELLED`, `EVENT_RESCHEDULED`, `DELIVERY_FAILURE`, `TICKETS_ON_HOLD`, and others (see spec) |

## Open questions

Each needs an answer from Skybox support or a careful first test before code depends on it.

1. **Does `broadcast` control marketplace visibility?** Likely `true` = listed on integrated exchanges, `false` = held in Skybox only. Confirm, and confirm how long propagation takes.
2. **Is `listPrice` in the bulk price update absolute or a delta?** The spec's description reads like a delta ("amount to increase or decrease"), while the field name suggests absolute. Test on one listing.
3. **Is `listPrice` per ticket?** Almost certainly, but confirm.
4. **Does `PUT /inventory/{id}` replace or patch?** If it replaces, sending a partial object could wipe fields. Prefer the bulk price endpoint for price and broadcast changes until confirmed.
5. **What counts as a sale signal?** Invoice creation, a ticket moving to `SOLD`, or both. Confirm the sequence when a marketplace sale comes in.
6. **Is there a usable test environment?** We asked during onboarding and got no answer.
7. **What may openseason keep locally?** See [`api-terms.md`](api-terms.md#open-questions-to-put-to-skybox).
