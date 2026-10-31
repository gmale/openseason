# openseason

**Free, open-source tooling that helps season ticket holders sell their tickets without the busywork or another layer of fees.**

[![License: MIT OR Apache-2.0](https://img.shields.io/badge/license-MIT%20OR%20Apache--2.0-blue.svg)](#license)
[![Status: early development](https://img.shields.io/badge/status-early%20development-orange.svg)](#roadmap)

> [!NOTE]
> Early development. Nothing here is ready to use yet.

## Motivation

I own season tickets, and selling the games I can't attend is slow, manual, and painful. openseason exists to remove that friction.

This is not a business or an attempt to compete with the companies that run ticket marketplaces. A spare-time project can't, and that was never the goal. The goal is to work in harmony with what the platforms allow and build the missing piece on top. Existing automation services are expensive and add another cut to a transaction that already passes through many hands. For someone who just wants their tickets sold, the only sensible option left is a free one.

Building in the open is deliberate: every line is public, so there is no room for scraping or anything else the platforms wouldn't welcome. Done well, this is good for them too, since more season ticket holders listing means more inventory for buyers. Done poorly, it deserves to be shut down. So the standard is simple: do it right, or don't do it.

## Built entirely by AI

openseason is not AI-assisted; it is AI-built. I'm a professional software engineer, but I won't write a single line of code (not even this README), and I don't know Rust. Call it vibe coding with a standard attached.

That is by necessity, not a shortcut: I have the problem and the quality bar, not the hours. I set direction and judge the results. AI does the research, design, code, tests, and documentation.

What separates this from slop is discipline, not authorship: tests on every change, decisions recorded in the [knowledge base](kb/INDEX.md), and everything in public. Under the hood it's Rust, with a network-free core, one adapter per platform, and a command-line front end.

## Principles

- **Sell-side only.** It helps you list and sell tickets you own. It will never buy tickets.
- **Official interfaces only.** Documented APIs, your own credentials, and each platform's terms. No scraping, no private endpoints, no evading bot detection or rate limits.
- **Your data stays yours.** Credentials and records stay on infrastructure you control. No telemetry.
- **Free.** No fees, no commissions, no paid tier.
- **Safety first.** Selling a seat twice is the worst failure a seller can have. Preventing it comes before every other feature.

Changes that conflict with these principles will not be merged.

## What it will do

Built for an individual with a handful of seats per game, not a broker managing thousands.

- **One inventory, many outlets.** List through point-of-sale and marketplace APIs from a single source of truth.
- **No double sales.** When seats sell anywhere, they come down everywhere.
- **Pricing you control.** Automatic rules, with manual overrides any time.
- **A clean record.** A sales ledger and a year-end summary for your accountant or tax software.

## Roadmap

- [x] Repository, licensing, and knowledge base
- [ ] Survey a first point-of-sale API: authentication, inventory, and listings
- [ ] Core domain model: events, seats, listings, and sales
- [ ] First platform adapter, plus a CLI to create, update, and remove listings
- [ ] Double-sale protection, dry-run mode, and an audit log
- [ ] Pricing rules
- [ ] Sales ledger and tax summary export

## Contributing

Issues are welcome, especially from season ticket holders describing how they sell today and where it hurts. Please open an issue before starting a pull request.

## Security

Report security issues privately through [GitHub's private vulnerability reporting](https://github.com/gmale/openseason/security/advisories/new), not in a public issue.

## Disclaimer

openseason is independent and not affiliated with or endorsed by any team, league, marketplace, or point-of-sale provider. All trademarks belong to their owners. Check your season ticket agreement and each platform's terms before reselling; following them is your responsibility. Provided as is, without warranty.

## License

Licensed under either of [Apache License, Version 2.0](LICENSE-APACHE) or [MIT License](LICENSE-MIT), at your option.

Unless you explicitly state otherwise, any contribution intentionally submitted for inclusion in openseason by you, as defined in the Apache-2.0 license, shall be dual licensed as above, without any additional terms or conditions.
