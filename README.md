# VTEX SE Toolkit

Skills for the VTEX **Solution Engineering** team, built around how SEs run
pre-sales opportunities in Rocketlane (the "Template SE" workflow:
Discovery → RFP → Demo → POC → Solution Design → Closing → Handover to PS).

Most skills are **read-only**. Two skills write to Rocketlane — `log-note` and
`status-board` — and both write **only after explicit confirmation**.

## Skills

| Skill | Triggers on | What it does |
|---|---|---|
| `status-report` | "write my weekly status report", "update on the Accor opp" | Drafts a shareable weekly update on how an opportunity is progressing through the SE playbook — stage, phase, what moved, blockers, next step. Compares the client's Slack channel against Rocketlane. Read-only. |
| `portfolio-risk` | "which of my opportunities need attention", "what's stalled" | Triages your active pipeline into 🔴/🟡/🟢 by Opportunity Stage, expected-close date, stalled phases, blocked tasks, Slack↔Rocketlane mismatches, and Lost-but-open deals. Read-only. |
| `discovery-brief` | "prep the discovery brief for client Y" | Builds a .docx discovery/solution-design brief combining the opportunity's context, Atlas best practices, and (if the prospect already has a VTEX account) its live configuration. Read-only. |
| `poc-readiness` | "is the POC ready for the demo" | Pass/fail/warning checklist crossing the POC/Demo tasks with the real demo-account config (catalog, checkout, channels) so the demo shows well. Read-only. |
| `log-note` | "log this note on the Accor project", "anota no RL que…" | Takes a note in any language, translates it to English, and records it as an internal note on the most relevant playbook task — **after showing the text + destination and getting your OK**. |
| `status-board` | "let me check off the statuses of my opps" | Shows your active opportunities in chat with a lightweight per-opportunity status picker; writes status changes back to Rocketlane via `update_project` **after you confirm the exact old → new changes**. |

## Connectors used

Rocketlane (projects, phases, tasks), **Slack**, VTEX Account Inspector, and
Atlas (architecture knowledge base). Each skill resolves the current user via
Rocketlane's profile, so it works for any SE without per-person setup.

**Slack ↔ Rocketlane reconciliation.** `status-report` and `portfolio-risk`
actively compare the client's Slack channel against the Rocketlane record and
flag mismatches — the channel is usually fresher and more honest than the CRM, so
this catches deals that advanced (or stalled) in Slack without Rocketlane being
updated. Client channel naming varies (e.g. `#emea-pt-benfica`,
`#campaign-biopak-b2b-australia`), so the skills search by client name and fall
back to message search. **Connect the Slack connector** for these skills to work
fully; without it they fall back to Rocketlane-only.

## Calibration note

These skills are tuned for the **SE / pre-sales** reality: non-billable projects
where the signal is opportunity progression (stage, phase, playbook tasks), not
billable hours or customer-mood. They are not aimed at the billable PS
implementation projects, which use different fields (budget, hours, go-live).

## Install

Add this folder as a plugin via **Settings → Capabilities**, or share the packaged
`.skill` bundles with the team.
