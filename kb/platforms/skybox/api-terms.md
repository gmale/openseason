# Skybox API terms: constraints on openseason

**Purpose:** What the Skybox API terms of use allow and forbid, and how that shapes openseason's design. Read this before designing anything that stores, combines, or analyzes Skybox data.
**Sources:** [Skybox API Terms of Use](https://skybox.vividseats.com/api-terms) (official); [Getting Started with Skybox APIs](https://skybox.zendesk.com/hc/en-us/articles/6769735238043-Getting-Started-with-Skybox-APIs)
**Last verified:** 2026-10-31

> This is our reading of the terms for engineering purposes, not legal advice. Where a design depends on an interpretation, get written confirmation from Skybox (the terms allow exceptions with prior written consent; contact `skybox@vividseats.com`).

## Who may use the API

- Only approved Skybox users who have accepted the Skybox terms of use, are 18 or older, and haven't been suspended.
- Access requires an **application token** granted on application. The application must describe the intended use, and the API may be used **only for the purpose stated on it**. Tokens are non-transferable and must not be shared.
- Skybox may require **certification** of an application, at the applicant's cost.
- The API is free; Skybox can change, suspend, or end it at any time without notice or liability.

## Permitted purposes

Managing inventory, listing it for sale, pricing it, and accessing data about one's own sales and inventory, presented in ways that complement Skybox's services. Use that competes with Skybox, aggregates Skybox content with third-party content, or fails to attribute Skybox content is prohibited.

## Restrictions that matter for openseason

The terms list prohibited uses (a) through (o). The ones that bear directly on our design, paraphrased:

| Clause | Restriction (paraphrased) | Impact |
|---|---|---|
| (a) | No caching, recording, pre-fetching, or storing Skybox content; no bulk-download features | openseason must not keep a local copy of inventory or sales |
| (b) | No using Skybox content to build your own database of sales, inventory, or listings | Skybox must stay the system of record; no local mirror |
| (c) | No metrics or statistical analysis of the API or its content | No analytics or pricing models built on Skybox data |
| (d) | No use on behalf of a third party | Each user runs openseason with their own tokens; never a hosted service for others |
| (e), (m) | No implying endorsement or affiliation | Matches our existing "no affiliation" stance |
| (h), (j) | Don't affect server stability; at most 600 requests/minute per IP and 2,500/minute per token | Client-side rate limiting is mandatory (see also per-endpoint limits in [README](README.md#rate-limits)) |
| (k) | No combining Skybox content with third-party content | The Skybox adapter must not merge its data with other platforms' data |
| (o) | No diverting Skybox's customers from Skybox | Fine: openseason sits on top of Skybox |

## Design consequences

1. **Skybox is the source of truth, not openseason.** openseason reads current state from Skybox when it needs it and does not persist it. Double-sale prevention across marketplaces is Skybox's job (its integrations with Vivid Seats, StubHub, and Ticketmaster+ exist for exactly this); openseason's job is to drive Skybox correctly.
2. **What openseason may keep locally:** its own configuration (pricing rules, schedules, preferences) and its own audit log of the actions it took. Whether IDs and the values openseason itself sent count as "Skybox content" is an interpretation to confirm with Skybox before relying on it.
3. **"One inventory, many outlets" works through Skybox,** which already distributes to multiple marketplaces. openseason should not aggregate Skybox data with direct integrations to other platforms.
4. **Sales ledger and tax summary need care.** Building them by storing Skybox sales data conflicts with (a) and (b). Options: generate reports on demand from Skybox's own report endpoints without persisting them, or get written consent. Decide before building.
5. **Pricing rules use the user's inputs,** such as time to event, floors, and step-downs, not statistical analysis of Skybox data.
6. **The application text is binding.** Describe openseason's real use when requesting the application token, and ask the open questions in the [README](README.md#open-questions) in the same request.

## Open questions to put to Skybox

- May openseason keep a local audit log containing inventory IDs, listing prices it set, and timestamps?
- May openseason generate a year-end sales summary for the user's own tax filing from Skybox data?
- Does openseason, as open-source software each user runs with their own tokens, need its own application, or does each user apply individually?
