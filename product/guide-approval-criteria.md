# Guide Approval Criteria

**Established:** July 13, 2026
**Owner:** Christine Smith
**Status:** Active rubric. Apply to every guide before release.

---

## The Six-Criterion Rubric

A guide must pass all six criteria before it ships. Each criterion is a binary pass/fail at time of review. Partial credit is not available -- if a criterion has an open gap, the guide is not approved.

### Criterion 1: Content Completeness

The written guide covers the full realistic scope of the life phase, not just the most obvious sub-questions. A guide that addresses the first three questions a user would Google but misses the fourth (the one that actually costs money to get wrong) fails this criterion.

**How to evaluate:** List the hardest sub-questions a real person in this life phase faces. Confirm the guide addresses each one with substance. "We mention it" is not the same as "we cover it."

---

### Criterion 2: Competitive Benchmark

The guide has been checked against the best existing tools and resources covering the same life phase, with real synthesized findings, not assumed coverage.

**How to evaluate:** Identify the two or three best existing resources for this life phase (a competing tool, a well-regarded book, a category-defining blog/publication). Compare scope and depth. Document what they cover that the guide matches, what the guide adds, and what they cover that the guide does not. The benchmark check must be recorded -- "we checked" is not sufficient without a note on what was found.

---

### Criterion 3: Tool Attachment

At least one interactive tool attached to the guide is genuinely topic-specific to this life phase, not a shared core-product tool relabeled as guide value.

**How to evaluate:** Does the attached tool require inputs or produce outputs that only make sense in this life phase's context? A general emergency fund calculator shared across five guides does not pass Tool Attachment for any one of them. A QDRO readiness checker built specifically for divorce does. A tool can be shared between two related guides (e.g., the FAFSA remarriage-timing planner shared between College Planning and Blended Families) and still pass, provided it is specific to the scenario both guides address.

---

### Criterion 4: Differentiation

The guide articulates clearly what Krovos does better than the best existing alternative for this life phase, not just what Krovos also does.

**How to evaluate:** Can you write one sentence that a user would genuinely find persuasive? "Krovos covers this topic" is not differentiation. "Krovos is the only tool that models what happens to a QDRO if a pension plan administrator takes 14 months to process" is differentiation.

---

### Criterion 5: Overlap Check

The guide has been checked against adjacent guides for two things: gaps (life-phase moments that fall between two guides and are addressed by neither) and intentional overlap (moments correctly addressed by more than one guide because the user may encounter them in either context).

**How to evaluate:** Map the life phase against the adjacent guides on either side. Name any genuine gaps. Name any overlapping moments and confirm they are handled consistently, not contradictorily, across guides.

---

### Criterion 6: Enforcement

The guide's topic-specific tool is actually gated behind guide purchase in the running application, not just documented as guide-specific.

**How to evaluate:**
1. Open the tool's route file. Search for `user_guide_purchases`, `guide_access`, `guideId`, `checkAccessAndLoad`, and any Stripe product/price check.
2. Open `proxy.ts`. Confirm the route is NOT in `isPublicPath` -- public routes are reachable by any visitor with the URL regardless of guide ownership.
3. Load the route in a browser as a non-purchasing subscriber and confirm the tool does not render in full.

A tool that is publicly accessible to any visitor with the URL is not gated, regardless of what the docs say.

---

## Enforcement Audit: July 13, 2026

### Finding

**Zero guide-specific tool routes have purchase-gate enforcement.** Every tool listed in Section 3 (Guide-Specific Tools) of `navigation-engine-tool-guide-mapping.md` is in `isPublicPath` in `proxy.ts` and contains no purchase-gate code. This is true for all 17 guides and all 37 distinct tool routes audited.

The only purchase-gate enforcement that exists anywhere in the codebase is a client-side check inside `app/life-phase-guides/[slug]/page.tsx` that gates the written guide content page itself. This check does not apply to any tool route.

**This was not discovered as a one-off for any single guide.** The pattern is uniform: every guide tool is public.

### Rubric Implication

Every guide that was previously assessed as passing Criterion 3 (Tool Attachment) on the basis of topical specificity must be reassessed against Criterion 6 (Enforcement) as failing. No guide currently passes Criterion 6.

### Full Audit Table

Audited July 13, 2026 against proxy.ts and each route's page source.

