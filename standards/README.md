# SE Governance Standards

Single source of truth for the VTEX SE governance logic. **Both** this toolkit's skills **and** the
Claude Project ("SE Governance Co-pilot") consume these files — keep the logic here, not duplicated
in skill prose. When a skill and a standard disagree, the **standard wins**.

| File | What it defines | Used by |
|---|---|---|
| `SE_Governance_Rubric.md` | Opportunity health 🟢/🟡/🔴 by stage; red flags | portfolio-risk, status-report, status-board, poc-account-readiness |
| `SE_Playbook_NextSteps.md` | Recommended next step by stage/situation; escalation triggers | portfolio-risk, status-report, meeting-to-rocketlane |
| `SE_Demo_Rubric.md` | Demo quality scoring (6 criteria, /18) | (Project capability) |
| `SE_SolutionDesign.md` | Solution-design structure + VTEX capability map | kickoff-brief (discovery-brief), (Project capability) |
| `SE_OKF_Standard.md` | Opportunity Knowledge Folder convention (Drive layout, renditions, `00_index`) | kickoff-brief, log-note, meeting-to-rocketlane, poc-account-readiness |
| `SE_OKF_Formatter.md` | Agent playbook to onboard an opportunity into the OKF structure | (Project / OKF setup) |

> **Provenance:** imported from the canonical Google Drive folder of the SE Co-pilot Governance
> Agent (the squad's working copy). `SE_OKF_Formatter.md` here is the **v2** content; the
> `SE_SolutionDesign.md` is the version loaded in the Project.

> **OKF** = "Open Knowledge Format" (Google), applied here as **Opportunity Knowledge Folders** —
> one folder per opportunity, agent-readable.
