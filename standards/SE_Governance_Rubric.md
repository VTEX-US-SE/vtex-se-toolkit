# SE Governance Rubric — Opportunity Health by Stage
 
## Stage taxonomy (Rocketlane phases)
Discovery Meeting → RFP → Demo → POC → Solution Design → Closing
 
---
 
## Health criteria per stage
 
### Discovery Meeting
🟢 **Healthy:** Discovery Document exists, Opportunity Folder created in Drive, activity within the last 7 days.
🟡 **At risk:** Missing one artifact OR no activity for 7–14 days.
🔴 **Critical:** No Discovery Document after 14 days in stage, OR no activity for more than 14 days.
 
### RFP (Request for Proposal)
🟢 **Healthy:** RFP received and under analysis, response in progress, activity within the last 7 days.
🟡 **At risk:** RFP received but no task assigned to it, OR no activity for 7–14 days.
🔴 **Critical:** RFP with no response after 14 days, OR no activity for more than 21 days.
 
### Demo
🟢 **Healthy:** Demo scheduled or completed, "Any specific requirement covered" task documented.
🟡 **At risk:** Demo completed but no follow-up documented, OR no activity for 5–10 days.
🔴 **Critical:** No demo scheduled after 10 days in stage, OR no activity for more than 14 days.
 
### POC
🟢 **Healthy:** POC actively in execution with tasks in progress, activity within the last 5 days.
🟡 **At risk:** POC started but progress stalled, OR no activity for 5–10 days.
🔴 **Critical:** No POC progress for more than 10 days, critical tasks blocked, OR no activity for more than 14 days.
 
### Solution Design
🟢 **Healthy:** Solution Design Document in progress, Architecture task started, activity within the last 7 days.
🟡 **At risk:** Architecture or PS proposal missing, OR no activity for 7–14 days.
🔴 **Critical:** No Solution Design Document after 14 days in stage, OR no activity for more than 21 days.
 
### Closing
🟢 **Healthy:** PS proposal sent, KT Document complete, Handover to PS scheduled.
🟡 **At risk:** One or more closing tasks pending without a due date, OR no activity for 5–10 days.
🔴 **Critical:** No movement in Closing for more than 14 days, OR due date passed without completion.
 
---
 
## Universal red flags (any stage)
- Due date passed and project still active → update date and log reason immediately.
- Status "Backlog" with progress > 50% → status inconsistency, needs update.
- ACV not registered on opportunities older than 30 days → data quality issue.
- No activity within the stage threshold defined above.
---
 
## Notes for the agent
- Pull activity data from Rocketlane task updates and project `updatedAt` timestamps.
- When a flag is raised, always state the specific data point that triggered it (e.g. "last activity: 18 days ago", "Discovery Document task: not started").
- If data is missing or a field is empty in Rocketlane, flag it as a data quality issue rather than assuming health.