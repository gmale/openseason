# Vivid Seats marketplace rules for large sellers

**Purpose:** Marketplace rules that constrain how openseason may list on Vivid Seats (directly or through Skybox). Validation logic should enforce these before anything is published.
**Sources:** [Large Seller Support Portal](https://brokersupport.vividseats.com/support/home), articles linked per rule below. Several were last modified years ago; treat the Large Seller Handbook as authoritative where they differ.
**Last verified:** 2026-10-31

## Rules

| Rule | What it says | Source (last modified) |
|---|---|---|
| **In-hand date** | Must be at least 7 calendar days before the event. Exceptions: documented primary-market delivery delays, "Special Delivery" listings, and specific teams or venues known for late delivery. A missing or past in-hand date displays as in hand, and a missing date is treated as the day of sale. | [Latest in-hand date](https://brokersupport.vividseats.com/support/solutions/articles/1000206985-what-is-the-latest-in-hand-date-i-am-allowed-to-have-are-there-exceptions-) (2026-01-15) |
| **Day of event** | Listings may stay purchasable after start time at Vivid Seats' discretion. The seller must remove anything they don't want sold. | [Listings on event day](https://brokersupport.vividseats.com/support/solutions/articles/1000207157-how-long-will-my-listings-stay-up-on-the-day-of-the-event-) (2015-10-02) |
| **Masking** | Allowed only at non-UK venues and within the handbook's upgrade guidelines (section 7.1): same section as the seats delivered, row masked by at most five rows, and never into the first 10 rows. Seat numbers aren't shown at checkout for non-UK venues. | [Masking](https://brokersupport.vividseats.com/support/solutions/articles/1000206984-am-i-allowed-to-mask-my-section-row-or-seat-numbers-) (2025-07-01) |
| **Opt-in events** | High-profile events require per-event approval (proof of purchase, delivery method, account standing). Listings won't appear until approved; request through Seller Services. | [High-profile events](https://brokersupport.vividseats.com/support/solutions/articles/1000206988-my-listings-are-not-showing-up-for-a-high-profile-event-how-do-i-list-tickets-for-this-event-) (2015-10-02) |
| **Account holds** | Putting the account on hold in Preferences does not deactivate existing listings. Ask Seller Operations to deactivate first. | [Preferences & API](https://brokersupport.vividseats.com/support/solutions/articles/1000206528-preferences-api) |

## Implications for openseason

1. **Validate before publishing.** Reject an in-hand date later than event date minus 7 days unless the user explicitly marks an allowed exception.
2. **Own the day-of cutoff.** Don't rely on the marketplace to pull listings at start time. openseason should delist unsold seats at a user-configured time before the event.
3. **No masking.** openseason lists the real section and row. Masking is permitted within limits but adds risk for little benefit to an individual seller; revisit only by explicit decision.
4. **Surface "not visible" states.** A listing accepted by the POS but missing from the marketplace (opt-in events, mapping problems) should show as a warning, not as live.
5. **Pausing means delisting.** A "pause selling" feature must actively delist, not just flip an account preference.
