---
name: discovery-brief
description: >-
  Build a discovery / solution-design brief for a VTEX Solution Engineering
  opportunity by combining the Rocketlane project with the Atlas architecture
  knowledge base and, when the prospect already has a VTEX account, its live
  configuration. Use whenever an SE says "prep the discovery brief for client Y",
  "build the solution design doc input for the Accor deal", "what do I need for
  the discovery meeting with Z", "help me frame the architecture for this
  opportunity", or is moving an opportunity into Discovery / Solution Design and
  wants the business context plus recommended architecture in one document.
  READ-ONLY across all sources; produces a .docx brief.
---

# Discovery / solution-design brief

## Purpose

As an SE moves an opportunity into **Discovery** and **Solution Design**, they
need the business context and a recommended VTEX architecture pulled together.
This skill assembles the opportunity's commercial context from Rocketlane, layers
in Atlas best practices, and — if the prospect is an existing VTEX account —
inspects what's already configured, producing a brief that feeds the discovery
meeting and the Solution Design Document.

All sources read-only. Inspect and read; never modify the account, the project,
or Atlas.

## Inputs to gather

- **The opportunity.** Name or ID in Rocketlane.
- **Existing VTEX account?** Ask whether the prospect already has a VTEX account
  to inspect. Many pre-sales opportunities are net-new (no account yet) — in that
  case skip live inspection and build from project + Atlas context.

## Workflow

1. **Read the opportunity (commercial context).** `get_projects` with
   `includeAllFields: true`. Extract: client/company, business model (from the
   name / record type: B2C, B2B, MKP…), `Opportunity Stage`, `Sales Region` /
   `Macro Region`, `ACV`, `Estimated GMV`, key dates, and the team. Read the
   playbook tasks (`get_tasks`) to see what's already captured (Discovery
   Document, RFP, requirements) and link any existing docs via the task `Link`
   field.

2. **Pull Atlas context.** Use the Atlas `retrieve_context` tool for architecture
   patterns relevant to the deal (e.g. "B2B trade policy architecture",
   "marketplace seller setup", "regionalization", "headless/FastStore", "ERP
   integration"). If a solution architecture document already exists for this
   client, fetch it with `get_architecture`. Ground recommendations in Atlas
   rather than inventing them.

3. **Inspect the live account (only if one exists).** Using the account name,
   call the relevant VTEX account-inspector tools — `vtex_get_account_info`,
   `vtex_get_catalog_summary`, `vtex_get_sales_channels`, `vtex_get_affiliates`,
   `vtex_get_payment_affiliations`, `vtex_get_shipping_policies`,
   `vtex_get_warehouses` — to capture the current state and what an expansion
   would build on. For B2B also check price tables; for marketplace check
   affiliates/sellers.

4. **Synthesize.** Cross the business model and requirements against
   Atlas-recommended architecture (and current account state, if any): the
   recommended approach, key decisions/trade-offs, integration points, and the
   open questions to resolve in discovery.

## Output: the .docx brief

Read the `docx` skill, then build `Discovery Brief - <Client>.docx`:

1. **Opportunity & context** — client, business model, region, ACV/GMV, stage,
   key dates, what's known so far.
2. **Requirements captured** — from the Discovery Document / RFP tasks, if any.
3. **Recommended architecture** — the VTEX approach for this business model,
   grounded in Atlas; key decisions and trade-offs.
4. **(If existing account) Current state** — concise read of what's configured.
5. **Open questions for discovery** — what to confirm with the customer/partner.
6. **Next steps** — toward Solution Design and the PS proposal/handover.

Keep it skimmable. Clearly mark which content is live-from-account vs. from the
project vs. recommended, so nothing reads as fabricated.

## Notes

- Don't assume a live account exists — most pre-sales deals are net-new. When
  there's no account, say so and lean on Atlas + requirements.
- Tailor depth to the business model: B2B → trade policies, price tables, org
  setup; B2C → catalog, payments, promotions; marketplace → seller/affiliate
  model; regionalized → warehouses, docks, shipping.
- For deep platform mechanics or architecture trade-offs, the vtex-expert and
  vtex-architect skills are complementary — use them to enrich recommendations.
