# Skybox sources

**Purpose:** Every source behind the Skybox knowledge files, so findings can be re-checked when Skybox changes.
**Last verified:** 2026-10-31

| Source | What it covers | Last checked |
|---|---|---|
| [API docs (Swagger UI)](https://skybox.vividseats.com/api-docs/) | Intro, token steps, link to spec and terms | 2026-10-31 |
| [OpenAPI spec](https://skybox.vividseats.com/services/openapi.json) | All endpoints, schemas, enums, servers, auth schemes | 2026-10-31 |
| [Webhooks guide](https://skybox.vividseats.com/api-docs/webhooks/) | Webhook topics, payload, signature, delivery | 2026-10-31 |
| [API Terms of Use](https://skybox.vividseats.com/api-terms) | Permitted and prohibited uses, request caps | 2026-10-31 |
| [Getting Started with Skybox APIs](https://skybox.zendesk.com/hc/en-us/articles/6769735238043-Getting-Started-with-Skybox-APIs) | Application token and API token steps (2022) | 2026-10-31 |
| [SkyBox API Rate Limits](https://skybox.zendesk.com/hc/en-us/articles/5999881334427-SkyBox-API-Rate-Limits) | Per-endpoint limits (2024) | 2026-10-31 |
| [Skybox help center](https://skybox.zendesk.com/hc/en-us) | FAQs and tutorials; its search API (`/api/v2/help_center/articles/search.json?query=`) is the fastest way to find articles | 2026-10-31 |
| [What is SkyBox Integration?](https://skybox.zendesk.com/hc/en-us/articles/205525125) | Direct integration covers Vivid Seats and StubHub only (2026) | 2026-10-31 |
| [Broadcast vs. tags, price, holds](https://skybox.zendesk.com/hc/en-us/articles/207811963) | Broadcast semantics (2022) | 2026-10-31 |
| [Downloader auto-transfers](https://skybox.zendesk.com/hc/en-us/articles/20200613854747) | Transfer automation requirements (2024) | 2026-10-31 |
| [Exchange-filter tags](https://skybox.zendesk.com/hc/en-us/articles/207212686), [split types](https://skybox.zendesk.com/hc/en-us/articles/206510933), [StubHub and custom splits](https://skybox.zendesk.com/hc/en-us/articles/207021426), [StubHub event link](https://skybox.zendesk.com/hc/en-us/articles/115005547623), [double-sale handling](https://skybox.zendesk.com/hc/en-us/articles/207282156), [manual exchange listings removed](https://skybox.zendesk.com/hc/en-us/articles/201279985), [purchase event mapping](https://skybox.zendesk.com/hc/en-us/articles/360057211791), [date format change](https://skybox.zendesk.com/hc/en-us/articles/19133263848987), [Vivid integration setup](https://skybox.zendesk.com/hc/en-us/articles/207538973), [API invitations](https://skybox.zendesk.com/hc/en-us/articles/201450845) | Practitioner details, gathered by a research agent; the first three rows above were re-checked first-hand | 2026-10-31 |
| [Slash: connecting Skybox](https://help.slash.com/en/articles/9954246-connecting-your-skybox-account-to-slash), [Reveal: connecting a POS](https://docs.revealmarkets.com/article/128-how-do-i-connect-to-my-pos) | Third-party descriptions of the API invitation email | 2026-10-31 |
| [2019 Ruby gists](https://gist.github.com/alessandrostein/b49d4ee1b73df363d47b86903ef9abff) | Old inventory and ticket schemas; superseded by the current spec | 2026-10-31 |
| [SkyBox welcome page](https://skybox.vividseats.com/welcome.html) | Product capabilities and integrations | 2026-10-31 |
| [Large Seller Support Portal](https://brokersupport.vividseats.com/support/home) | Vivid Seats marketplace rules and handbook | 2026-10-31 |
| Onboarding correspondence with Skybox support (private) | Account setup steps, contacts, payment terms | 2026-10-31 |

## Notes

- The spec lists a "Stage" server on an internal Vivid Seats corporate domain; there is no known public sandbox.
- The Swagger page loads its content with JavaScript, so plain HTTP fetchers see an empty page. Read the spec JSON directly instead.
- No open-source Skybox client was usable as prior art. An MCP server (`theticketman/skybox-mcp`) is listed in directories but its repository is not accessible. `agoferro/skybox-private` appears to be a copy of Skybox's own front end; do not use it.