| Tool | Guide | Route | Purchase-gate | Auth/subscription required |
|---|---|---|---|---|
| Early Career Decision Engine | Early Career Starter Kit | /early-career | NO | NO |
| Finance Structure Comparison | Wedding and Joining Finances | /finance-structure | NO | NO |
| Authorization-Gap Bridge Planner | Immigration and Finances | /auth-gap | NO | NO |
| Path-to-Residency Cost Planner | Immigration and Finances | /residency-costs | NO | NO |
| Credit-Building-From-Zero | Immigration and Finances | /credit-building | NO | NO |
| Parental Leave Navigator | Pregnancy, Baby and Family Leave | /parental-leave-navigator | NO | NO |
| Childcare Bubble Calculator | Pregnancy, Baby and Family Leave | /childcare-bubble | NO | NO |
| Children's Planning Hub | Pregnancy, Baby and Family Leave | /childrens-planning | NO | NO |
| Life Insurance Needs Calculator | Pregnancy, Baby and Family Leave | /life-insurance-needs | NO | NO |
| 529 Projection Tool | College Planning | /college-529 | NO | NO |
| FAFSA Remarriage-Timing Planner | College Planning / Blended Families | /fafsa-remarriage-timing | NO | NO |
| Quarterly Tax Calculator | Starting a Business | /quarterly-tax | NO | NO |
| Retirement Plan Comparison | Starting a Business | /retirement-plan-comparison | NO | NO |
| Gig Income Engine | Gig Work / Freelance | /gig-engine | NO | NO |
| Runway Calculator | Gig Work / Freelance | /runway | NO | NO |
| Emergency Fund Priority Tool | Divorce / Widowhood | /emergency-fund-priority | NO | NO |
| Single Income Budget | Divorce / Widowhood | /income-stabilization | NO | NO |
| Credit Rebuilding Timeline | Divorce / Widowhood | /credit-rebuilding | NO | NO |
| QDRO Readiness Prep | Divorce | /qdro-readiness | NO | NO |
| QDRO Tracker and Award Estimator | Divorce | /qdro-tracker | NO | NO |
| Caregiver Ledger | Caregiving | /caregiver-ledger | NO | NO |
| LTD Analyzer | Disability Insurance | /ltd-analyzer | NO | NO |
| Social Security Survivor-Benefit Optimizer | Widowhood / Retirement | /ss-survivor-optimizer | NO | NO |
| QTIP vs. Bypass Trust Decision Walkthrough | Remarriage / Estate Planning | /qtip-bypass-decision | NO | NO |
| Safe Withdrawal Rate Calculator | Retirement | /swr | NO | NO |
| CoastFI Calculator | Retirement | /coast-fi | NO | NO |
| Roth Conversion Modeling | Retirement | /roth-conversion | NO | NO |
| Withdrawal Sequencing | Retirement | /withdrawal-sequencing | NO | NO |
| Inherited IRA Distribution Strategy | Inheritance | /inherited-ira-distribution | NO | NO |
| Debt Optimizer | Early Career / Divorce / Career Transition | /debt-optimizer | NO | NO |
| Emergency Fund Calculator | Early Career / Career Transition | /emergency-fund | NO | NO |
| Credit Card Engine | Early Career | /credit-card-optimizer | NO | NO |
| Savings Vault System | Wedding / Pregnancy | /savings-vaults | NO | NO |
| Insurance Adequacy Analyzer | Multiple | /insurance-adequacy | NO | NO |
| Open Enrollment Comparison | Multiple | /open-enrollment-comparison | NO | NO |
| Credit Health Dashboard | Career Transition | /credit-health | NO | NO |
| Rent vs. Buy Calculator | Home Buying | /rent-vs-buy | NO | NO |

### What the Existing Enforcement Mechanism Actually Does

The check inside `app/life-phase-guides/[slug]/page.tsx` queries `user_guide_purchases` to determine if the user has purchased the specific guide. If not, it checks `/api/household/guide-access` for connected-subscriber inheritance. If neither check passes, the guide's written content does not render and a purchase CTA is shown instead.

This gate applies only to the narrative content of the guide itself. It has no effect on any tool route.

### Implications for Pricing Decisions

This is a factual report, not a recommendation. The relevant facts for any pricing decision are:

1. Every guide-specific tool is currently reachable by any visitor with the URL, including unauthenticated visitors with no subscription.
2. Moving any tool from "guide-gated" to "core product" in the docs would not change what the code currently does -- the tools are already effectively ungated.
3. Adding real enforcement to any tool requires: (a) removing the route from `isPublicPath` in proxy.ts, and (b) adding a purchase-gate check to the route's page file that queries `user_guide_purchases`. Neither is currently present for any tool.

Pricing decisions about what should be gated are product decisions made in chat, not in this document.

---

## How to Use This Document

