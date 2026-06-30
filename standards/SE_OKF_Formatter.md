# SE_OKF_Formatter — Agent Playbook
> Operational instructions for the SE Co-pilot to onboard any opportunity into the OKF structure.
> This file is read by the agent, not maintained by hand.
 
---
 
## Purpose
 
When an SE starts working a new opportunity, this playbook tells the agent exactly what to do to:
1. Create the root OKF folder and internal structure in Google Drive
2. Pull canonical data from Rocketlane
3. Generate the `00_index.md` entry point
4. Process any existing documents into `.md` renditions
5. Keep the index current as new files arrive
---
 
## Trigger conditions
 
Run this playbook when:
- An SE says "set up OKF for [customer]" or "homologate this folder to OKF"
- An SE shares a Drive folder link that has no OKF subfolders yet
- An SE shares a Drive folder link that has files but no `00_index.md`
- A folder exists under the main opps folder without the standard subfolder structure
---
 
## Versioning convention (CRITICAL — read before creating any file)
 
The agent cannot edit existing files in Google Drive — every write creates a new file. To avoid stale duplicates cluttering folders, **all agent-generated files use a `_v{n}` suffix** that increments on every update.
 
### Rules
 
```
FIRST TIME a file is created:
  filename → {basename}_v1.{ext}
  examples → 00_index_v1.md / rfp_20260625_v1.md / architecture_20260625_v1.md
 
EVERY SUBSEQUENT UPDATE to the same file:
  1. Search the folder for existing versions of that basename.
  2. Identify the highest version number present (e.g. _v2).
  3. Create the new file with n+1 (e.g. _v3).
  4. Inform the SE which old versions can be deleted manually.
 
NEVER overwrite or silently create a duplicate without incrementing the version.
```
 
### Version search protocol
 
Before creating any agent-generated file, always:
1. Call `Google Drive: search_files` on the parent folder.
2. Filter results by basename (e.g. `00_index`).
3. Find the max `_v{n}` present → create `_v{n+1}`.
4. If no versioned file exists → create `_v1`.
### What gets versioned
 
All agent-generated files follow this convention:
 
| File | Versioning example |
|------|--------------------|
| `00_index.md` | `00_index_v1.md` → `00_index_v2.md` → ... |
| `.md` renditions | `rfp_20260625_v1.md` → `rfp_20260625_v2.md` → ... |
| `architecture_YYYYMMDD.md` | `architecture_20260625_v1.md` → ... |
 
### What does NOT get versioned
 
Original files uploaded by the SE (`.pdf`, `.docx`, `.xlsx`, `.excalidraw`) are **never renamed or versioned by the agent** — they are the SE's files and must stay as-is.
 
### Communicating versions to the SE
 
After creating a new versioned file, always tell the SE:
```
Created: 00_index_v2.md
Previous version: 00_index_v1.md — safe to delete manually from Drive.
```
 
---
 
## Step 0 — Determine the starting point
 
Before doing anything else, assess the situation:
 
```
CASE A — No folder exists yet:
  → The SE provides a parent Drive folder (e.g. main opps folder).
  → You must create the root folder AND all subfolders.
  → Go to Step 0A.
 
CASE B — Root folder exists, no subfolders:
  → The SE shares a Drive folder link.
  → You must create the subfolders inside it.
  → Go to Step 1 (Rocketlane), then Step 2 (subfolders).
 
CASE C — Root folder exists, subfolders exist, no 00_index_v{n}.md:
  → Scan what's already there, then go directly to Step 3.
 
CASE D — Everything exists, 00_index_v{n}.md is stale:
  → Read current index, then go to Step 5 (update only).
```
 
---
 
## Step 0A — Create the root folder (CASE A only)
 
The SE must provide:
- **Parent folder** in Drive where the root folder will be created (ask for the Drive link if not given)
- **Customer name**
- **Rocketlane opp_id** (if known)
### Root folder naming
 
```
IF opp_id is known and confirmed in Rocketlane:
  folder name → "{OPP_ID} - {Customer}"
  example    → "1209838 - Promoopcion"
 
IF opp_id is NOT yet known or Rocketlane has no record:
  folder name → "XXXXXXX - {Customer}"
  example    → "XXXXXXX - AcmeFashion"
  note in index → "⚠️ opp_id pending — update folder name and frontmatter when Rocketlane ID is assigned"
```
 
> Note: the root folder itself is never versioned — only files inside it are.
 
Create the root folder using `Google Drive: create_file` with:
- `mimeType: application/vnd.google-apps.folder`
- `parentId`: the SE-provided parent folder ID
- `title`: the folder name as defined above
Store the returned folder ID — all subfolders go inside it.
 
---
 
## Step 1 — Resolve the opportunity in Rocketlane
 
```
1. Extract the opp_id from the folder name prefix (the numeric part before " - ").
   - If the prefix is "XXXXXXX", skip the Rocketlane query and go to Step 1B.
 
2. Call Rocketlane get_projects with:
   - projectId: {opp_id}
   - includeAllFields: true
 
3. Extract and store:
   - projectName
   - customer.companyName
   - owner.emailId
   - status.label
   - currentPhases[0].phaseName
   - fields: ACV, Estimated GMV, Sales Region, Macro Region, Opportunity ID (Salesforce), Opportunity Stage, Opp Closed Date
   - startDate, dueDate
```
 
