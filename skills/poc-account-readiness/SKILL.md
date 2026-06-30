---
name: poc-readiness
description: >-
  Check whether a VTEX POC / demo account is ready to show, by cross-referencing
  the Rocketlane opportunity's POC/Demo tasks with the live VTEX account
  configuration. Use whenever a Solution Engineer asks "is the POC ready for the
  Accor demo", "what's missing in the demo account", "are we good to present the
  POC for client Y", "check the demo environment for Z", or wants a pre-demo
  checklist that reflects what's actually configured in the account rather than
  just what's checked off in the project. Produces a pass/fail/warning checklist
  by area with what's missing and where to fix it. READ-ONLY across Rocketlane
  and the VTEX account.
---

# POC / demo readiness check

## Purpose

In pre-sales, an SE often has to stand up a POC or demo account and make sure it
actually shows well before presenting to the prospect. A project task can be
checked off while the account itself isn't presentable — or vice versa. This
skill checks both: the **Rocketlane POC/Demo tasks** and the **live account**
(catalog populated, payments testable, storefront/channels configured), so the
SE walks into the demo confident.

Read-only across all sources. Inspect and read; never modify anything.

## Inputs to gather

- **The opportunity.** Name or ID. Read/confirm the VTEX **demo or POC account**
  name to inspect (ask the SE — pre-sales accounts are often a dedicated demo
  account, not a production one).

## Workflow

1. **Project check.** `get_projects` (all fields) for stage and current phase
   (expect Demo or POC). Then `get_tasks` filtered by `projectId`, focusing on
   the **Demo**, **POC**, and **Any specific requirement covered** tasks — their
   Status (To do / In progress / Completed / Blocked / on hold) and any `Link`.

2. **Live account checks.** Using the account name, verify what makes a demo
   credible:
   - **Catalog** — `vtex_get_catalog_summary`, `vtex_get_category_tree`,
     `vtex_get_brands`: enough active products and a sensible category tree (an
     empty catalog kills a demo).
   - **Channels / storefront** — `vtex_get_sales_channels` / `vtex_get_affiliates`:
     the right trade policy active for the business model being demoed (critical
     for a B2B demo).
   - **Payments** — `vtex_get_payment_affiliations`, `vtex_get_payment_rules`,
     `vtex_get_installments`: at least a test payment path so checkout completes.
   - **Logistics** — `vtex_get_shipping_policies`, `vtex_get_warehouses`,
     `vtex_get_docks`: shipping resolves so the cart/checkout works.
   - Any **specific requirement** the prospect asked to see (map to the relevant
     inspector tool — promotions, subscriptions, pickup points, etc.).

3. **Match against a model-specific checklist.** Weight by what's being demoed:
   B2C → catalog + checkout + promotions; B2B → trade policy + price tables +
   org/quote flow; marketplace → seller/affiliate setup. Consult vtex-expert /
   Atlas if unsure of platform expectations, but keep the check grounded in
   account data.

## Output format

A checklist grouped by area, each item ✅ ready / ⚠️ check / ❌ blocker:

```
## POC readiness — [opportunity] · account [account] · [date]

Overall: 🔴 Not demo-ready / 🟡 Demo-ready with caveats / 🟢 Demo-ready

Catalog
  ✅ 320 active SKUs across 12 categories
  ⚠️ Several products without images

Checkout (payments + logistics)
  ❌ No active test payment affiliation — checkout won't complete

Channels
  ✅ B2B trade policy active

Specific requirements
  ⚠️ Quote flow requested by prospect — not configured

Project (Rocketlane)
  ⚠️ "POC" task still In progress; "Any specific requirement covered" To do

### Fix before the demo
1. ...
### Nice to polish
1. ...
```

Be explicit that ❌ items would break the demo and ⚠️ items need a judgment call.
Tie each gap to where it's fixed.

## Notes

- If the account can't be inspected, run the task-only check and clearly flag
  that live verification was skipped — never assume an area is ready without
  evidence.
- A demo only needs to be credible, not complete — focus on what the prospect
  will actually click through, not exhaustive production-readiness.

---

## Shared standards (source of truth)

This skill is grounded in the VTEX SE governance standards in [`../../standards/`](../../standards/). When this skill and a standard disagree, **the standard wins** — those files are the single source of truth that the Claude Project and Cowork consume too.

- `SE_Governance_Rubric.md` — health flags
- `SE_OKF_Standard.md` — where POC/Demo docs live in the OKF
