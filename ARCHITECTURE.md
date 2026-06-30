# Architecture — SE Governance Co-pilot (proposal)

> **Status: proposal, for discussion.** This document describes a convergent architecture
> ("A4") that unifies this plugin's skills with the VTEX SE governance standards into a single
> source of truth. Opened on a branch to align with the toolkit's authors before merging.

## Why this exists

Two efforts grew in parallel covering the same scope (Rocketlane pre-sales governance for VTEX
Solution Engineering):

- **This plugin** (`vtex-se-toolkit`) — 7 Claude skills + MCP connectors, installable in Claude
  Code / Desktop.
- **A Claude Project** ("SE Governance Co-pilot") — custom instructions + a knowledge base of
  governance standards (rubrics, playbook, OKF, solution design) + connectors.

They are **complementary, not competing**: the Project writes curated knowledge to Google Drive
(OKF) and the skills act on Rocketlane. The risk is **divergence** — the same "what is a healthy
opportunity / what is the next step" logic living in two places. This proposal makes the **repo the
single source of truth** that both consume.

## The A4 architecture (four layers, all Claude-native, no server to maintain)

1. **Knowledge — Google Drive / OKF.** Opportunity docs (RFPs, decks, transcripts, Excalidraw,
   solution designs) organized as Opportunity Knowledge Folders (see `standards/SE_OKF_Standard.md`).
2. **Standards — this repo (`standards/`).** Rubrics, playbook, demo rubric, solution-design
   template + VTEX capability map, OKF standard/formatter. Versioned and PR-able. **Single source
   of truth** — the skills and the Claude Project both consume these.
3. **Reactive surface — Claude Project.** Org-wide, zero-install. Instructions + the standards as
   knowledge + connectors. Rocketlane **writes happen here**, interactively, behind a confirm gate.
4. **Proactive surface — Claude Cowork (scheduled).** Surfaces alerts/digests at three levels
   (SE / regional managers / VP). **Read-only by design** — see the boundary below.

```
Cowork (scheduled, READ-ONLY alerts) ─┐
Claude Project (reactive, writes w/ confirm) ─┤── consume ──> standards/ (this repo) ──> Google Drive / OKF
vtex-se-toolkit skills (this repo) ───┘
```

## Critical boundary: writes vs scheduled runs

The write skills (`log-note`, `status-board`, `meeting-to-rocketlane`) write to Rocketlane **only
after explicit human confirmation**. A scheduled run has no human to confirm, so:

- **Scheduled (Cowork) = read-only** — it surfaces alerts/digests (e.g. "opp X has had no activity
  in N days"); it never writes to Rocketlane.
- **Writes stay on the interactive surface**, behind the confirm gate.

## Validation (Claude Desktop, 2026-06-30)

- ✅ Install-direct works: the plugin installs and its **7 skills are available inside a Claude
  Project**.
- ✅ Atlas (knowledge base) and Google Drive (OKF) reachable and grounded from within the Project.
- ⚠️ Rocketlane connector requires per-user auth (OTP) — see "residual owners".

## Residual owners (the only ownership left after removing the server)

Claude-native surfaces remove the always-on **server** (the failure mode that stalls internal
agents when no team owns the infra). What remains to own:

1. **Connector / credentials** — the custom Rocketlane MCP connector (and its OAuth).
2. **Standards repo** — who reviews/merges changes to `standards/`.
3. **Cowork schedules** — who maintains the three alert levels.

## What this branch changes

- Adds `standards/` — the 6 governance files as the single source of truth (imported from the
  canonical Drive folder).
- Adds `project/Instructions.md` — the Claude Project system prompt (so the whole config lives in
  the repo).
- Refactors each skill's `SKILL.md` to **reference `standards/`** ("standard wins on conflict")
  instead of relying only on logic embedded in prose.
- Adds this `ARCHITECTURE.md`.

It does **not** change skill behavior beyond pointing them at the shared standards. Skill prose,
triggers, and safety rules are unchanged.