- Apply the six-criterion rubric to every guide before release.
- Criterion 6 (Enforcement) requires a code check, not a docs check. The audit table above shows the current state as of July 13, 2026. Re-audit any route where enforcement has been added.
- Update this document when enforcement is added to a route, when a guide passes Criterion 6, or when the rubric criteria are revised.
- This document lives in `docs/product/guide-approval-criteria.md`. The pointer in `CLAUDE.md` Product Decisions Log is the canonical reference.

---

## Full Tool Inventory

**Audited:** July 13, 2026
**Source:** app/ directory listing, proxy.ts isPublicPath, app/tools/page.tsx CATEGORIES array, per-file Life Graph pre-fill grep

44 distinct tool routes live in the codebase. All are in `isPublicPath` in `proxy.ts` and carry no purchase-gate code. All are linked from `/tools`. Auth/subscription is not required for any of them.

"LG Pre-fill: YES" means the tool reads from `life_graph` via Supabase. Fields listed are the ones confirmed by grep at audit time -- the set may be incomplete if the tool reads through intermediate functions.

| Route | Guide(s) | Discoverability | LG Pre-fill | Auth/Sub | Purchase-gate |
|---|---|---|---|---|---|
| /auth-gap | Immigration | /tools | NO | NO | NO |
| /caregiver-ledger | Caregiving | /tools | NO | NO | NO |
| /childcare-bubble | New Parent | /tools | YES: spending_total_monthly | NO | NO |
| /childrens-planning | New Parent | /tools | YES | NO | NO |
| /coast-fi | Retirement | /tools | YES: investments_total, retirement_goals, spending_total_monthly, partner_name | NO | NO |
| /college-529 | College Planning | /tools | NO | NO | NO |
| /credit-building | Immigration | /tools | NO | NO | NO |
| /credit-card-optimizer | Core, Early Career | /tools | NO | NO | NO |
| /credit-health | Core, Career Transition | /tools | NO | NO | NO |
| /credit-rebuilding | Divorce, Widowhood | /tools | NO | NO | NO |
| /debt-optimizer | Core, Early Career, Divorce | /tools | YES | NO | NO |
| /early-career | Early Career | /tools | NO | NO | NO |
| /emergency-fund | Core, Early Career, Career Transition | /tools | YES: spending_total_monthly | NO | NO |
| /emergency-fund-priority | Divorce, Widowhood | /tools | YES: investment_data, spending_total_monthly | NO | NO |
| /fafsa-remarriage-timing | College Planning, Remarriage | /tools | YES: num_children, investments_total | NO | NO |
| /finance-structure | Newlywed | /tools | NO | NO | NO |
| /gig-engine | Gig Work | /tools | NO | NO | NO |
| /household-calendar | Core | /tools | NO | NO | NO |
| /income-stabilization | Divorce, Widowhood | /tools | YES: income_data, my_takehome | NO | NO |
| /inherited-ira-distribution | Core, Inheritance | /tools | NO | NO | NO |
| /insurance-adequacy | Core, New Parent, Disability, Retirement, Estate | /tools | YES: num_children, income_data | NO | NO |
| /life-insurance-needs | New Parent, Estate Planning | /tools | YES: investments_total, my_takehome | NO | NO |
| /ltd-analyzer | Disability | /tools | NO | NO | NO |
| /milestones | Core | /tools | YES: investments_total, retirement_age, my_takehome, spending_total_monthly | NO | NO |
| /net-worth | Core | /tools | NO | NO | NO |
| /open-enrollment-comparison | Core, New Parent, Career Transition, Retirement | /tools | YES | NO | NO |
| /parental-leave-navigator | New Parent | /tools | YES: state, my_takehome, spending_total_monthly | NO | NO |
| /paycheck-allocation | Core | /tools | YES: my_takehome, spending_total_monthly | NO | NO |
| /purchase-strategy | Core | /tools | NO | NO | NO |
| /qdro-readiness | Divorce | /tools | NO | NO | NO |
| /qdro-tracker | Divorce | /tools | YES: investments_total | NO | NO |
| /qtip-bypass-decision | Remarriage, Estate Planning | /tools | YES: state | NO | NO |
| /quarterly-tax | Starting a Business | /tools | NO | NO | NO |
| /refinance-calculator | Core, Home Buying | /tools | NO | NO | NO |
| /rent-vs-buy | Core, Home Buying | /tools | NO | NO | NO |
| /residency-costs | Immigration | /tools | YES: investment_data | NO | NO |
| /retirement-plan-comparison | Starting a Business | /tools | NO | NO | NO |
| /retirement-trajectory | Core, Retirement* | /tools | YES: investments_total, retirement_age, my_takehome, spending_total_monthly | NO | NO |
| /roth-conversion | Core, Retirement | /tools | YES: income_data, my_takehome | NO | NO |
| /runway | Gig Work | /tools | NO | NO | NO |
| /savings-vaults | Core, Newlywed, New Parent | /tools | YES | NO | NO |
| /ss-survivor-optimizer | Widowhood, Retirement* | /tools | NO | NO | NO |
| /swr | Retirement | /tools | YES: investments_total, retirement_goals, spending_total_monthly, has_partner, partner_name | NO | NO |
| /withdrawal-sequencing | Core, Retirement | /tools | YES: investments_total | NO | NO |

