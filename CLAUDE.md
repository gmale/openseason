# openseason: guidance for AI sessions

openseason is a free, open-source, sell-side tool that helps an individual season ticket holder list and sell their own tickets. It is built entirely by AI: the maintainer sets direction and judges results but does not write code. Hold the work to the bar the README promises.

## Start here

- Read [`kb/INDEX.md`](kb/INDEX.md). It lists every knowledge file; open only what the task needs.
- For platform work, start with `kb/platforms/<platform>/README.md`. Each platform folder has a quick-reference README, then topic files.
- Check the platform README's **Open questions** before assuming an API behavior. Unverified facts are marked as such.

## Non-negotiables

These come from the README's Principles. Do not trade them for convenience.

- **Sell-side only.** Never write code that buys tickets.
- **Official interfaces only.** Documented APIs with the user's own credentials. No scraping, no undocumented endpoints, no evading bot detection or rate limits.
- **No secrets or personal data in the repository**, including `kb/`, which is public. Credentials come from environment variables or a git-ignored local config.
- **Safety first.** Anything that creates, changes, or removes a live listing must support a dry run and write an audit record. Preventing double sales outranks every feature.

## How we work

- **Language:** Rust, as a Cargo workspace under `crates/`. The core crate has no network code; each platform gets its own adapter crate behind a shared interface.
- **Done means tested:** every change ships with tests. Before calling work done, run `cargo fmt --check`, `cargo clippy --all-targets -- -D warnings`, and `cargo test`.
- **Write down what you learn:** durable findings (API behavior, gotchas, decisions) go in `kb/` following the conventions in `kb/INDEX.md`, with a line added to the index. Decisions go in `kb/decisions/`.
- **Log workflow friction:** when the AI workflow itself slows things down, add or update an entry in [`kb/friction.md`](kb/friction.md).
- **After pushing `kb/` changes,** remind the maintainer to sync the claude.ai project (friction F-001).