### Step 1B — Rocketlane ID not yet assigned
 
Use when the folder is named `XXXXXXX - {Customer}` or when a Rocketlane query returns no result.
 
```
- Set opp_id = "XXXXXXX" in the frontmatter
- Set all Rocketlane fields to "— (pending Rocketlane assignment)"
- Add warning banner at the top of the index:
  "> ⚠️ **opp_id not yet assigned.** Once the project is created in Rocketlane:
  > 1. Rename this folder to "{REAL_OPP_ID} - {Customer}"
  > 2. Update `opp_id` in the frontmatter
  > 3. Re-run the OKF formatter to populate the Rocketlane snapshot."
- Do NOT block folder creation or index generation — proceed with what is known.
```
 
---
 
## Step 2 — Create the subfolder structure in Google Drive
 
Check if each subfolder already exists before creating it. Never duplicate.
 
```
Required subfolders (in order):
  01_discovery/
  02_rfp/
  03_demo/
  04_additional/
  05_solution_design/
```
 
Use `Google Drive: create_file` with `mimeType: application/vnd.google-apps.folder` and `parentId` of the opportunity root folder.
 
Store all returned subfolder IDs — you will need them to place files correctly.
 
---
 
## Step 3 — Generate 00_index_v{n}.md
 
Before creating, search the root folder for any existing `00_index_v*.md` files and determine the next version number.
 
### Template — Rocketlane data available
 
```markdown
---
opp_id: "{OPP_ID}"
customer: "{Customer}"
vertical: "{vertical}"        # fill if known, leave blank if not
region: "{Macro Region}"
index_version: v{n}
---
 
# {OPP_ID} - {Customer}
 
> Agent-generated entry point. Do not edit manually.
> Canonical state (stage / owner / ACV / last activity) is sourced from Rocketlane via `opp_id`.
> Last updated: {YYYY-MM-DD}
> **This is the current version: 00_index_v{n}.md** — previous versions are safe to delete.
 
---
 
## Opportunity snapshot (from Rocketlane)
 
| Field | Value |
|-------|-------|
| **Project name** | {projectName} |
| **Opportunity ID** | {Salesforce opp ID} |
| **Record type** | {Opportunity Record Type} |
| **Stage** | {Opportunity Stage} |
| **Status** | {status.label} |
| **Current phase** | {currentPhases[0].phaseName} |
| **Owner** | {owner.emailId} |
| **ACV** | ${ACV} USD |
| **Estimated GMV** | ${Estimated GMV} USD |
| **Sales Region** | {Sales Region} |
| **Macro Region** | {Macro Region} |
| **Opportunity Created** | {startDate} |
| **Opp Closed Date** | {dueDate} |
 
---
 
## Folder structure
 
​```
{OPP_ID} - {Customer}/
├── 00_index_v{n}.md               ← this file (agent-generated, current version)
├── 01_discovery/
│   └── {files or (empty)}
├── 02_rfp/
│   └── {files or (empty)}
├── 03_demo/
│   └── {files or (empty)}
├── 04_additional/
│   └── {files or (empty)}
└── 05_solution_design/
    └── {files or (empty)}
​```
 
---
 
## File inventory
 
| File | Version | Type | Folder | Notes |
|------|---------|------|--------|-------|
{file_inventory_rows}
 
---
 
## Key context for the agent
 
- {1-3 bullet points summarizing what is known about the opportunity so far}
---
 
## Naming convention
 
Agent-generated files: `{descriptor}_YYYYMMDD_v{n}.{ext}` — version increments on every update.
Original files (SE-uploaded): `{descriptor}_YYYYMMDD.{ext}` — never renamed by the agent.
 
**Golden rule:** original always wins. If original and rendition diverge, regenerate the rendition as v{n+1}.
 
---
 
## Notes
 
- Credentials are never stored inline — reference the SE vault in 1Password.
- This index is regenerated by the SE Co-pilot agent when new files are added to the folder.
- To clean up old versions, delete any `_v{n-1}` files manually from Drive.
```
 
### Template delta — opp_id = XXXXXXX (Rocketlane pending)
 
Same template as above, with these differences:
 
```
- opp_id frontmatter → "XXXXXXX"
- All Rocketlane snapshot fields → "— (pending Rocketlane assignment)"
- Add warning banner immediately after the header (see Step 1B)
- Key context section → note that Rocketlane data is pending; summarize only what is known from documents
```
 
### Rules for the index
 
- `opp_id`, `customer`, `region` come from Rocketlane — never ask the SE to fill these (exception: XXXXXXX case)
- `vertical` is NOT in Rocketlane — infer from documents or ask the SE once
- If `Opp Closed Date` is in the past, append `⚠️ (fecha vencida — validar actualización)`
- `file_inventory_rows` must list every file currently in the folder — scan each subfolder before writing
- Include a `Version` column in the file inventory table to track which version of each rendition is current
- `Key context`: write 1–3 bullets on stack, project type, and known risks — only if documents have been read
 