*Asterisk: the tool appears in the guide section of /tools/page.tsx but is not listed under that guide in navigation-engine-tool-guide-mapping.md. See discrepancies section below.

**Tools NOT in the /tools directory page** (excluded because they require auth or are not standard tools):
- `/goals` -- goals tracker, auth + active subscription required, intentionally excluded from /tools
- `/life-gaps` -- lead magnet quiz, not a tool per se

---

## Guide-by-Guide Reconciliation: Criterion 3 (Tool Attachment)

Criterion 3 asks: does the guide have at least one tool that is genuinely topic-specific and not just a shared core product tool?

**Core Product tools** (available to every user from /tools Core section, regardless of guide):
net-worth, debt-optimizer, paycheck-allocation, savings-vaults, emergency-fund, credit-health, credit-card-optimizer, purchase-strategy, refinance-calculator, rent-vs-buy, insurance-adequacy, open-enrollment-comparison, roth-conversion, withdrawal-sequencing, inherited-ira-distribution, retirement-trajectory, household-calendar, milestones

| Guide | All Tools | Core-Also | Genuinely Exclusive | Criterion 3 |
|---|---|---|---|---|
| Early Career Starter Kit | early-career, debt-optimizer, emergency-fund, credit-card-optimizer | debt-optimizer, emergency-fund, credit-card-optimizer | early-career | PASS |
| Newlywed / Wedding | finance-structure, savings-vaults | savings-vaults | finance-structure | PASS |
| Immigration | auth-gap, residency-costs, credit-building | none | auth-gap, residency-costs, credit-building | PASS |
| Home Buying | rent-vs-buy, refinance-calculator | rent-vs-buy, refinance-calculator | NONE | **FAIL** |
| New Parent | parental-leave-navigator, childcare-bubble, childrens-planning, life-insurance-needs, savings-vaults, insurance-adequacy, open-enrollment-comparison | savings-vaults, insurance-adequacy, open-enrollment-comparison | parental-leave-navigator, childcare-bubble, childrens-planning, life-insurance-needs | PASS |
| College Planning | college-529, fafsa-remarriage-timing | none | college-529, fafsa-remarriage-timing | PASS |
| Career Transition | emergency-fund, credit-health, insurance-adequacy, open-enrollment-comparison | emergency-fund, credit-health, insurance-adequacy, open-enrollment-comparison | NONE | **FAIL** |
| Starting a Business | quarterly-tax, retirement-plan-comparison | none | quarterly-tax, retirement-plan-comparison | PASS |
| Gig Work | gig-engine, runway | none | gig-engine, runway | PASS |
| Divorce | emergency-fund-priority, income-stabilization, credit-rebuilding, qdro-readiness, qdro-tracker, debt-optimizer | debt-optimizer | emergency-fund-priority, income-stabilization, credit-rebuilding, qdro-readiness, qdro-tracker | PASS |
| Caregiving | caregiver-ledger | none | caregiver-ledger | PASS |
| Disability | insurance-adequacy, ltd-analyzer | insurance-adequacy | ltd-analyzer | PASS |
| Widowhood | emergency-fund-priority, income-stabilization, credit-rebuilding, ss-survivor-optimizer | none | emergency-fund-priority, income-stabilization, credit-rebuilding, ss-survivor-optimizer | PASS |
| Remarriage / Blended Families | fafsa-remarriage-timing, qtip-bypass-decision | none | fafsa-remarriage-timing (shared w/ College Planning), qtip-bypass-decision (shared w/ Estate) | PASS |
| Retirement | retirement-trajectory, roth-conversion, withdrawal-sequencing, swr, coast-fi, insurance-adequacy, open-enrollment-comparison, ss-survivor-optimizer | retirement-trajectory, roth-conversion, withdrawal-sequencing, insurance-adequacy, open-enrollment-comparison | swr, coast-fi, ss-survivor-optimizer | PASS |
| Estate Planning | qtip-bypass-decision, life-insurance-needs, insurance-adequacy | insurance-adequacy | qtip-bypass-decision (shared w/ Remarriage), life-insurance-needs | PASS |
| Inheritance | inherited-ira-distribution | inherited-ira-distribution | NONE | **FAIL** |

