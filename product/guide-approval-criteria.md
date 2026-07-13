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
