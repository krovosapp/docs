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

## Enforcement Audit: July 13, 2026 (Historical -- Superseded)

### Finding

**Zero guide-specific tool routes have purchase-gate enforcement.** Every tool listed in Section 3 (Guide-Specific Tools) of `navigation-engine-tool-guide-mapping.md` is in `isPublicPath` in `proxy.ts` and contains no purchase-gate code. This is true for all 17 guides and all 37 distinct tool routes audited.

The only purchase-gate enforcement that exists anywhere in the codebase is a client-side check inside `app/life-phase-guides/[slug]/page.tsx` that gates the written guide content page itself. This check does not apply to any tool route.

**This was not discovered as a one-off for any single guide.** The pattern is uniform: every guide tool is public.

### Rubric Implication (as of July 13)

Every guide that was previously assessed as passing Criterion 3 (Tool Attachment) on the basis of topical specificity must be reassessed against Criterion 6 (Enforcement) as failing. No guide passed Criterion 6 as of July 13, 2026.

**This finding is superseded by the July 15, 2026 re-audit below.** Kept here as historical record of the gap and the decision trail; do not treat the July 13 audit table as current state.

---

## Enforcement Audit: July 15, 2026 (Current)

### Finding

**Every guide-specific tool route now has purchase-gate enforcement.** All 24 pre-existing guide-exclusive tools (previously ungated per the July 13 finding above) plus all 27 tools built in the July 15 session are wrapped in `GuideGate` (`app/components/GuideGate.tsx`) and removed from `isPublicPath` in `proxy.ts`. Server-side auth + active-subscription middleware checks now apply uniformly underneath the client-side purchase check.

`GuideGate` checks `user_guide_purchases` for the current user against the tool's `guideSlugs`, falls back to `/api/household/guide-access` for connected-subscriber inheritance, and redirects unauthenticated visitors to sign-in. Shared tools pass an array of qualifying guide slugs (OR-logic) -- confirmed live-working, not just code-reviewed: a subscriber owning either one of two qualifying guides independently is granted access, not only a subscriber owning both.

**Confirmed Core exceptions, not oversights:** `/rent-vs-buy` (deliberate Home Buying lead magnet, stays public and ungated by design), and the Retirement Hub's own tools `/retirement`, `/swr`, `/coast-fi` (Core, no purchase gate, stay public). `/social-security-comparison` and `/rmd-tracker` are also Core/Retirement-Hub tools with no purchase gate, but were swept out of `isPublicPath` along with every other tool touched in the July 15 "all 27, no exceptions" pass -- they now require sign-in + active subscription like any other non-public route, an intentional asymmetry against `/swr`/`/coast-fi`/`/retirement` documented in CLAUDE.md's Product Decisions Log.

### Full Audit Table (Re-audited July 15, 2026)

Audited directly against `proxy.ts` and each route's `page.tsx` source -- not inferred from the July 13 table.

