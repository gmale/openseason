# Listing season tickets through Skybox

**Purpose:** The end-to-end flow openseason drives in Skybox for a season ticket holder, from setup to post-event cleanup, with what is confirmed and what still needs a first test.
**Sources:** [OpenAPI spec](https://skybox.vividseats.com/services/openapi.json) (operation descriptions and schemas), [rate limits](https://skybox.zendesk.com/hc/en-us/articles/5999881334427-SkyBox-API-Rate-Limits), [webhooks](https://skybox.vividseats.com/api-docs/webhooks/), [API terms](https://skybox.vividseats.com/api-terms), Skybox help center articles linked inline
**Last verified:** 2026-10-31

Status markers: **[spec]** confirmed in the official spec or docs; **[test]** plausible but must be confirmed by a first careful request.

## Mental model

In Skybox, inventory isn't created directly. You record a **purchase** from a **vendor**, and each purchase line carries an **inventory** record (event, section, row, seats, cost, stock type). That inventory gets a **list price** and a **broadcast** flag, and Skybox distributes broadcast inventory to its integrated marketplaces. A sale arrives as an **invoice** whose lines reference the sold tickets. [spec]

openseason never owns this data. It tells Skybox what to do and reads current state back when needed (see [`api-terms.md`](api-terms.md)).

## 0. Prerequisites (once per user)

1. A Skybox account approved and live on Vivid Seats ([`onboarding.md`](onboarding.md)).
2. An application token: request it with the [application form](https://skybox.vividseats.com/application-sign-up), describing openseason's real use. [spec]
3. An API token from **External Accounts → API Invitation** in Skybox, and the account ID. [spec]
4. **Every seat is listed through Skybox only.** Listing the same seats through any other channel (another consignment service, a direct marketplace account) at the same time creates double-sale risk that neither system can see.

## 1. Vendor (once)

`POST /purchases` requires a `vendorId`. [spec] Create one vendor representing where the season tickets came from (the team's box office) with `POST /vendors`, or use the account default from `GET /vendors/default`. Store only its ID in openseason's config.

## 2. Find the season's events

`GET /events` with `event` or `performerId`, `venue`, and an `eventDateFrom`/`eventDateTo` range covering the season. [spec] Match each home game to a Skybox `eventId`, and have the user confirm the matches before anything is created.

If a game doesn't exist in Skybox, a purchase line can carry `eventMapping` (`eventName`, `venueName`, `eventDate`) instead of `eventId`, which sends it to Skybox's mapping process. [spec] Provide one or the other, not both. Mapping is reviewed by hand and delays the listing going live; once a set of values has been mapped, identical later requests reuse it ([help center](https://skybox.zendesk.com/hc/en-us/articles/360057211791)). Treat it as an exception path.

**Check the StubHub link too.** An event without a StubHub link won't list on StubHub until one is added ([help center](https://skybox.zendesk.com/hc/en-us/articles/115005547623)). The API has `POST /events/{event-id}/stubhub-link-request` for this. [spec] openseason should report events that will only reach Vivid Seats.

## 3. Create inventory (once per season)

One `POST /purchases` per season, with one line per game. [spec] Required on each line, per the operation description: `quantity`, `section`, `row`, `cost`, `lowSeat`, `highSeat`, `stockType`, `seatType`, and `eventId` or `eventMapping`. The `Line` schema also requires `accountId`, `amount` (line total), and `lineItemType` (`INVENTORY`), and the `Inventory` schema additionally marks `splitType` as required.

Expected shape [test: confirm nesting with a single-line request first]:

```json
{
  "vendorId": 111,
  "externalRef": "openseason:2026-27",
  "lines": [
    {
      "accountId": 222,
      "lineItemType": "INVENTORY",
      "amount": 400.00,
      "quantity": 2,
      "inventory": {
        "eventId": 333,
        "quantity": 2,
        "section": "101",
        "row": "10",
        "lowSeat": 5,
        "highSeat": 6,
        "cost": 200.00,
        "stockType": "MOBILE_TRANSFER",
        "seatType": "CONSECUTIVE",
        "splitType": "NEVERLEAVEONE",
        "broadcast": false
      }
    }
  ]
}
```

Notes:
- **Create with `broadcast: false`.** Creating and listing are separate steps, so nothing goes live by accident. With broadcast off, a listing is down on every exchange ([help center](https://skybox.zendesk.com/hc/en-us/articles/207811963)).
- **Cost per game** comes from the user's own allocation of the season price. openseason doesn't guess it.
- **Stock type and delivery** for mobile-transfer season tickets are most likely `stockType: MOBILE_TRANSFER` with `invDeliveryMethod: TRANSFER`. [test]
- **Split type:** `DEFAULT` sells even quantities and never leaves a single ticket; Vivid Seats converts `DEFAULT` to never-leave-one for listings of 8 or more ([help center](https://skybox.zendesk.com/hc/en-us/articles/206510933)). StubHub does not support `CUSTOM` splits ([help center](https://skybox.zendesk.com/hc/en-us/articles/207021426)), so openseason should avoid `CUSTOM`. For a pair of seats, `DEFAULT` is the natural choice.
- **In-hand date** must respect the marketplace rule (no later than 7 days before the event without an exception). Mobile-transfer tickets that are already available can use today's date. See [`../vivid-seats/marketplace-rules.md`](../vivid-seats/marketplace-rules.md).
- `GET /purchases/{id}` returns the fully persisted state after creation. [spec]

## 4. List and price

`PUT /inventory` takes an array of `{ id, listPrice, broadcast }` for bulk price and listing changes. [spec] Use it both to list (`broadcast: true` with a price) and to reprice.

A listing with `broadcast: true` still stays off exchanges while it has a hold, an exchange-filter tag, or a $0 list price ([help center](https://skybox.zendesk.com/hc/en-us/articles/207811963)). Two consequences: openseason must never send a $0 price by accident (treat it as an error), and "listed" in openseason's status means broadcast on, price above zero, no hold, and no `-all` tag.

Before relying on it, confirm two things on a single listing [test]:
- whether `listPrice` here is the new absolute price or a change from the current one (the spec description is ambiguous), and
- that `listPrice` is per ticket.

`PUT /inventory/{id}` also updates inventory but takes a full `Inventory` object; until we know whether it replaces or patches, don't use it for price changes.

## 5. Detect sales

Options, in order of preference for an individual seller:

1. **Poll invoices.** `GET /invoices?createdDateFrom=<last checkpoint>` on a steady interval (every minute is plenty; the default limit is 50 calls per second). [spec] Fetch each new invoice and its lines to see which inventory sold.
2. **Webhooks.** `INVOICE` notifications as a faster trigger, never the only one. See [`webhooks.md`](webhooks.md).
3. **`GET /inventory/sold`** with an invoice date range: useful for reports, but rate-limited to one call per 30 seconds. [spec]

Skybox's integration is what pulls sold tickets off the other exchange; if removal fails, Skybox emails the seller ([help center](https://skybox.zendesk.com/hc/en-us/articles/207282156)). It also removes listings created by hand directly on an exchange ([help center](https://skybox.zendesk.com/hc/en-us/articles/201279985)), which is one more reason every seat goes through Skybox. openseason's role after a sale is to notify the user and to make sure nothing it controls re-lists those seats. [test: observe how quickly a sale propagates]

## 6. Fulfill

For mobile-transfer tickets, the seller transfers the tickets to the buyer through the team's ticketing app, following the marketplace's delivery instructions. Skybox tracks this on the invoice (`fulfillmentStatus` `PENDING` → `COMPLETE`) and has delivery endpoints (`POST /invoices/actions/deliver_tickets`, transfer URLs on inventory). [spec for the endpoints]

**Skybox already offers transfer automation.** Its Team Ticket Downloader ([setup article](https://skybox.zendesk.com/hc/en-us/articles/20200613854747), 2024) sweeps the account every few minutes for invoices that are pending, have a mobile-transfer or Flash stock type, and belong to the Vivid Seats or StubHub customer. It then performs the transfer, uploads proof to the marketplace, and marks the invoice complete. Two requirements matter:

- Section, row, and seat must match exactly across Skybox, the downloader, and the team ticketing account. openseason must create inventory with the exact values from the user's tickets.
- The user enters their Vivid broker token, exchange credentials, and team-account login into the downloader. That's a decision for the user, not something openseason does.

MVP: openseason tells the user exactly what to transfer and to whom, and works alongside the downloader if the user enables it. Building our own transfer automation is unnecessary while the downloader exists.

## 7. Before and after the event

- **Delist cutoff:** at a user-configured time before the event, set `broadcast: false` on anything unsold. Marketplaces may keep listings up past start time otherwise. [spec for the flag; marketplace behavior per Vivid rules]
- **After the event:** unsold inventory can be left as is or cancelled with `POST /inventory/actions/cancel`. Decide when we build it.

## First live test (do this before writing the adapter)

On one low-stakes game, in a local Claude Code session with the tokens in the environment:

1. `GET /account` and `GET /events` for the game. Confirm auth and event lookup.
2. `POST /purchases` with one line, `broadcast: false`. Read it back with `GET /purchases/{id}` and `GET /inventory?eventId=`.
3. `PUT /inventory` with `{id, listPrice, broadcast: false}`. Confirm whether the price is absolute or a delta.
4. Flip `broadcast: true`, check the listing appears on Vivid Seats, then flip it back. Note the delay.
5. Record every answer in this file and close the matching [open questions](README.md#open-questions).

## Implementation notes for the Rust adapter

- **Rate limiting is mandatory:** a per-endpoint limiter matching the [published limits](README.md#rate-limits), plus a global cap under 600 requests per minute.
- **Tolerant deserialization:** enums should accept unknown values instead of failing, and structs should ignore unknown fields. The spec is large and changes.
- **Flexible date parsing:** Skybox returns ISO 8601 date-times with a variable number of fractional-second digits. A strict fixed-format parser will fail on some responses.
- **Dry run everywhere:** every write takes a dry-run flag that logs the exact request it would send.
- **Idempotency:** tag every purchase openseason creates with an `externalRef` (for example `openseason:<season>`) and check for it before creating, so a retry can't create duplicate inventory.
- **No local mirror:** caches live only for the duration of a command, in line with the API terms.
