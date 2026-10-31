# Getting a Skybox seller account

**Purpose:** What an individual seller goes through to get a Skybox account live on Vivid Seats, and the gotchas along the way.
**Sources:** first-hand onboarding correspondence with Skybox support (September 2026); [What is Skybox?](https://brokersupport.vividseats.com/support/solutions/articles/1000206849-what-is-skybox-); [SkyBox welcome page](https://skybox.vividseats.com/welcome.html)
**Last verified:** 2026-10-31

## What Skybox is

Skybox is a free, cloud-based point-of-sale (POS) system run by Vivid Seats. A seller keeps inventory in Skybox and Skybox distributes listings to marketplaces. Its own integration reaches **Vivid Seats and StubHub**; Ticketmaster Resale is reachable only through the SFS program for listings with barcodes attached, and other marketplaces need a third-party automation provider ([help center](https://skybox.zendesk.com/hc/en-us/articles/205525125)). It also offers reporting, multiple users with audit trails, and a ticket downloader that can automate transfers.

Skybox comes bundled with Vivid Seats' **Large Seller Program**. You don't get one without the other.

## Onboarding steps (as experienced, September 2026)

1. **Request an account** through the Skybox sign-up form. Support replies within about three days.
2. **Intake questionnaire** by email: current marketplaces, POS, automation providers, inventory type and size, annual volume, years selling.
3. **Short intro call** (10–15 minutes) with a Skybox onboarding rep.
4. **Account creation.** The rep creates a Vivid Seats Broker Portal account (brokers.vividseats.com) and a Skybox login for the same email.
5. **Tasks before going live:**
   - Sign the **Large Seller Agreement** (an amendment to the Vivid Seats Seller Terms). It asks for a photo ID and supporting business details.
   - Complete an identity verification step.
   - Add a credit card to the Broker Portal.
   - Add inventory to Skybox.
6. **Approval.** Once the agreement is approved, the rep takes the Skybox account live on Vivid Seats.
7. **Payouts setup.** A separate **Seller Onboarding Form** collects bank (ACH) and tax details. Vivid Seats does not remit payment until it's complete.

No company or LLC was required to open the account; the company field only affects how the seller's name is displayed.

## Gotchas

- **Fan seller listings get deactivated.** Creating a large seller account turns off any listings the same person has under Vivid Seats' fan seller program.
- **Payment timing starts conservative.** New large sellers are paid after the event. Faster terms (payment on delivery) are offered after building a sales track record.
- **Two different logins.** The Broker Portal and the consumer Vivid Seats site share an email but behave differently; password resets for one can land you in the other.
- **No answer yet on a sandbox.** We asked whether a test environment exists for the API and got no reply. See [README open questions](README.md#open-questions).
- **Seller access and API access are separate.** Going live on Vivid Seats does not grant API access. The application token and API token are requested separately (see [README](README.md#authentication)).
- **The Vivid Seats integration has its own setup.** Per the help center, the Vivid Seats external account in Skybox needs its integration provider set to "Vivid Direct" and a Vivid Broker Portal API key, which the portal shows only once ([help center](https://skybox.zendesk.com/hc/en-us/articles/207538973)). [unverified first-hand]
- **The StubHub link is per event.** Events without a StubHub link don't list there until one is added ([help center](https://skybox.zendesk.com/hc/en-us/articles/115005547623)).

## Support contacts (role mailboxes)

| Mailbox | Use it for |
|---|---|
| `skyboxsupport@vividseats.com` | Skybox features, API, integrations |
| `brokers@vividseats.com` | Real-time help with an order or a near-term event |
| `accountrep@vividseats.com` | Account, payments, cancelled orders |
| `mapping@vividseats.com` | Mapping an event or listing correctly on the Vivid Seats marketplace |

The **Large Seller Handbook** lives in the [Large Seller Support Portal](https://brokersupport.vividseats.com/support/home); it covers Vivid Seats marketplace rules (in-hand dates, listing masks, day-of-event cutoffs, unconfirmed-order limits).