---
 
## Step 4 — Process existing documents into .md renditions
 
After the index is created, scan each subfolder for files without a current `.md` rendition.
 
### Decision table
 
| File type | Action |
|-----------|--------|
| `.pdf` | Read via `Google Drive: read_file_content`, generate `.md` rendition as `{basename}_v1.md`, upload to same subfolder |
| `.docx` | Read via `Google Drive: read_file_content`, generate `.md` rendition as `{basename}_v1.md`, upload to same subfolder |
| `.xlsx` / `.csv` | Read content, generate `.md` rendition as structured tables |
| `.excalidraw` | Cannot be read — generate `.md` rendition from SE description or skip with `(rendition pending — diagram requires SE input)` |
| `.md` with `_v{n}` suffix | Already a rendition — check if paired original has changed; if not, skip |
| Google Doc / Slides / Sheet | Read via `read_file_content`, generate `.md` rendition |
 
### Rendition naming rule
 
```
Original (SE file, never touched):   rfp_20260101.pdf
First rendition:                     rfp_20260101_v1.md
Updated rendition:                   rfp_20260101_v2.md
 
Original:                            architecture_20260101.excalidraw
First rendition:                     architecture_20260101_v1.md
```
 
### Rendition content rules by folder
 
| Folder | Rendition must include |
|--------|----------------------|
| `01_discovery` | Full transcript or summary structured with headers per topic discussed |
| `02_rfp` | All requirements as structured tables; mandatory closing section **"Notas SE"** with VTEX capability mapping and risk flags |
| `03_demo` | Demo script or transcript by section; flag open objections or follow-up commitments |
| `04_additional` | Full document content; mandatory closing section **"Notas SE"** with implications for the VTEX proposal |
| `05_solution_design` | Architecture described layer by layer; list all components, integrations, and open decisions |
 
---
 
## Step 5 — Update the index after every new file
 
Every time a new file is added to any subfolder:
1. Search the root folder for the current `00_index_v{n}.md`
2. Read it
3. Create `00_index_v{n+1}.md` with:
   - Updated **File inventory** table (add new file + version)
   - Updated **Folder structure** tree
   - Refreshed **Key context** if new material changes the picture
   - Updated `Last updated` date and `index_version`
4. Inform the SE: *"Created 00_index_v{n+1}.md — previous version v{n} is safe to delete."*
 
---
 
## XXXXXXX → real ID promotion
 
When the SE confirms that a Rocketlane ID has been assigned to a previously placeholder folder:
 
```
1. Rename the root Drive folder from "XXXXXXX - {Customer}" to "{REAL_OPP_ID} - {Customer}"
   → Note: Drive rename requires the SE to do it manually.
   → If not yet done, instruct the SE and wait for confirmation.
2. Re-run Step 1 with the real opp_id to pull Rocketlane data.
3. Create 00_index_v{n+1}.md:
   - frontmatter opp_id → real ID
   - Populate all Rocketlane snapshot fields
   - Remove the ⚠️ warning banner
   - Update Last updated date and index_version
4. Confirm to the SE that the OKF is now fully linked to Rocketlane.
```
 
---
 
## Opp Closed Date flag logic
 
```
IF Opp Closed Date < today:
  → append "⚠️ (fecha vencida — validar actualización)" to the date in snapshot table
  → add note in Key context: "Opp Closed Date is in the past — SE should confirm updated timeline with AE."
```
 
---
 
## Worked reference
 
The canonical example of a correctly formatted OKF folder is:
 
```
/main opps folder/1209838 - Promoopcion/
```
 
Files present in this example:
- `00_index_v1.md`, `00_index_v2.md`, `00_index_v3.md` — index versions (latest is current)
- `02_rfp/Brief necesidades Promoopcion.pdf` + `rfp_20260625_v1.md`
- `04_additional/Frontend Headless PromoOpcion B2B.pdf` + `20251207_propuesta_partner_experimentality_v1.md`
- `05_solution_design/architecture_20260625.excalidraw` + `architecture_20260625_v1.md`
 
---
 
## Error handling
 
| Situation | Action |
|-----------|--------|
| No parent Drive folder provided (CASE A) | Ask SE for the Drive link of the parent folder before proceeding |
| Rocketlane project not found | Set opp_id = XXXXXXX, apply Step 1B, proceed with folder creation |
| opp_id is XXXXXXX and SE claims it exists in Rocketlane | Ask SE to verify the project ID directly in Rocketlane and re-provide it |
| File unreadable (corrupted, image-only PDF) | Note in index as `(rendition pending — file unreadable)` and flag to SE |
| Subfolder already exists | Skip creation, do not duplicate |
| `00_index_v{n}.md` already exists | Read it first, create v{n+1} — never overwrite |
| No `_v{n}` found for a basename | Treat as first time → create `_v1` |
| Drive write permission denied | Flag to SE — folder may not be shared with the agent's account |