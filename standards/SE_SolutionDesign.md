# SE Solution Design — Template, VTEX Capability Reference & Document Generation
 
> **Status: v2 — Active.**
> This file drives both the content structure and the `.docx` file generation workflow.
> When real Solution Design examples are available, add them as `SD_Example_[CustomerName].md` and reference them here to calibrate depth and format.
 
---
 
## How to use this file
 
When an SE provides customer input — an RFP, partner proposal, architecture doc, or Discovery notes — do the following **in order**:
 
1. **Read this file** to understand the content structure and VTEX capability reference.
2. **Read the `docx` SKILL.md** before writing any code (`/mnt/skills/public/docx/SKILL.md`).
3. **Generate the content** by mapping requirements to VTEX capabilities using the structure below.
4. **Produce the `.docx` file** using the `docx` npm library via `bash_tool`, following the technical patterns in the SKILL.
5. **Present the file** using `present_files` so the user can download it.
> Do not output the Solution Design as markdown in chat. The deliverable is always a `.docx` file.
 
---
 
## Input format (what to provide)
 
Provide as much of the following as available before requesting a Solution Design:
 
```
Customer: [name]
Commerce model: [B2C / B2B / Marketplace / D2C / hybrid]
Vertical: [retail / pharma / fashion / electronics / etc.]
Region: [MX / US / LATAM / EMEA / etc.]
System Integrator: [name or TBD]
 
Key requirements (from RFP, Discovery, or partner proposal):
1. [requirement]
2. [requirement]
...
 
Current tech stack:
- ERP: [SAP / Oracle / custom / unknown]
- POS: [if applicable]
- Other integrations: [WMS, PIM, Payment, Loyalty, Fiscal, etc.]
 
Scope constraints:
- [e.g. MVP only — Phase 1, exclude physical stores, B2C out of scope for now]
 
Attachments: [RFP PDF / partner proposal / architecture diagram / Discovery notes]
```
 
If some fields are missing, proceed with what is available and flag unknowns as Open Questions in Section 4.
 
---
 
## Solution Design content structure
 
The document follows the **6-section VTEX PS template**. Generate all sections unless the user explicitly constrains scope (e.g. MVP-only, architecture-only). Keep content grounded in the provided input — do not add capabilities or phases that were not requested or implied.
 
### Section 1 — Project Context
 
A brief executive summary: who the customer is, their business model, motivation to adopt VTEX, and the context of this proposal.
 
Metadata table to populate:
 
| Field | Value |
|---|---|
| Industry / Vertical | |
| Business model | |
| Target markets | |
| Reason for replatforming | |
| Current platform | |
| Estimated go-live date | |
 
### Section 2 — Proposed Architecture
 
Overview of the proposed architecture. Subsections:
 
**2.1 Merchant Back Office** — Systems integrating with VTEX.
 
| System | Type | Integration owner | Data synchronized |
|---|---|---|---|
 
**2.2 Integration Layer / Middleware** — Recommended middleware/IPaaS, number and type of integrations, SI vs. client responsibilities. Reference: https://developers.vtex.com/vtex-rest-api/docs/erp-integration-guide
 
**2.3 VTEX Core Services** — Only list modules relevant to this customer. Draw from the capability quick-reference below.
 
**2.4 Third-party Solutions** — External apps that complement or replace native VTEX functionality.
 
| Solution | Function | Relationship with VTEX | Owner |
|---|---|---|---|
 
**2.5 Sales Channels** — Frontend (framework, key capabilities, owner), Mobile App (if applicable), Physical Stores (if applicable), External Marketplaces (if applicable).
 
### Section 3 — Project Roadmap
 
Implementation phases with target dates and capabilities per phase. If only Phase 1 / MVP is in scope, describe only that phase and note that subsequent phases are out of scope for this document.
 
Each phase includes:
- Target date
- Scope summary
- Core capabilities
- Integrations delivered in this phase
- (Phase 2+) Future expansion capabilities
### Section 4 — Project Risks
 
Risk table covering: Contract, System Integrator, Frontend / Technology, Logistics, Loyalty / CRM, Payment methods, ERP / POS, and any project-specific risks. Each row includes: area, description + mitigation, level (High / Medium / Low), owner (VTEX / SI / Client).
 
Derive risks from what is known about the customer's stack and constraints. Flag any area where information is insufficient as a risk in itself.
 
### Section 5 — Project Specifics
 
Customer- and market-specific details the implementation team must know:
 
- **5.1 Payment Methods** — PSPs, wallets, BNPL, local payment methods, integration considerations.
- **5.2 Logistics & Carriers** — Shipping calculation flow, main carriers, business rules.
- **5.3 Loyalty Program** — Loyalty program mechanics, system, VTEX integration points.
- **5.4 Fiscal / Regulatory** — Invoicing requirements (e.g. CFDI for Mexico), checkout data requirements, privacy regulations (e.g. LFPDPPP, GDPR).
- **5.5 Other** — Any other relevant particularity not covered above.
### Section 6 — Next Steps
 
Immediate actions post-handover from Pre-Sales to Professional Services.
 
| Action | Owner | Due date |
|---|---|---|
| Discovery kickoff with client | PS Lead | DD/MM |
| SI onboarding on VTEX | SI Lead | DD/MM |
| VTEX account provisioning | VTEX Ops | DD/MM |
 
---
 
