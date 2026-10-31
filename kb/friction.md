# Agentic Friction Log

**Purpose:** Track the places where building openseason entirely with AI is slower or more manual than it should be, so recurring costs are visible and get fixed.
**Last verified:** 2026-10-31

## How to use this log

- One entry per friction point, under the section that matches its status.
- IDs (`F-001`, `F-002`, …) are permanent and never reused.
- When the status changes, move the entry to the matching section, update its **Status** line with the date, and keep the history.
- Record facts and workarounds, not complaints. Link to sources where they exist.

| Status | Meaning |
|---|---|
| active | Still costs time; no lasting fix yet |
| mitigated | A workaround or automation removes most of the cost |
| resolved | Fixed upstream or no longer applies |
| accepted | Known, and not worth fixing |

## Active

### F-001 · Project knowledge does not auto-sync from GitHub

- **Status:** active (2026-10-31)
- **Frequency:** every change to `kb/`
- **What happens:** The claude.ai project syncs `kb/` from this repository, but only when someone clicks the sync icon in the project's Context section. Until then, chats in the project read stale knowledge.
- **Workaround:** After a push that touches `kb/`, click sync in the project. Claude should say so whenever it pushes `kb/` changes.
- **Mitigation ideas (untested):** have Claude mirror changed `kb/` files into the project directly after each push; check whether the redesigned Projects experience, which clones repositories per session, removes the step.

### F-002 · Claude cannot create repositories from a chat session

- **Status:** active (2026-10-31)
- **Frequency:** once per new repository
- **What happens:** A claude.ai chat session can only work on repositories already attached to it. Creating a repository, and granting the Claude GitHub App access to it, are manual steps on github.com.
- **Workaround:** Create the empty repository by hand, add it to the Claude GitHub App's repository list, then have Claude attach it.

### F-003 · Chat sandbox cannot reach external APIs

- **Status:** active (2026-10-31)
- **Frequency:** any task that calls a platform API
- **What happens:** The claude.ai chat workspace only reaches package registries and GitHub, so it cannot call point-of-sale or marketplace APIs. Research can happen in chat, but exercising an API cannot.
- **Workaround:** Run API work in a local Claude Code session, with credentials in the local environment, or in a cloud environment configured to allow the platform's domain.

### F-004 · Product docs lag the claude.ai interface

- **Status:** active (2026-10-31)
- **Frequency:** occasional
- **What happens:** Help articles describe a "project knowledge" panel with a "+" menu, while the app labels the area "Context" and the steps differ. Instructions Claude gives from the docs can send the user looking for controls that have moved.
- **Workaround:** When the interface doesn't match, describe what is on screen and adjust from there.

## Mitigated

_(none yet)_

## Resolved

_(none yet)_

## Accepted

_(none yet)_
