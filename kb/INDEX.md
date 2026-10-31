# Knowledge Base Index

This is the entry point to openseason's knowledge base. Every knowledge file gets one line here, so people and AI sessions can find what they need without loading everything.

## Layout

| Folder | Purpose | Status |
|---|---|---|
| `platforms/` | Point-of-sale and marketplace APIs: authentication, endpoints, listing and inventory flows, rate limits, gotchas | Skybox, Vivid Seats |
| `rust/` | Rust stack decisions and patterns: crates chosen (async runtime, HTTP client, storage), why, and pitfalls hit | empty |
| `decisions/` | Short decision records: what we chose, the alternatives, and the date | empty |
| `sources/` | Pointers to source material (docs, URLs) and when each was last checked | Skybox |

## Conventions

- One topic per file, with a descriptive file name (for example, `platforms/<platform>/listings-create.md`).
- Each file starts with its purpose, its sources, and the date it was last verified.
- Record our conclusions and gotchas, not copies of vendor documentation; link to the originals.
- This repository is public. No credentials, tokens, account numbers, seat locations, or personal sales data in any file.

## Files

- [`friction.md`](friction.md): agentic friction log; where building with AI is slower or more manual than it should be, with status and workarounds
- [`platforms/skybox/README.md`](platforms/skybox/README.md): **start here for Skybox**; base URL, auth, rate limits, endpoint map, enums, open questions
- [`platforms/skybox/api-terms.md`](platforms/skybox/api-terms.md): what the Skybox API terms allow and forbid, and the design constraints that follow (no local mirror, no aggregation, no analytics)
- [`platforms/skybox/listing-flow.md`](platforms/skybox/listing-flow.md): end-to-end flow for listing season tickets: vendor, events, purchase-created inventory, pricing, sale detection, fulfillment, first live test
- [`platforms/skybox/onboarding.md`](platforms/skybox/onboarding.md): getting a Skybox seller account live on Vivid Seats; steps, gotchas, support mailboxes
- [`platforms/skybox/webhooks.md`](platforms/skybox/webhooks.md): Skybox webhook subscriptions, topics, payload, signature, delivery guarantees, and what they mean for sale detection
- [`platforms/vivid-seats/marketplace-rules.md`](platforms/vivid-seats/marketplace-rules.md): Vivid Seats large-seller rules that constrain listings (in-hand dates, day-of-event, masking, opt-in events)
- [`sources/skybox.md`](sources/skybox.md): every source behind the Skybox files, with last-checked dates