## Document generation — technical instructions
 
### File naming convention
```
VTEX_Solution_Design_[CustomerName]_[Scope].docx
```
Examples:
- `VTEX_Solution_Design_Farmamigo_MVP_B2B.docx`
- `VTEX_Solution_Design_AcmeCorp_Phase1.docx`
### Output path
Write the final file to `/mnt/user-data/outputs/`.
 
### Generation workflow
 
```
1. Plan all content (sections 1–7) based on the input provided.
2. Read /mnt/skills/public/docx/SKILL.md — mandatory before writing any code.
3. Write a Node.js script at /home/claude/generate_solution_design.js using the `docx` npm library.
4. Run: node /home/claude/generate_solution_design.js
5. Validate: python /mnt/skills/public/docx/scripts/office/validate.py [output_path]
6. If validation fails: unpack → fix XML → repack.
7. present_files([output_path])
```
 
### Document formatting standards
 
Apply the following consistently:
 
- **Page size:** Letter (12240 × 15840 DXA), 1-inch margins (1440 DXA each side). Content width = 9360 DXA.
- **Default font:** Arial, 12pt (size: 24 in half-points).
- **Cover page:** Client name as H1, subtitle "Solution Design", metadata table (version, date, prepared by, SI). No page number on cover.
- **Section headings:** H1 for numbered sections (e.g. "1. Project Context"), H2 for subsections.
- **Tables:** Always set `width: { size: 9360, type: WidthType.DXA }`, matching `columnWidths` array. Use `ShadingType.CLEAR` for header shading. Cell margins: `{ top: 80, bottom: 80, left: 120, right: 120 }`. Border color: `"CCCCCC"`.
- **Header rows:** Fill color `"D5E8F0"` (light blue), bold text.
- **Bullet lists:** Always use `LevelFormat.BULLET` with numbering config — never unicode bullet characters.
- **Page breaks:** Insert a `PageBreak` inside a `Paragraph` between the cover and the first section. Never standalone.
- **Headers/footers:** Footer with "Confidential — VTEX Inc." left-aligned and page number right-aligned. Use tab stops, not tables, for two-column footer layout.
- **Confidentiality note:** Include at the end of the document: *"This document is a Pre-Sales to Professional Services handover and must be complemented with the Discovery output."*
### Scope discipline
 
- **Never reference phases, capabilities, or roadmap items outside the agreed scope.**
- If the user specifies MVP / Phase 1 only: Phase 2 and beyond must not appear anywhere in the document — not in section titles, roadmap tables, or architecture descriptions.
- If a requirement cannot be mapped to VTEX natively, document it in Section 5 (Project Specifics) or flag it as a risk in Section 4. Do not silently omit it.
- If a field has no data, write "To be defined in Discovery" — never leave template placeholders like `[xxx]` in the output.
---
 
## VTEX capability quick-reference
 
| Capability | Description | Typical use case |
|---|---|---|
| **B2C Commerce** | Multi-tenant SaaS storefront + OMS | Standard DTC or retail |
| **B2B Commerce / B2B Suite** | Price tables, cost centers, quotes, purchase orders, punch-out, B2B Organizations | Wholesale, distributors, pharma |
| **Customer Credit** | Credit lines and promissory (pagaré) management | B2B deferred payment |
| **Marketplace** | Seller management, commission rules, split payment | Multi-seller / platform model |
| **FastStore** | Headless storefront with VTEX native APIs | Performance-first / custom UI |
| **VTEX IO** | Low-code app framework for custom extensions | Custom apps, integrations |
| **OMS** | Order management, multi-warehouse, franchise accounts | Complex fulfillment |
| **Intelligent Search** | Native search & merchandising | Search, recommendations |
| **Promotions & Loyalty** | Promotion rules, gift cards, coupons | Marketing / retention |
| **Checkout** | Native checkout, customizable via IO | All commerce models |
| **Franchise Accounts** | Regional pricing and inventory via sub-accounts | Multi-region / franchise / branches |
| **VTEX Shipping Network** | Carrier integrations + fulfillment orchestration | Last-mile complexity |
| **Quickorder** | Bulk order entry via SKU list or spreadsheet | B2B repeat purchases |
| **Weni (VTEX)** | Conversational commerce via WhatsApp / chat | Service + commerce |
| **VTEX Sales App** | In-store / assisted selling POS | Omnichannel / retail |
 
---
 
## Agent rules
 
- **Always produce a `.docx` file.** Do not output the Solution Design as chat text or markdown.
- **Read the `docx` SKILL.md before writing any generation code.** This is mandatory.
- **Ground every section in the provided input.** Do not invent requirements or add VTEX capabilities that were not requested or implied.
- **Respect scope constraints strictly.** If the user says "MVP only" or "exclude Phase 2", enforce that throughout every section of the document.
- **Flag gaps honestly.** If a requirement cannot be mapped natively, say so in Section 5 or 4. Never silently mark it as covered.
- **Replace all template placeholders.** Outputs must not contain `[xxx]`, `[DD/MM]`, or similar. Use "To be defined in Discovery" when data is genuinely unknown.
- **Use SD_Example files to calibrate depth.** If a `SD_Example_[CustomerName].md` file is available in the Project knowledge, use it as a reference for the expected level of detail and formatting.
- **Write all document content in English** — section titles, table headers, body text, metadata fields, and footers.