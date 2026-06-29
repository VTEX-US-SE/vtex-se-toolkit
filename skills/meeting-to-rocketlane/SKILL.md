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
