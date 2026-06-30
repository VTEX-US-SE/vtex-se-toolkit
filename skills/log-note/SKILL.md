---
name: log-note
description: >-
  Log an annotation/note about a VTEX SE opportunity into Rocketlane, always
  translated into English. Use whenever a Solution Engineer says "log this note
  on the Accor project", "add a note to Rocketlane: ...", "registra essa
  anotação no projeto X", "anota no RL que ...", "save this update to the BioPak
  opportunity", or dictates a project update/observation (in any language) they
  want recorded in Rocketlane. The skill translates the note to English, picks
  the most relevant playbook task, shows the SE exactly what will be written and
  where, and writes it only after explicit confirmation. This is the only skill
  in the toolkit that WRITES to Rocketlane — it always confirms first.
---

# Log a note to Rocketlane (in English)

## Purpose

SEs work across many languages (PT, ES, EN…), but Rocketlane is the shared,
regional/global record — so notes should land there **in English**, consistently,
no matter what language the SE dictated them in. This skill captures a note from
chat, normalizes it to clear professional English, and records it on the right
Rocketlane task.

## Critical safety rules

This is the **only** skill that modifies Rocketlane. Treat every write as
consequential:

- **Always translate to English first.** Regardless of the language the SE used,
  the text written to Rocketlane must be in English. Preserve meaning, names,
  product/feature terms, numbers, and dates. Don't editorialize or add facts.
- **Always confirm before writing.** Show the SE (1) the final English text,
  (2) the exact project, (3) the exact task and field it will go to. Write only
  after a clear "yes". If anything is ambiguous, ask — never guess a destination.
- **Never overwrite.** Append to the existing note as a new dated entry; keep
  prior content intact.
- **Write to the internal note field** (`taskPrivateNote`), which is team-only —
  not the customer-visible description — unless the SE explicitly asks otherwise.

## Workflow

1. **Capture the note and the target opportunity.** Get the note text and which
   project it's about. Resolve the project with `get_projects` (search by name;
   if multiple match, list and ask).

2. **Translate to English.** Produce a faithful, concise English version of the
   note. If the SE wrote in English already, keep it (tidy only lightly).

3. **Pick the most relevant playbook task.** List the project's tasks with
   `get_tasks` (by `projectId`). Map the note's topic to the playbook step:
   - discovery findings / requirements → *Discovery Document* (or *Discovery
     Meeting*)
   - RFP details → *RFP*
   - demo feedback → *Demo*
   - POC progress/blockers → *POC* (or *Any specific requirement covered*)
   - architecture / solution decisions → *Architecture* / *Solution Design
     Document*
   - handover info → *Knowledge Transfer Document to PS* / *Handover to PS*
   If the right task isn't obvious, ask the SE which task to attach it to rather
   than guessing.

4. **Read the current note.** Fetch that task with `get_tasks`
   (`taskId`, `includeAllFields: true`) to read the existing `taskPrivateNote`,
   so you can append rather than replace.

5. **Confirm.** Show the SE a preview:
   ```
   Project: [name]
   Task:    [task name]  ·  field: internal note (team-only)
   Append:  [<date>] [author] — <English note>
   ```
   Wait for explicit confirmation.

6. **Write.** Call `update_task` with `task_id` and
   `task_data.taskPrivateNote` = existing HTML + a new appended paragraph, e.g.
   `<p>[2026-06-25] Gabriela — &lt;English note&gt;</p>`. Use HTML; keep prior
   entries above the new one. Confirm to the SE that it's saved.

## Notes

- Date each entry (use today's date) and, when known, name the author, so the
  internal note reads as a running log.
- If the SE dictates one note that spans several topics/tasks, offer to split it
  across the matching tasks, or log it once on the most relevant — their call.
- Keep the English crisp and factual. This note feeds the eventual PS handover, so
  clarity matters more than literal word-for-word translation.

---

## Shared standards (source of truth)

This skill is grounded in the VTEX SE governance standards in [`../../standards/`](../../standards/). When this skill and a standard disagree, **the standard wins** — those files are the single source of truth that the Claude Project and Cowork consume too.

- `SE_OKF_Standard.md` — note conventions and where artifacts live
