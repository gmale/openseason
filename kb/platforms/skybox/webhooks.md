# Skybox webhooks

**Purpose:** How Skybox pushes change notifications (inventory, sales, holds) and what that means for openseason's sale detection.
**Sources:** [Skybox Webhooks](https://skybox.vividseats.com/api-docs/webhooks/) (official)
**Last verified:** 2026-10-31

## Summary

Skybox can POST a JSON notification to a URL you register whenever inventory, purchases, invoices, invoice lines, or holds change. Delivery is best-effort: unordered, possibly duplicated, possibly missed, and muted after repeated failures. Webhooks are a latency optimization, never the source of truth. Polling must exist anyway.

## Managing subscriptions

All requests use JSON and the standard Skybox auth headers (see [README](README.md#authentication)).

| Method | Path | Purpose |
|---|---|---|
| GET | `/webhooks` | List the account's subscriptions |
| POST | `/webhooks` | Create a subscription |
| GET | `/webhooks/{id}` | Read one subscription |
| PUT | `/webhooks/{id}` | Replace a subscription (same fields as create) |
| DELETE | `/webhooks/{id}` | Remove a subscription |

Create/update fields:

| Field | Meaning |
|---|---|
| `topic` | One of `INVENTORY`, `PURCHASE`, `INVOICE`, `LINE`, `HOLD` |
| `url` | Where notifications are POSTed |
| `headers` | Extra headers Skybox adds to each delivery, as one comma-separated string of `Name: value` pairs |
| `secret` | Key used to sign each delivery |

## Topics

| Topic | Actions | Fetch the full record with |
|---|---|---|
| `INVENTORY` | `CREATED`, `UPDATED` | `GET /inventory/{id}` |
| `PURCHASE` | `CREATED`, `UPDATED` | `GET /purchases/{id}` |
| `INVOICE` | `CREATED`, `UPDATED` | `GET /invoices/{id}` |
| `LINE` | `CREATED`, `UPDATED` | `GET /lines/{id}` |
| `HOLD` | `CREATED`, `UPDATED`, `DELETED` | `GET /holds/{id}` |

Inventory events also fire when a listing is split or merged. In POS terms an **invoice** is normally the sell side (a sale to a customer) and a **purchase** the buy side (acquiring tickets), which would make `INVOICE` openseason's sale signal. **Unverified for Skybox:** confirm against the API reference before relying on it.

## Payload

```json
{
  "id": 123,
  "accountId": 456,
  "topic": "INVENTORY",
  "action": "UPDATED",
  "generatedBySameAccount": true,
  "current":  { "inventory": { "listPrice": 210.0 } },
  "previous": { "listPrice": 240.0 }
}
```

- `id` is the affected record's ID.
- `current` is nested under a key named for the entity (`inventory` in the documented example). `previous` appears only on `UPDATED` and holds the old values of the changed fields directly, without the entity key.
- On `UPDATED`, `current` holds only the changed fields. On `CREATED`, it holds the record.
- `generatedBySameAccount` tells you whether your own account caused the change, which lets openseason ignore echoes of its own writes.
- Inventory fields seen in the documented example include `eventId`, `quantity`, `section`, `row`, `lowSeat`, `highSeat`, `cost`, `ticketIds`, `stockType`, `splitType`, `listPrice`, `inHandDate`, `tags`, and `broadcast`.

The values above are illustrative; the shape follows the documented example. Deserializers should tolerate unknown fields.

## Signature

Each delivery carries a `Webhooks-Signature` header: the hex-encoded HMAC-SHA1 of the request body, keyed with the subscription's `secret`. Verify it over the raw body bytes before parsing, using a constant-time comparison.

## Delivery guarantees

- Skybox expects an acknowledgment within about one second. Respond immediately and process asynchronously.
- No ordering guarantee. Notifications may be missed or duplicated.
- Repeated delivery failures cause Skybox to mute notifications temporarily, with longer mute periods each time, up to 24 hours. The account email is notified each time.
- Skybox itself recommends periodic polling to catch anything missed.

## Implications for openseason

1. **Polling first, webhooks later.** A webhook needs a public HTTPS endpoint that answers in under a second, which most individual sellers don't have. The MVP should detect sales by polling invoices on a short interval. Webhooks become an optional accelerator.
2. **Idempotent handlers.** Treat every notification as a hint: fetch the current record by ID and reconcile. Never apply a payload's diff blindly, because events can arrive out of order or twice.
3. **Reconciliation loop.** Even with webhooks on, run a periodic full reconciliation of open listings and recent invoices. This is the safety net behind the "no double sales" principle.
4. **Echo suppression.** Use `generatedBySameAccount` plus openseason's own audit log to tell its own changes apart from changes made in the Skybox UI or by marketplaces.
5. **Rust shape (when we get there):** an HTTP handler that verifies the signature, enqueues the event, and returns 200 immediately; a worker that dedupes and reconciles. Signature check: HMAC-SHA1 over raw bytes, hex compare in constant time.