**Three guides fail Criterion 3:** Home Buying, Career Transition, Inheritance. Each currently attaches only Core product tools. These guides have no tool that a user could not access without buying the guide.

Note: a "shared" tool between two guide sections still passes Criterion 3 as long as both guides address the scenario that makes the tool relevant. The rubric's exact language permits this. Only tools that are Core product (available to all users at all times with no guide connection) disqualify.

---

## Doc-vs-Reality Discrepancies: navigation-engine-tool-guide-mapping.md

Audited July 13, 2026. These are mismatches between what navigation-engine-tool-guide-mapping.md says and what the code and /tools page show. The mapping doc is the source Christine reviews; correct it after review, not automatically.

### Discrepancy 1: ss-survivor-optimizer still listed as BACKLOG in Retirement section

**In mapping doc (Section 3, Retirement):** `BACKLOG: Social Security survivor-benefit optimizer (shared with Widowhood guide).`

**In reality:** `/ss-survivor-optimizer` is built and live. `app/ss-survivor-optimizer/page.tsx` exists. `/tools` page lists it under both Widowhood and Retirement. `krovos-open-items-and-backlog.md` marks it RESOLVED.

**Mapping doc inconsistency:** Section 3 Widowhood correctly says the tool is built and live. Section 3 Retirement does not. Section 4 says "0 net-new tools remaining" but Section 3 Retirement contradicts this.

**Correction needed:** In Section 3 Retirement, replace the BACKLOG line with a built/live entry matching the format of the Widowhood section. Remove the internal contradiction with Section 4.

---

### Discrepancy 2: retirement-trajectory appears under Retirement guide on /tools but not in mapping doc

**In mapping doc (Section 3, Retirement):** retirement-trajectory is not listed. It appears only in Section 1 (Core).

**In reality:** `app/tools/page.tsx` includes retirement-trajectory in the Retirement guide category CATEGORIES entry, alongside swr, coast-fi, and others.

**Decision needed:** Was this intentional (added to /tools after the mapping doc was written) or an error in /tools? If retention-trajectory should surface under Retirement as a contextual reinforcement tool, update the mapping doc Section 3 Retirement to reflect this. If /tools is wrong, remove it from the Retirement category in the CATEGORIES array.

---

### Discrepancy 3: life-insurance-needs missing from Estate Planning in mapping doc

**In mapping doc (Section 3, Estate Planning):** life-insurance-needs is not listed. Tools shown: QTIP walkthrough, Document Vault (speced-not-built), Trusted Pro Network (speced-not-built), Insurance Adequacy Analyzer.

**In reality:** `app/tools/page.tsx` includes life-insurance-needs in the Estate Planning CATEGORIES entry. The tool exists at `/life-insurance-needs`.

**Correction needed:** Add life-insurance-needs to Section 3 Estate Planning in the mapping doc. Note it is shared with New Parent guide.

---

### Discrepancy 4: Three guides have no guide-exclusive tool (Criterion 3 gap not flagged)

**Guides:** Home Buying, Career Transition, Inheritance

**In mapping doc:** These guides are listed with only Core product tools. No note flags the Criterion 3 gap.

**Reality:** All tools attached to these three guides are already available to every user as Core product tools. A user who has not purchased any guide has the same tool access as one who purchased these guides.

**No mapping doc correction is automatic here** -- the resolution depends on a product decision: build a guide-exclusive tool for each, redesign the guide bundle, or accept the current state. Flag for Christine's review.

---

### Summary of Corrections Pending Christine's Review

| # | Action | File | Automatic? |
|---|---|---|---|
| 1 | Update ss-survivor-optimizer from BACKLOG to built/live in Section 3 Retirement | navigation-engine-tool-guide-mapping.md | Yes (factual correction) |
| 2 | Decide whether retirement-trajectory belongs under Section 3 Retirement, then align mapping doc or /tools/page.tsx | Both | Requires Christine decision |
| 3 | Add life-insurance-needs to Section 3 Estate Planning | navigation-engine-tool-guide-mapping.md | Yes (factual correction) |
| 4 | Decide whether to build guide-exclusive tools for Home Buying, Career Transition, and Inheritance | Product decision | Requires Christine decision |
