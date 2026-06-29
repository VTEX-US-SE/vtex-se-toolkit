---
name: meeting-to-rocketlane
description: >-
  Turn a sales/discovery/demo meeting (from Granola or Read AI) into a structured
  English note logged on the right VTEX SE opportunity in Rocketlane. Use whenever
  a Solution Engineer says "log my last meeting with Accor to Rocketlane",
  "resume a call de discovery do BioPak e registra", "what came out of my Benfica
  meeting", "turn my Granola/Read AI notes into a Rocketlane update", or wants the
  key points and action items of a recent meeting captured against an opportunity.
  Tool-agnostic: it uses whichever meeting connector is available (Granola or Read
  AI). When a meeting's name matches an opportunity, it asks which stage/card of
  that opportunity to log the note under, summarizes the meeting in English, and
  writes it to that card's Private note — only after explicit confirmation.
---

# Meeting → Rocketlane

## Purpose

SE meetings (discovery calls, demos, POC reviews) hold the real signal of where a
deal is going, but that rarely makes it into Rocketlane. This skill pulls the
meeting from the SE's notetaker — **Granola or Read AI, whichever is connected** —
summarizes the substance in English, and records it on the right opportunity, so
the project history and the eventual PS handover stay current.

## Tool-agnostic detection

Detect which meeting connector is available and use it; don't assume one:

- **Granola** — `list_meetings` / `query_granola_meetings` to find the meeting,
  `get_meeting_transcript` (and `get_meetings`) for content.
- **Read AI** — `list_meetings` to find it, `get_meeting_by_id` for the report,
  transcript, summary, and action items.

If both are connected, ask which to use (or use the one that has the meeting). If
neither is connected, say so and point the SE to connect Granola or Read AI.

## Workflow

1. **Find the meeting.** Resolve which opportunity/client it's about. Then list
   recent meetings from the available connector and match by client name and
   date. If the match isn't obvious, show the recent meetings and let the SE pick
   (don't guess).

2. **Pull the content.** Get the transcript / AI summary / action items for that
   meeting.

3. **Summarize in English.** Produce a tight, factual summary — regardless of the
   meeting's language — covering: what was discussed, decisions, customer
   concerns/blockers, and concrete **action items / next steps** (with owners if
   stated). Don't invent; if something's unclear, leave it out or mark it open.

4. **Resolve the project and ASK which stage/card to log under.** Resolve the
   Rocketlane project with `get_projects`. Then **ask the SE which opportunity
   stage/card the note should go on** — list the playbook cards from `get_tasks`
   (Discovery Meeting, Discovery Document, RFP, Demo, POC, Any specific
   requirement covered, Solution Design Document, Architecture, PS proposal
   request, Knowledge Transfer Document to PS, Handover to PS). Suggest the most
   likely card based on the meeting name/content (e.g. a "Discovery" meeting →
   Discovery Meeting/Document), but let the SE pick. Do not guess silently.

5. **Confirm, then write to that card's Private note.** Show the SE the exact
   English text, the project, and the chosen card. On confirmation, write the
   note to that task's **`taskPrivateNote`** (internal/team-only) via
   `update_task` — append a dated, attributed entry (`[YYYY-MM-DD] <author> —
   <English note>`) and keep any existing private-note content above it. Use
   HTML. This lands in the card's **Private note** section (visible when the card
   is opened). Never write without the SE's explicit OK.

## Output

- A clean English meeting summary in chat (discussion · decisions · blockers ·
  action items), then the confirm-and-log step.
- Optionally offer to feed the same summary into the `status-report` for that
  opportunity's weekly update.

## Notes

- Keep customer-sensitive detail out of anything customer-visible; the note goes
  to the internal field by default.
- This skill never modifies the meeting source — it only reads from Granola /
  Read AI. The only write is the confirmed Rocketlane note.
- If the meeting can't be found, say so plainly rather than logging a vague note.
---
name: meeting-to-rocketlane
description: >-
  Turn a sales/discovery/demo meeting (from Granola or Read AI) into a structured
  English note logged on the right VTEX SE opportunity in Rocketlane. Use whenever
  a Solution Engineer says "log my last meeting with Accor to Rocketlane",
  "resume a call de discovery do BioPak e registra", "what came out of my Benfica
  meeting", "turn my Granola/Read AI notes into a Rocketlane update", or wants the
  key points and action items of a recent meeting captured against an opportunity.
  Tool-agnostic: it uses whichever meeting connector is available (Granola or Read
  AI). It summarizes in English and, reusing the log-note flow, writes to
  Rocketlane only after explicit confirmation.
---

# Meeting → Rocketlane

## Purpose

SE meetings (discovery calls, demos, POC reviews) hold the real signal of where a
deal is going, but that rarely makes it into Rocketlane. This skill pulls the
meeting from the SE's notetaker — **Granola or Read AI, whichever is connected** —
summarizes the substance in English, and records it on the right opportunity, so
the project history and the eventual PS handover stay current.

## Tool-agnostic detection

Detect which meeting connector is available and use it; don't assume one:

- **Granola** — `list_meetings` / `query_granola_meetings` to find the meeting,
  `get_meeting_transcript` (and `get_meetings`) for content.
- **Read AI** — `list_meetings` to find it, `get_meeting_by_id` for the report,
  transcript, summary, and action items.

If both are connected, ask which to use (or use the one that has the meeting). If
neither is connected, say so and point the SE to connect Granola or Read AI.

## Workflow

1. **Find the meeting.** Resolve which opportunity/client it's about. Then list
   recent meetings from the available connector and match by client name and
   date. If the match isn't obvious, show the recent meetings and let the SE pick
   (don't guess).

2. **Pull the content.** Get the transcript / AI summary / action items for that
   meeting.

3. **Summarize in English.** Produce a tight, factual summary — regardless of the
   meeting's language — covering: what was discussed, decisions, customer
   concerns/blockers, and concrete **action items / next steps** (with owners if
   stated). Don't invent; if something's unclear, leave it out or mark it open.

4. **Map to the opportunity + playbook task.** Resolve the Rocketlane project
   (`get_projects`), and pick the most relevant playbook task for the note
   (discovery call → *Discovery Document*; demo → *Demo*; POC → *POC*;
   architecture → *Architecture/Solution Design Document*). If unsure, ask.

5. **Log it via the log-note flow.** Hand the English summary to the `log-note`
   behavior: show the SE the exact text, project, and task; append as a dated
   internal note (`taskPrivateNote`) **only after explicit confirmation**. Never
   write without the SE's OK.

## Output

- A clean English meeting summary in chat (discussion · decisions · blockers ·
  action items), then the confirm-and-log step.
- Optionally offer to feed the same summary into the `status-report` for that
  opportunity's weekly update.

## Notes

- Keep customer-sensitive detail out of anything customer-visible; the note goes
  to the internal field by default.
- This skill never modifies the meeting source — it only reads from Granola /
  Read AI. The only write is the confirmed Rocketlane note.
- If the meeting can't be found, say so plainly rather than logging a vague note.
