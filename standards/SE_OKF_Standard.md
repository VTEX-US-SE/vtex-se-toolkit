# SE OKF Standard — Opportunity Knowledge Folders
 
> How every opportunity's materials are organized in Google Drive so the SE Co-pilot can navigate them. **Adopted org-wide.**
> **Status: provisional** — pending confirmation of Rocketlane semantics (pre-sale pipeline vs delivery). The structure holds regardless; only stage names may change.
 
## Folder structure
```
/SE Opportunities/
  {OPP_ID} - {Customer}/
    00_index.md                            ← agent-generated entry point
    01_discovery/
      discovery_transcript_YYYYMMDD.docx   (+ .md rendition)
      discovery_summary_YYYYMMDD.docx      (+ .md rendition)
    02_rfp/
      rfp_YYYYMMDD.xlsx                     ← answered RFP (system of record)
      rfp_YYYYMMDD.md                       ← markdown rendition
      rfp_analysis_YYYYMMDD.md              ← post-processing analysis (requirement→capability + flags)
    03_demo/
      demo_script_YYYYMMDD.md
      demo_transcript_YYYYMMDD.docx        (+ .md rendition)
      demo_summary_YYYYMMDD.docx           (+ .md rendition)
    04_additional/
      YYYYMMDD_{topic}.md                   ← extra knowledge base (extra meetings, refs, questionnaires)
    05_solution_design/
      architecture_YYYYMMDD.excalidraw      ← architecture diagram
      architecture_YYYYMMDD.md              ← markdown rendition
      solution_design_YYYYMMDD.docx         ← formal deliverable
```
**Golden rule:** every file carries `YYYYMMDD` and a self-describing name.
 
## 00_index.md — agent-generated
The entry point. The agent generates/regenerates it from folder contents + Rocketlane; it is **not maintained by hand**. Frontmatter holds only the join key and what Rocketlane lacks (`opp_id`, `vertical`, `region`). Canonical state (stage / owner / ACV / last activity) comes from **Rocketlane via `opp_id`** — never duplicate it in the index.
 
## Meeting transcripts — Google Meet + Gemini
Meetings are recorded in Google Meet; **Gemini auto-generates two `.docx` files per meeting**: (1) the full transcript with timestamps, (2) a summary. Store both and process each to a markdown rendition:
- `discovery_transcript_YYYYMMDD.docx` → `discovery_transcript_YYYYMMDD.md`
- `discovery_summary_YYYYMMDD.docx` → `discovery_summary_YYYYMMDD.md`
(same pattern for `demo_*`). The agent reads the `.md`; the `.docx` is the original.
 
## Document processing — docx / pdf / xlsx / csv → markdown (IMPORTANT)
Official VTEX and prospect documents — and the Gemini transcripts/summaries above — arrive as `.docx`, `.pdf`, `.xlsx` or `.csv`. The connector reads markdown most reliably (tables, layout and diagrams degrade in the native formats). Therefore, for every such document:
1. **Keep the original** (system of record).
2. **Produce a markdown rendition** alongside it (same basename, `.md`).
3. **Register both in `00_index.md`**, marking which is original and which is the rendition.
4. **The agent generates the rendition** when it is missing — the SE does not convert by hand.
When original and rendition diverge, the original wins — regenerate the rendition.
 
## RFP flow
1. Client sends the RFP (often `.xlsx`/`.docx`).
2. SE answers in the spreadsheet → `rfp_YYYYMMDD.xlsx` (system of record).
3. Process to `rfp_YYYYMMDD.md` (rendition).
4. SE/agent writes `rfp_analysis_YYYYMMDD.md` — the requirement→VTEX-capability mapping and flags. **This is the post-processing analysis, produced after the answered xlsx exists.**
## Architecture / solution design
Draw the architecture in Excalidraw (`architecture_YYYYMMDD.excalidraw`), export a markdown rendition (`architecture_YYYYMMDD.md`) so the agent can read it (the connector can't read diagrams/images), then generate the formal `solution_design_YYYYMMDD.docx` via `SE_SolutionDesign.md`.
 
## Security
Credentials (demo environments, etc.) are never inline — reference 1Password (SE vault).
 
## Worked example
See `EXAMPLE - SE Opportunities/RL-EXAMPLE - Acme Fashion` in this same Drive folder.