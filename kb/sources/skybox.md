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
| [Skybox help center](https://skybox.zendesk.com/hc/en-us) | FAQs and tutorials | not reviewed |
| [SkyBox welcome page](https://skybox.vividseats.com/welcome.html) | Product capabilities and integrations | 2026-10-31 |
| [Large Seller Support Portal](https://brokersupport.vividseats.com/support/home) | Vivid Seats marketplace rules and handbook | 2026-10-31 |
| Onboarding correspondence with Skybox support (private) | Account setup steps, contacts, payment terms | 2026-10-31 |

## Notes

- The spec lists a "Stage" server on an internal Vivid Seats corporate domain; there is no known public sandbox.
- The Swagger page loads its content with JavaScript, so plain HTTP fetchers see an empty page. Read the spec JSON directly instead.