| Tool | Guide(s) | Route | GuideGate `guideSlugs` | In `isPublicPath`? | Purchase-gate |
|---|---|---|---|---|---|
| Early Career Decision Engine | Early Career | /early-career | `early-career` | NO | YES |
| Student Loan Repayment Strategy Tool | Early Career | /student-loan-strategy | `early-career` | NO | YES |
| First Apartment Calculator | Early Career | /first-apartment-calculator | `['early-career']` | NO | YES |
| Finance Structure Comparison | Newlywed | /finance-structure | `newlywed` | NO | YES |
| Wedding Budget Allocator | Newlywed | /wedding-budget-allocator | `newlywed` | NO | YES |
| Filing Status Tax Impact Comparator | Newlywed | /filing-status-comparator | `['newlywed']` | NO | YES |
| Authorization-Gap Bridge Planner | Immigration | /auth-gap | `immigration` | NO | YES |
| Path-to-Residency Cost Planner | Immigration | /residency-costs | `immigration` | NO | YES |
| Credit-Building-From-Zero | Immigration | /credit-building | `immigration` | NO | YES |
| Down Payment vs. PMI Strategy Tool | Home Buying | /down-payment-pmi-strategy | `['home-buying']` | NO | YES |
| Total Cost of Homeownership Calculator | Home Buying | /homeownership-cost-calculator | `home-buying` | NO | YES |
| Closing Cost and Cash-to-Close Estimator | Home Buying | /closing-cost-estimator | `home-buying` | NO | YES |
| Escrow vs. Self-Managed Comparator | Home Buying | /escrow-comparator | `['home-buying']` | NO | YES |
| Parental Leave Navigator | New Parent | /parental-leave-navigator | `new-parent` | NO | YES |
| Children's Planning Hub | New Parent | /childrens-planning | `new-parent` | NO | YES |
| 529 Projection Tool | New Parent | /college-529 | `new-parent` | NO | YES |
| Life Insurance Needs Calculator | New Parent / Estate Planning | /life-insurance-needs | `['new-parent', 'estate-planning']` | NO | YES (OR-logic, live-confirmed) |
| Financial Aid Award Comparison Tool | College Planning | /financial-aid-comparison | `college-planning` | NO | YES |
| Student Loan Optimizer for Parents | College Planning | /parent-loan-optimizer | `college-planning` | NO | YES |
| FAFSA Remarriage-Timing Planner | College Planning / Blended Family | /fafsa-remarriage-timing | `['college-planning', 'blended-family']` | NO | YES (OR-logic) |
| Severance and Benefits Bridge Calculator | Career Transition | /severance-bridge-calculator | `['career-transition']` | NO | YES |
| Offer Comparison Tool | Career Transition | /offer-comparison | `['career-transition']` | NO | YES |
| COBRA vs. Marketplace Comparator | Career Transition | /cobra-vs-marketplace | `career-transition` | NO | YES |
| Business Entity Structure Comparison | Starting a Business | /entity-structure-comparison | `starting-a-business` | NO | YES |
| Quarterly Tax Calculator | Starting a Business | /quarterly-tax | `starting-a-business` | NO | YES |
| Retirement Plan Comparison | Starting a Business | /retirement-plan-comparison | `starting-a-business` | NO | YES |
| Multi-Platform Income and Expense Tracker | Gig Work | /multi-platform-income-tracker | `['gig-work-freelance-income']` | NO | YES |
| Gig Income Engine | Gig Work | /gig-engine | `gig-work-freelance-income` | NO | YES |
| Runway Calculator | Gig Work | /runway | `gig-work-freelance-income` | NO | YES |
| Emergency Fund Priority Tool | Divorce / Widowhood | /emergency-fund-priority | `['divorce', 'widowhood']` | NO | YES (OR-logic) |
| Single Income Budget | Divorce / Widowhood | /income-stabilization | `['divorce', 'widowhood']` | NO | YES (OR-logic) |
| Credit Rebuilding Timeline | Divorce / Widowhood | /credit-rebuilding | `['divorce', 'widowhood']` | NO | YES (OR-logic) |
| QDRO Readiness Prep | Divorce | /qdro-readiness | `divorce` | NO | YES |
| QDRO Tracker and Award Estimator | Divorce | /qdro-tracker | `divorce` | NO | YES |
| Care Options Cost Comparison | Caregiving | /care-options-cost-comparison | `caregiving` | NO | YES |
| Caregiver Impact on Retirement Calculator | Caregiving | /caregiver-retirement-impact | `caregiving` | NO | YES |
| Caregiver Ledger | Caregiving | /caregiver-ledger | `caregiving` | NO | YES |
| Benefit Coordination Calculator | Disability | /benefit-coordination-calculator | `disability-planning` | NO | YES |
| Out-of-Pocket Medical Cost Planner | Disability | /medical-cost-planner | `['disability-planning']` | NO | YES |
| LTD Analyzer | Disability | /ltd-analyzer | `disability-planning` | NO | YES |
| First-Year Financial Checklist | Widowhood | /first-year-financial-checklist | `widowhood` | NO | YES |
| Social Security Survivor-Benefit Optimizer | Widowhood only | /ss-survivor-optimizer | `widowhood` | NO | YES (single-guide, confirmed NOT OR'd against Retirement) |
| Blended Family Conflict Checker | Blended Family | /blended-family-conflict-checker | `blended-family` | NO | YES |
| QTIP vs. Bypass Trust Walkthrough | Blended Family / Estate Planning | /qtip-bypass-decision | `['blended-family', 'estate-planning']` | NO | YES (OR-logic, live-confirmed) |
| Beneficiary Designation Audit | Estate Planning | /beneficiary-designation-audit | `estate-planning` | NO | YES |
| Estate Tax Threshold and State Exposure Checker | Estate Planning | /estate-tax-checker | `estate-planning` | NO | YES |
| Windfall Allocation Tool | Inheritance | /windfall-allocation | `inheritance` | NO | YES |
| Step-Up in Basis and Capital Gains Estimator | Inheritance | /step-up-basis-calculator | `inheritance` | NO | YES |
| Inherited IRA Distribution Strategy | Inheritance | /inherited-ira-distribution | `inheritance` | NO | YES (moved from Core, July 15) |
| Debt Optimizer | Core (shared) | /debt-optimizer | -- | NO | N/A -- Core, no gate needed |
| Emergency Fund Calculator | Core (shared) | /emergency-fund | -- | NO | N/A -- Core |
| Credit Card Engine | Core (shared) | /credit-card-optimizer | -- | NO | N/A -- Core |
| Savings Vault System | Core (shared) | /savings-vaults | -- | NO | N/A -- Core |
| Insurance Adequacy Analyzer | Core (shared) | /insurance-adequacy | -- | NO | N/A -- Core |
| Open Enrollment Comparison | Core (shared) | /open-enrollment-comparison | -- | NO | N/A -- Core |
| Credit Health Dashboard | Core (shared) | /credit-health | -- | NO | N/A -- Core |
| Roth Conversion Modeling | Core (shared) | /roth-conversion | -- | NO | N/A -- Core |
| Withdrawal Sequencing | Core (shared) | /withdrawal-sequencing | -- | NO | N/A -- Core |
| Rent vs. Buy Calculator | Home Buying lead magnet | /rent-vs-buy | -- | **YES** | N/A -- deliberately public, not gated |
| Retirement Hub | Core / Retirement Hub | /retirement | -- | **YES** | N/A -- Core |
| Safe Withdrawal Rate Calculator | Core / Retirement Hub | /swr | -- | **YES** | N/A -- Core |
| CoastFI Calculator | Core / Retirement Hub | /coast-fi | -- | **YES** | N/A -- Core |
| Social Security Claiming Comparison | Core / Retirement Hub | /social-security-comparison | -- | NO | N/A -- Core, but auth+sub required (swept out of isPublicPath) |
| RMD Deadline Tracker | Core / Retirement Hub | /rmd-tracker | -- | NO | N/A -- Core, but auth+sub required (swept out of isPublicPath) |
| Retirement Trajectory | Deprecated | /retirement-trajectory | -- | NO | N/A -- redirect stub to /retirement |

**Every guide-specific tool row above shows YES.** All 51 distinct tool routes audited (24 pre-existing guide tools + 27 new tools) pass Criterion 6.

### Bugs found and fixed during the July 15 re-audit

1. **Broken import** in `/social-security-comparison` (`@supabase/auth-helpers-nextjs`, a package not installed) -- build was failing. Fixed: swapped to the project's standard `createClient()` from `lib/supabase.ts`.
2. **4 wrong `guideSlugs` values** in newly-built tools, where the slug didn't match any real `life_phase_guides.slug` -- `GuideGate`'s fail-open fallback (grants access when no matching guide row is found) was silently granting free access:
   - `benefit-coordination-calculator`: `"disability"` -> `"disability-planning"`
   - `medical-cost-planner`: `['disability']` -> `['disability-planning']`
   - `blended-family-conflict-checker`: `"remarriage"` -> `"blended-family"`
   - `multi-platform-income-tracker`: `['gig-work']` -> `['gig-work-freelance-income']`
   Confirmed against live production (not just the migration files): navigated to `/life-phase-guides/<slug>` for all 7 slugs as a signed-in test user. The 4 corrected slugs each resolved to their real guide page; the 3 wrong slugs each fell through to the marketplace listing.
3. **`GUIDE_TOOLS` lookup keys** in `app/life-phase-guides/[slug]/page.tsx` (a separate table from GuideGate, used to render the "Tools for this guide" section on each guide's own page) had the same three wrong keys: `'disability'`, `'gig-work'`, `'remarriage'`. Since this object is keyed directly by the URL slug param, the Disability, Gig Work, and Blended Family guide pages were silently rendering zero attached tools. Fixed to `'disability-planning'`, `'gig-work-freelance-income'`, `'blended-family'`. Confirmed live: `/life-phase-guides/disability-planning` now renders its tools section.
4. **`isPublicPath` inconsistency**: 14 of the 27 new tools had been added to `isPublicPath` (bypassing server-side auth) mid-session, the other 13 had not, with no logged product decision. Resolved: all 27 removed, no exceptions, decision logged in CLAUDE.md's Product Decisions Log.
5. **24 of the pre-existing guide-exclusive tools had zero `GuideGate` wrapping** (16 had no reference at all; 8 had a dangling unused import added but never actually wrapped in JSX, apparently from an earlier interrupted session). All 24 wrapped in this re-audit, using slugs verified directly against Supabase migration seed data.

### Live Verification (July 15, 2026)

- `npm run build` -- clean, all routes compile.
- `/gig-engine` blocked for a subscriber not owning Gig Work & Freelance Income -- CONFIRMED.
- `/quarterly-tax` allowed for a subscriber owning Starting a Business -- CONFIRMED.
- `/auth-gap` blocked for a subscriber not owning Immigration -- CONFIRMED.
- OR-logic (`life-insurance-needs`, gated to `new-parent` OR `estate-planning`): a subscriber owning New Parent only -- allowed, CONFIRMED. A subscriber owning Estate Planning only -- allowed, CONFIRMED. A subscriber owning neither -- blocked, shown both purchase CTAs, CONFIRMED.
- Retirement Hub (`/retirement`): all 6 linked tools render (SWR, CoastFI, Roth Conversion, Withdrawal Sequencing, Social Security Claiming Comparison, RMD Deadline Tracker) plus FreedomScenarios inline, pulling real Life Graph data -- CONFIRMED.
- `/retirement-trajectory` redirects cleanly to `/retirement` -- CONFIRMED.

Full session detail: `BUILD_LOG.md` in the app repo, entry "27 New Guide-Exclusive Tools, Retirement Hub, Full Purchase-Gate Enforcement."

### Implications for Pricing Decisions

1. Every guide-specific tool now requires both an active subscription (server-side) and the specific guide purchase (client-side `GuideGate`), or connected-household inheritance of that purchase.
2. `Rent vs. Buy Calculator` remains the sole deliberate exception: public, no auth, no gate, by design as Home Buying's lead magnet.
3. Any future guide-exclusive tool route must be built with `GuideGate` and removed from `isPublicPath` at creation time -- see the Build Conventions section of CLAUDE.md.

---

## How to Use This Document

- Apply the six-criterion rubric to every guide before release.
- Criterion 6 (Enforcement) requires a code check, not a docs check. The audit table above shows current state as of July 15, 2026. Re-audit any route where enforcement logic changes.
- Update this document when enforcement changes on a route, when a guide's Criterion 6 status changes, or when the rubric criteria are revised.
- This document lives in `docs/product/guide-approval-criteria.md`. The pointer in `CLAUDE.md` Product Decisions Log is the canonical reference.

---

## Full Tool Inventory

**Re-audited:** July 15, 2026
**Source:** app/ directory listing, proxy.ts isPublicPath, app/tools/page.tsx CATEGORIES array, per-file GuideGate grep, per-file Life Graph pre-fill grep

51 distinct guide-adjacent tool routes live in the codebase (24 pre-existing + 27 new), plus the Core Product and Retirement Hub tools. Every guide-exclusive tool is gated (see the audit table above for exact `guideSlugs`, no repetition here). This section covers discoverability and Life Graph pre-fill status only.

All tools are linked from `/tools`, except `/goals` (auth + active subscription required, intentionally excluded from the /tools directory) and `/life-gaps` (lead magnet quiz, not a tool per se).

"LG Pre-fill: YES" means the tool reads from `life_graph` via Supabase. Fields listed are the ones confirmed by grep at audit time -- the set may be incomplete if the tool reads through intermediate functions. Tools built in the July 15 session were not individually re-audited for Life Graph pre-fill status against the Build Conventions requirement in CLAUDE.md; that remains an open follow-up, not confirmed either way for the 27 new tools.

| Route | Guide(s) | LG Pre-fill (pre-existing tools, audited July 13) |
|---|---|---|
| /auth-gap | Immigration | NO |
| /caregiver-ledger | Caregiving | NO |
| /childcare-bubble | New Parent | YES: spending_total_monthly |
| /childrens-planning | New Parent | YES |
| /coast-fi | Retirement Hub | YES: investments_total, retirement_goals, spending_total_monthly, partner_name |
| /college-529 | New Parent (moved from College Planning) | NO |
| /credit-building | Immigration | NO |
| /credit-card-optimizer | Core, Early Career | NO |
| /credit-health | Core, Career Transition | NO |
| /credit-rebuilding | Divorce, Widowhood | NO |
| /debt-optimizer | Core, Early Career, Divorce | YES |
| /early-career | Early Career | NO |
| /emergency-fund | Core, Early Career, Career Transition | YES: spending_total_monthly |
| /emergency-fund-priority | Divorce, Widowhood | YES: investment_data, spending_total_monthly |
| /fafsa-remarriage-timing | College Planning, Blended Family | YES: num_children, investments_total |
| /finance-structure | Newlywed | NO |
| /gig-engine | Gig Work | NO |
| /household-calendar | Core | NO |
| /income-stabilization | Divorce, Widowhood | YES: income_data, my_takehome |
| /inherited-ira-distribution | Inheritance (moved from Core) | NO |
| /insurance-adequacy | Core, New Parent, Disability, Retirement, Estate | YES: num_children, income_data |
| /life-insurance-needs | New Parent, Estate Planning | YES: investments_total, my_takehome |
| /ltd-analyzer | Disability | NO |
| /milestones | Core | YES: investments_total, retirement_age, my_takehome, spending_total_monthly |
| /net-worth | Core | NO |
| /open-enrollment-comparison | Core, New Parent, Career Transition, Retirement | YES |
| /parental-leave-navigator | New Parent | YES: state, my_takehome, spending_total_monthly |
| /paycheck-allocation | Core | YES: my_takehome, spending_total_monthly |
| /purchase-strategy | Core | NO |
| /qdro-readiness | Divorce | NO |
| /qdro-tracker | Divorce | YES: investments_total |
| /qtip-bypass-decision | Blended Family, Estate Planning | YES: state |
| /quarterly-tax | Starting a Business | NO |
| /refinance-calculator | Core, Home Buying | NO |
| /rent-vs-buy | Core, Home Buying (public lead magnet) | NO |
| /residency-costs | Immigration | YES: investment_data |
| /retirement-plan-comparison | Starting a Business | NO |
| /retirement-trajectory | Deprecated, redirects to /retirement | N/A -- redirect stub |
| /roth-conversion | Core, Retirement | YES: income_data, my_takehome |
| /runway | Gig Work | NO |
| /savings-vaults | Core, Newlywed, New Parent | YES |
| /ss-survivor-optimizer | Widowhood only (no longer Retirement, see above) | NO |
| /swr | Retirement Hub | YES: investments_total, retirement_goals, spending_total_monthly, has_partner, partner_name |
| /withdrawal-sequencing | Core, Retirement | YES: investments_total |
| 27 new tools (all built July 15, 2026) | See enforcement audit table above for guide assignment | NOT AUDITED -- follow-up needed |

---

## Guide-by-Guide Reconciliation: Criterion 3 (Tool Attachment)

Criterion 3 asks: does the guide have at least one tool that is genuinely topic-specific and not just a shared core product tool?

**Core Product tools** (available to every user from /tools Core section, regardless of guide):
net-worth, debt-optimizer, paycheck-allocation, savings-vaults, emergency-fund, credit-health, credit-card-optimizer, purchase-strategy, refinance-calculator, rent-vs-buy, insurance-adequacy, open-enrollment-comparison, roth-conversion, withdrawal-sequencing, household-calendar, milestones, goals

**Retirement Hub tools** (Core, consolidated at /retirement): retirement, swr, coast-fi, social-security-comparison, rmd-tracker. `retirement-trajectory` is deprecated (redirects to /retirement).

| Guide | Genuinely Exclusive Tools (July 15, 2026) | Criterion 3 |
|---|---|---|
| Early Career Starter Kit | early-career, student-loan-strategy, first-apartment-calculator | PASS |
| Newlywed / Wedding | finance-structure, wedding-budget-allocator, filing-status-comparator | PASS |
| Immigration | auth-gap, residency-costs, credit-building | PASS |
| Home Buying | down-payment-pmi-strategy, homeownership-cost-calculator, closing-cost-estimator, escrow-comparator | **PASS (was FAIL July 13)** |
| New Parent | parental-leave-navigator, childcare-bubble, childrens-planning, college-529, life-insurance-needs (shared w/ Estate Planning) | PASS |
| College Planning | financial-aid-comparison, parent-loan-optimizer, fafsa-remarriage-timing (shared w/ Blended Family) | PASS |
| Career Transition | severance-bridge-calculator, offer-comparison, cobra-vs-marketplace | **PASS (was FAIL July 13)** |
| Starting a Business | entity-structure-comparison, quarterly-tax, retirement-plan-comparison | PASS |
| Gig Work | multi-platform-income-tracker, gig-engine, runway | PASS |
| Divorce | emergency-fund-priority (shared w/ Widowhood), income-stabilization (shared w/ Widowhood), credit-rebuilding (shared w/ Widowhood), qdro-readiness, qdro-tracker | PASS |
| Caregiving | care-options-cost-comparison, caregiver-retirement-impact, caregiver-ledger | PASS |
| Disability | benefit-coordination-calculator, medical-cost-planner, ltd-analyzer | PASS |
| Widowhood | first-year-financial-checklist, emergency-fund-priority (shared), income-stabilization (shared), credit-rebuilding (shared), ss-survivor-optimizer | PASS |
| Remarriage / Blended Families | blended-family-conflict-checker, fafsa-remarriage-timing (shared w/ College Planning), qtip-bypass-decision (shared w/ Estate Planning) | PASS |
| Retirement | No longer sells separately as of July 15, 2026 -- tools consolidated into the Core Retirement Hub | N/A |
| Estate Planning | beneficiary-designation-audit, estate-tax-checker, qtip-bypass-decision (shared w/ Blended Family), life-insurance-needs (shared w/ New Parent) | PASS |
| Inheritance | windfall-allocation, step-up-basis-calculator, inherited-ira-distribution | **PASS (was FAIL July 13)** |

**All guides now pass Criterion 3.** The three guides that failed on July 13 (Home Buying, Career Transition, Inheritance) each gained 3-4 genuinely exclusive tools in the July 15 build.

---

## Doc-vs-Reality Discrepancies from July 13 Audit -- All Resolved July 15, 2026

| # | Discrepancy (July 13) | Resolution (July 15) |
|---|---|---|
| 1 | ss-survivor-optimizer listed as BACKLOG in Retirement section, contradicting Section 4's "0 net-new tools remaining" | Resolved. `/ss-survivor-optimizer` is now Widowhood-exclusive only, single-guide gate, confirmed not OR'd against Retirement (which no longer sells separately). No longer listed under Retirement at all. |
| 2 | retirement-trajectory appeared under Retirement guide on /tools but not in the mapping doc | Resolved by deprecation. `/retirement-trajectory` is now a redirect stub to `/retirement`; the question of which guide section it belonged under is moot. |
| 3 | life-insurance-needs missing from Estate Planning in the mapping doc | Resolved. Listed under both New Parent and Estate Planning in `navigation-engine-tool-guide-mapping.md`, with OR-logic gating (`['new-parent', 'estate-planning']`) confirmed live. |
| 4 | Three guides (Home Buying, Career Transition, Inheritance) had no guide-exclusive tool, Criterion 3 gap not flagged | Resolved. Each guide gained 3-4 genuinely exclusive, purchase-gated tools in the July 15 build. All three now pass Criterion 3. See the reconciliation table above. |

Full detail on the July 15 build and its live verification: `navigation-engine-tool-guide-mapping.md` and `BUILD_LOG.md` (app repo).
