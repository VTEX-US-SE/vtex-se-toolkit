---
name: portfolio-risk
description: >-
  Triage a VTEX Solution Engineer's pre-sales opportunity portfolio in Rocketlane
  and surface what needs attention. Use whenever an SE asks "which of my
  opportunities need attention", "what's stalled", "show me my pipeline health",
  "which deals are slipping", "what's past its close date", or wants a prioritized
  triage of their active opportunities on the "Template SE" workflow. Scores each
  active project by Opportunity Stage progression, expected-close date, stalled
  phase, data hygiene gaps, and stage/Rocketlane mismatches (e.g. an opportunity
  marked Lost or Won but still left open), classifying each as red / yellow /
  green with the reason and a suggested next action. READ-ONLY across Rocketlane
  and Slack.
---

# SE pipeline / portfolio triage

## Purpose

An SE carries many pre-sales opportunities at once, each moving through
Discovery → RFP → Demo → POC → Solution Design → Closing. This skill triages the
active portfolio so the SE spends attention where it matters — catching stalled
deals, opportunities past their expected close, and data hygiene issues before
they hurt the pipeline or the handover to PS.

These are non-billable "Template SE" projects with no hours or customer mood, so
**do not score on budget burn**. Score on opportunity momentum. Read-only: only
read Rocketlane and Slack; never modify data.

## Inputs to gather

- **Whose portfolio.** Default to the current user — `get_my_profile` → `userId`.
  Accept "the whole team" or a named colleague (resolve via `get_users`).
- **Optional filters.** Region/macro-region, stage, or phase, if asked.

## Workflow

1. **List active opportunities.** `get_projects` filtered by `teamMemberId` = the
   target user and `status` = In progress (value 2). Page through all results.
   Fetch detail with `includeAllFields: true` for the fields below (fetch detail
   only for non-obvious ones if the list is large, to save calls).

2. **Score each opportunity on SE-relevant signals:**
   - **Past expected close:** `dueDate` / Opp Closed Date is in the past while the
     project is still In progress. Compute days past.
   - **Stage / Rocketlane mismatch:** `Opportunity Stage` = **Lost** or **Won**
     but the project is still open (not closed/handed over). Lost-but-open and
     Won-but-not-handed-over are both data hygiene flags worth surfacing.
   - **Stalled:** stuck in one phase for weeks, sitting in **Stand-by**, or no
     task movement (no recently completed/started playbook tasks) — especially
     with `progressPercentage` near 0 well after the start date.
   - **Blocked work:** playbook tasks in **Blocked** / **on hold** status
     (`get_tasks` by `projectId`).
   - **Data gaps:** missing `Opportunity ID`, `ACV`, or `Estimated GMV` on an
     opportunity that's progressing — hurts forecasting and handover.
   - **High value:** large `ACV` / `Estimated GMV` raises the priority of any
     other flag.

3. **Classify:**
   - 🔴 **Red** — past expected close while open, Lost-but-still-open, or a
     high-value deal stalled/blocked.
   - 🟡 **Yellow** — approaching close with low progress, stalled a few weeks,
     blocked task, or notable data gaps.
   - 🟢 **Green** — progressing through the playbook on schedule.

4. **Slack cross-check.** For Red/Yellow items (and any opportunity that looks
   suspiciously quiet), find the client's channel with `slack_search_channels`
   (naming varies, e.g. `#emea-pt-benfica`, `#campaign-biopak-b2b-australia`) or
   fall back to `slack_search_public_and_private`, then scan recent messages and
   **compare against the Rocketlane state**. The mismatch itself is a signal:
   Slack shows the deal advanced but Rocketlane is behind; Slack shows a blocker
   or lost/churn signal Rocketlane doesn't reflect; or Slack has gone silent for
   weeks while a close date looms. Surface these as part of the reason. Use
   messages as evidence only — never paste private conversation into the output.

## Output format

One prioritized table, Red first, then Yellow, then a compact Green roll-up:

```
## Pipeline triage — [user] · [date]

🔴 Needs attention
| Opportunity | Stage | Phase | Signal | Detail | Suggested action |
|---|---|---|---|---|---|

🟡 Watch
| Opportunity | Stage | Phase | Signal | Detail | Suggested action |

🟢 On track (N): Client A, Client B, ...
```

Keep "Suggested action" concrete: "close out — opp marked Lost but still open",
"chase customer for POC sign-off", "rebaseline expected close", "fill ACV/Opp ID
before handover". Lead with the few that truly need the SE today.

## Notes

- The single most common, highest-value flag here is the **stage/RL mismatch** —
  opportunities marked Won or Lost but still left open in Rocketlane.
  Always surface these; they quietly distort the SE's active list.
- Be precise on the "why" — "47 days past expected close, still in Negotiate"
  beats "looks risky".

---

## Shared standards (source of truth)

This skill is grounded in the VTEX SE governance standards in [`../../standards/`](../../standards/). When this skill and a standard disagree, **the standard wins** — those files are the single source of truth that the Claude Project and Cowork consume too.

- `SE_Governance_Rubric.md` — scoring / health by stage
- `SE_Playbook_NextSteps.md` — suggested next action
- `SE_OKF_Standard.md` — a missing OKF folder is itself a risk flag
