---
name: status-board
description: >-
  Review and update the project statuses of a VTEX SE's active Rocketlane
  opportunities, right in chat. Use whenever a Solution Engineer says "let me
  check the statuses of my opps", "show me a table where I can mark each
  opportunity's status", "I want to update the status of several deals at once",
  "mark these as on hold / completed / cancelled", or wants to review and
  bulk-update opportunity/project statuses. Shows a status table in chat, lets
  the SE mark the new statuses, and writes them back to Rocketlane via
  update_project — only after confirming the exact old → new changes. WRITES to
  Rocketlane, always confirmed first.
---

# SE status check & update (in chat)

## Purpose

An SE often wants to eyeball all their opportunities and fix the project statuses
in one pass — close out deals marked Lost that are still open, put stalled ones
On Hold, mark finished ones Completed. This skill does that conversationally:
present the statuses as a table, let the SE say what to change, and apply the
updates to Rocketlane after confirmation.

## What status this changes (and what it doesn't)

- It updates the **project status** — the Rocketlane field with values
  *In progress (2), On Hold (29), Completed (3), Cancelled (17), Backlog (5)* —
  via `update_project` (`project_data.status.value`).
- It shows the **Opportunity Stage** as read-only context to help the SE decide.
  This skill changes the project status only; if the SE also wants to change the
  Opportunity Stage, that's done directly in Rocketlane.

## Workflow (chat-first)

1. **Load the opportunities.** `get_my_profile` → `userId`; `get_projects`
   filtered by `teamMemberId` = userId and `status` = 2 (In progress), paging
   through all. For context, fetch each with `includeAllFields` to read current
   project `status`, `Opportunity Stage`, and close date (`Opp Closed Date` /
   `dueDate`).

2. **Show a status table** in chat — one row per opportunity:
   `Client · Stage · Close date (flag if past/soon) · Current project status`.
   Order by what likely needs a decision (Lost-but-open and past-close first).
   Then offer an interactive pick: either the SE types the changes in free text
   ("mark KERING and Cristian Lay as Cancelled, Accor as On Hold"), or — for a
   lightweight dropdown-style picker with no custom UI — use the built-in
   **AskUserQuestion** tool, one question per opportunity with the five statuses
   as options. AskUserQuestion allows up to 4 questions per call, so batch the
   opportunities that most likely need a change (Lost-but-open and past-close
   first) in groups of 4 rather than asking about all of them. This is the
   cheap, fast interactive option — prefer it over building an HTML artifact.

3. **Echo the changes for confirmation.** List every intended change as
   `Client: old → new` and ask for a clear go-ahead. Never write before this.

4. **Apply.** For each confirmed change, call `update_project` with
   `project_id` and `project_data.status.value` = the new status id. Report what
   succeeded/failed and show the updated table.

## Safety

- Writing project statuses is consequential — **always** show the `old → new`
  list and get explicit confirmation before any `update_project` call.
- Apply only the changes the SE confirmed; don't touch rows they didn't mention.
- This skill changes the project status only. If the SE wants to change the
  Opportunity Stage, point them to do it directly in Rocketlane.

## Optional: live artifact

If the SE explicitly wants a clickable board instead of chat, the same logic can
be delivered as a self-contained HTML artifact (a table with a status `<select>`
per row and a confirm-then-write Save button calling `update_project`). Default
to the in-chat flow above unless they ask for the UI.

---

## Shared standards (source of truth)

This skill is grounded in the VTEX SE governance standards in [`../../standards/`](../../standards/). When this skill and a standard disagree, **the standard wins** — those files are the single source of truth that the Claude Project and Cowork consume too.

- `SE_Governance_Rubric.md` — stage taxonomy (read-only context for status decisions)
