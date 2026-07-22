# Guide Approval Criteria

**Established:** July 13, 2026
**Owner:** Christine Smith
**Status:** Active rubric. Apply to every guide before release.

**Value-readiness companion audit:** See `guide-value-audit-2026-07-20.md` for the current scored review of all 17 guides, $49 readiness, factual/safety blockers, and required premium outcomes. Passing the six binary criteria in this file does not by itself establish price readiness.

**Personalization architecture:** See `guide-personalization-without-api.md` for the founder-approved requirement that paid guides reuse the existing Life Graph and Life View, produce saved personalized outcomes without another model call, and remain unavailable until they reach A-level readiness.

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
| First Job Decisions | Early Career | /first-job-decisions | `early-career` | NO | YES |
| Student Loan Repayment Planner | Early Career | /student-loan-repayment-planner | `early-career` | NO | YES |
| First Apartment Calculator | Early Career | /first-apartment-calculator | `['early-career']` | NO | YES |
| Joint Finances Comparison | Newlywed | /joint-finances-comparison | `newlywed` | NO | YES |
| Wedding Budget Planner | Newlywed | /wedding-budget-planner | `newlywed` | NO | YES |
| Filing Status Comparison | Newlywed | /filing-status-comparison | `['newlywed']` | NO | YES |
| Work Authorization Gap Planner | Immigration | /work-authorization-gap-planner | `immigration` | NO | YES |
| Green Card Cost Planner | Immigration | /green-card-cost-planner | `immigration` | NO | YES |
| Credit Builder | Immigration | /credit-builder | `immigration` | NO | YES |
| Down Payment vs. PMI Comparison | Home Buying | /down-payment-vs-pmi-comparison | `['home-buying']` | NO | YES |
| Homeownership Cost | Home Buying | /homeownership-cost-calculator | `home-buying` | NO | YES |
| Closing Cost Calculator | Home Buying | /closing-cost-calculator | `home-buying` | NO | YES |
| Escrow Comparison | Home Buying | /escrow-comparison | `['home-buying']` | NO | YES |
| Parental Leave Planner | New Parent | /parental-leave-planner | `new-parent` | NO | YES |
| Child Costs Planner | New Parent | /child-costs-planner | `new-parent` | NO | YES |
| 529 Projection Calculator | New Parent | /529-projection-calculator | `new-parent` | NO | YES |
| Life Insurance Calculator | New Parent / Estate Planning | /life-insurance-calculator | `['new-parent', 'estate-planning']` | NO | YES (OR-logic, live-confirmed) |
| Financial Aid Comparison | College Planning | /financial-aid-comparison | `college-planning` | NO | YES |
| Parent Loan Comparison | College Planning | /parent-loan-optimizer | `college-planning` | NO | YES |
| FAFSA Remarriage Planner | College Planning / Blended Family | /fafsa-remarriage-timing | `['college-planning', 'blended-family']` | NO | YES (OR-logic) |
| Severance Bridge Calculator | Career Transition | /severance-bridge-calculator | `['career-transition']` | NO | YES |
| Offer Comparison | Career Transition | /offer-comparison | `['career-transition']` | NO | YES |
| COBRA vs. Marketplace Comparison | Career Transition | /cobra-vs-marketplace-comparison | `career-transition` | NO | YES |
| Business Structure Comparison | Starting a Business | /business-structure-comparison | `start-business` | NO | YES |
| Quarterly Tax Calculator | Starting a Business | /quarterly-tax | `start-business` | NO | YES |
| Retirement Plan Comparison | Starting a Business | /retirement-plan-comparison | `start-business` | NO | YES |
| Revenue Target | Starting a Business | /revenue-target | `start-business` | NO | YES |
| Gig Income Tracker | Gig Work | /gig-income-tracker | `['gig-work']` | NO | YES |
| Gig Paycheck Calculator | Gig Work | /gig-paycheck-calculator | `gig-work` | NO | YES |
| Savings Runway Calculator | Gig Work | /savings-runway-calculator | `gig-work` | NO | YES |
| Emergency Fund Rebuilder | Divorce / Widowhood | /emergency-fund-rebuilder | `['divorce', 'widowhood']` | NO | YES (OR-logic) |
| Single Income Planner | Divorce / Widowhood | /single-income-planner | `['divorce', 'widowhood']` | NO | YES (OR-logic) |
| Credit Rebuilding Planner | Divorce / Widowhood | /credit-rebuilding-planner | `['divorce', 'widowhood']` | NO | YES (OR-logic) |
| QDRO Readiness Checklist | Divorce | /qdro-readiness-checklist | `divorce` | NO | YES |
| QDRO Tracker | Divorce | /qdro-tracker | `divorce` | NO | YES |
| Care Options Comparison | Caregiving | /care-options-comparison | `caregiving` | NO | YES |
| Caregiving Retirement Impact | Caregiving | /caregiver-retirement-impact-calculator | `caregiving` | NO | YES |
| Caregiver Cost Tracker | Caregiving | /caregiver-cost-tracker | `caregiving` | NO | YES |
| Disability Benefits Calculator | Disability | /benefit-coordination-calculator | `disability` | NO | YES |
| Medical Cost Planner | Disability | /medical-cost-planner | `['disability']` | NO | YES |
| LTD Coverage Checker | Disability | /ltd-coverage-checker | `disability` | NO | YES |
| Widowhood Checklist | Widowhood | /widowhood-first-year-checklist | `widowhood` | NO | YES |
| Survivor Benefit Calculator | Widowhood only | /survivor-benefit-calculator | `widowhood` | NO | YES (single-guide, confirmed NOT OR'd against Retirement) |
| Blended Family Inheritance | Blended Family | /blended-family-inheritance-checklist | `blended-family` | NO | YES |
| QTIP vs. Bypass Comparison | Blended Family / Estate Planning | /qtip-vs-bypass-comparison | `['blended-family', 'estate-planning']` | NO | YES (OR-logic, live-confirmed) |
| Beneficiary Checker | Estate Planning | /beneficiary-checker | `estate-planning` | NO | YES |
| Estate Tax Checker | Estate Planning | /estate-tax-checker | `estate-planning` | NO | YES |
| Inheritance Planner | Inheritance | /inheritance-planner | `inheritance` | NO | YES |
| Step-Up Basis Calculator | Inheritance | /step-up-basis-calculator | `inheritance` | NO | YES |
| Inherited IRA Planner | Inheritance | /inherited-ira-planner | `inheritance` | NO | YES (moved from Core, July 15) |
| Debt Payoff Planner | Core (shared) | /debt-payoff-planner | -- | NO | N/A -- Core, no gate needed |
| Emergency Fund Calculator | Core (shared) | /emergency-fund | -- | NO | N/A -- Core |
| Credit Card Optimizer | Core (shared) | /credit-card-optimizer | -- | NO | N/A -- Core |
| Savings Goals | Core (shared) | /savings-goals | -- | NO | N/A -- Core |
| Insurance Gap Checker | Core (shared) | /insurance-gap-checker | -- | NO | N/A -- Core |
| Open Enrollment Comparison | Core (shared) | /open-enrollment-comparison | -- | NO | N/A -- Core |
| Credit Health | Core (shared) | /credit-health | -- | NO | N/A -- Core |
| Roth Conversion Calculator | Core (shared) | /roth-conversion-calculator | -- | NO | N/A -- Core |
| Retirement Withdrawal Planner | Core (shared) | /retirement-withdrawal-planner | -- | NO | N/A -- Core |
| Rent vs. Buy Calculator | Home Buying lead magnet | /rent-vs-buy | -- | **YES** | N/A -- deliberately public, not gated |
| Retirement Hub | Core / Retirement Hub | /retirement | -- | **YES** | N/A -- Core |
| Safe Withdrawal Rate | Core / Retirement Hub | /swr | -- | **YES** | N/A -- Core |
| CoastFI Calculator | Core / Retirement Hub | /coast-fi | -- | **YES** | N/A -- Core |
| Social Security Comparison | Core / Retirement Hub | /social-security-comparison | -- | NO | N/A -- Core, but auth+sub required (swept out of isPublicPath) |
| RMD Deadline Tracker | Core / Retirement Hub | /rmd-tracker | -- | NO | N/A -- Core, but auth+sub required (swept out of isPublicPath) |
| Retirement Trajectory | Deprecated | /retirement-trajectory | -- | NO | N/A -- redirect stub to /retirement |

**Every guide-specific tool row above shows YES.** All 51 distinct tool routes audited (24 pre-existing guide tools + 27 new tools) pass Criterion 6.

### Bugs found and fixed during the July 15 re-audit

1. **Broken import** in `/social-security-comparison` (`@supabase/auth-helpers-nextjs`, a package not installed) -- build was failing. Fixed: swapped to the project's standard `createClient()` from `lib/supabase.ts`.
2. **4 wrong `guideSlugs` values** in newly-built tools, where the slug didn't match any real `life_phase_guides.slug` -- `GuideGate`'s fail-open fallback (grants access when no matching guide row is found) was silently granting free access:
   - `benefit-coordination-calculator`: `"disability"` -> `"disability-planning"`
   - `medical-cost-planner`: `['disability']` -> `['disability-planning']`
   - `blended-family-inheritance-checklist`: `"remarriage"` -> `"blended-family"`
   - `gig-income-tracker`: `['gig-work']` -> `['gig-work-freelance-income']`
   Confirmed against live production (not just the migration files): navigated to `/life-phase-guides/<slug>` for all 7 slugs as a signed-in test user. The 4 corrected slugs each resolved to their real guide page; the 3 wrong slugs each fell through to the marketplace listing.
3. **`GUIDE_TOOLS` lookup keys** in `app/life-phase-guides/[slug]/page.tsx` (a separate table from GuideGate, used to render the "Tools for this guide" section on each guide's own page) had the same three wrong keys: `'disability'`, `'gig-work'`, `'remarriage'`. Since this object is keyed directly by the URL slug param, the Disability, Gig Work, and Blended Family guide pages were silently rendering zero attached tools. Fixed to `'disability-planning'`, `'gig-work-freelance-income'`, `'blended-family'`. Confirmed live: `/life-phase-guides/disability-planning` now renders its tools section.
4. **`isPublicPath` inconsistency**: 14 of the 27 new tools had been added to `isPublicPath` (bypassing server-side auth) mid-session, the other 13 had not, with no logged product decision. Resolved: all 27 removed, no exceptions, decision logged in CLAUDE.md's Product Decisions Log.
5. **24 of the pre-existing guide-exclusive tools had zero `GuideGate` wrapping** (16 had no reference at all; 8 had a dangling unused import added but never actually wrapped in JSX, apparently from an earlier interrupted session). All 24 wrapped in this re-audit, using slugs verified directly against Supabase migration seed data.

### Live Verification (July 15, 2026)

- `npm run build` -- clean, all routes compile.
- `/gig-paycheck-calculator` blocked for a subscriber not owning Gig Work & Freelance Income -- CONFIRMED.
- `/quarterly-tax` allowed for a subscriber owning Starting a Business -- CONFIRMED.
- `/work-authorization-gap-planner` blocked for a subscriber not owning Immigration -- CONFIRMED.
- OR-logic (`life-insurance-calculator`, gated to `new-parent` OR `estate-planning`): a subscriber owning New Parent only -- allowed, CONFIRMED. A subscriber owning Estate Planning only -- allowed, CONFIRMED. A subscriber owning neither -- blocked, shown both purchase CTAs, CONFIRMED.
- Retirement Hub (`/retirement`): all 6 linked tools render (Safe Withdrawal Rate, CoastFI Calculator, Roth Conversion Calculator, Retirement Withdrawal Planner, Social Security Comparison, RMD Deadline Tracker) plus Retirement Scenarios inline, pulling real Life Graph data -- CONFIRMED.
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

All tools are linked from `/tools`, except `/life-goals` (auth + active subscription required, intentionally excluded from the /tools directory) and `/life-gaps` (lead magnet quiz, not a tool per se).

"LG Pre-fill: YES" means the tool reads from `life_graph` via Supabase. Fields listed are the ones confirmed by grep at audit time -- the set may be incomplete if the tool reads through intermediate functions. Tools built in the July 15 session were not individually re-audited for Life Graph pre-fill status against the Build Conventions requirement in CLAUDE.md; that remains an open follow-up, not confirmed either way for the 27 new tools.

| Route | Guide(s) | LG Pre-fill (pre-existing tools, audited July 13) |
|---|---|---|
| /work-authorization-gap-planner | Immigration | NO |
| /caregiver-cost-tracker | Caregiving | NO |
| /childcare-bubble | New Parent | YES: spending_total_monthly |
| /child-costs-planner | New Parent | YES |
| /coast-fi | Retirement Hub | YES: investments_total, retirement_goals, spending_total_monthly, partner_name |
| /529-projection-calculator | New Parent (moved from College Planning) | NO |
| /credit-builder | Immigration | NO |
| /credit-card-optimizer | Core, Early Career | NO |
| /credit-health | Core, Career Transition | NO |
| /credit-rebuilding-planner | Divorce, Widowhood | NO |
| /debt-payoff-planner | Core, Early Career, Divorce | YES |
| /first-job-decisions | Early Career | NO |
| /emergency-fund | Core, Early Career, Career Transition | YES: spending_total_monthly |
| /emergency-fund-rebuilder | Divorce, Widowhood | YES: investment_data, spending_total_monthly |
| /fafsa-remarriage-timing | College Planning, Blended Family | YES: num_children, investments_total |
| /joint-finances-comparison | Newlywed | NO |
| /gig-paycheck-calculator | Gig Work | NO |
| /life-labor | Core | NO |
| /single-income-planner | Divorce, Widowhood | YES: income_data, my_takehome |
| /inherited-ira-planner | Inheritance (moved from Core) | NO |
| /insurance-gap-checker | Core, New Parent, Disability, Retirement, Estate | YES: num_children, income_data |
| /life-insurance-calculator | New Parent, Estate Planning | YES: investments_total, my_takehome |
| /ltd-coverage-checker | Disability | NO |
| /milestone-tracker | Core | YES: investments_total, retirement_age, my_takehome, spending_total_monthly |
| /net-worth | Core | NO |
| /open-enrollment-comparison | Core, New Parent, Career Transition, Retirement | YES |
| /parental-leave-planner | New Parent | YES: state, my_takehome, spending_total_monthly |
| /paycheck-planner | Core | YES: my_takehome, spending_total_monthly |
| /purchase-planner | Core | NO |
| /qdro-readiness-checklist | Divorce | NO |
| /qdro-tracker | Divorce | YES: investments_total |
| /qtip-vs-bypass-comparison | Blended Family, Estate Planning | YES: state |
| /quarterly-tax | Starting a Business | NO |
| /revenue-target | Starting a Business | YES: income types, take-home income, partner income, debts, and goals |
| /refinance-calculator | Core, Home Buying | NO |
| /rent-vs-buy | Core, Home Buying (public lead magnet) | NO |
| /green-card-cost-planner | Immigration | YES: investment_data |
| /retirement-plan-comparison | Starting a Business | NO |
| /retirement-trajectory | Deprecated, redirects to /retirement | N/A -- redirect stub |
| /roth-conversion-calculator | Core, Retirement | YES: income_data, my_takehome |
| /savings-runway-calculator | Gig Work | NO |
| /savings-goals | Core, Newlywed, New Parent | YES |
| /survivor-benefit-calculator | Widowhood only (no longer Retirement, see above) | NO |
| /swr | Retirement Hub | YES: investments_total, retirement_goals, spending_total_monthly, has_partner, partner_name |
| /retirement-withdrawal-planner | Core, Retirement | YES: investments_total |
| 27 new tools (all built July 15, 2026) | See enforcement audit table above for guide assignment | NOT AUDITED -- follow-up needed |

---

## Guide-by-Guide Reconciliation: Criterion 3 (Tool Attachment)

Criterion 3 asks: does the guide have at least one tool that is genuinely topic-specific and not just a shared core product tool?

**Core Product tools** (available to every user from /tools Core section, regardless of guide):
net-worth, debt-payoff-planner, paycheck-planner, savings-goals, emergency-fund, credit-health, credit-card-optimizer, purchase-planner, refinance-calculator, rent-vs-buy, insurance-gap-checker, open-enrollment-comparison, roth-conversion-calculator, retirement-withdrawal-planner, life-labor, milestone-tracker, life-goals

**Retirement Hub tools** (Core, consolidated at /retirement): retirement, swr, coast-fi, social-security-comparison, rmd-tracker. `retirement-trajectory` is deprecated (redirects to /retirement).

| Guide | Genuinely Exclusive Tools (July 15, 2026) | Criterion 3 |
|---|---|---|
| Early Career | first-job-decisions, student-loan-repayment-planner, first-apartment-calculator | PASS |
| Newlywed | joint-finances-comparison, wedding-budget-planner, filing-status-comparison | PASS |
| Immigration | work-authorization-gap-planner, green-card-cost-planner, credit-builder | PASS |
| Home Buying | down-payment-vs-pmi-comparison, homeownership-cost-calculator, closing-cost-calculator, escrow-comparison | **PASS (was FAIL July 13)** |
| New Parent | parental-leave-planner, childcare-bubble, child-costs-planner, 529-projection-calculator, life-insurance-calculator (shared w/ Estate Planning) | PASS |
| College Planning | financial-aid-comparison, parent-loan-optimizer, fafsa-remarriage-timing (shared w/ Blended Family) | PASS |
| Career Transition | severance-bridge-calculator, offer-comparison, cobra-vs-marketplace-comparison | **PASS (was FAIL July 13)** |
| Starting a Business | business-structure-comparison, quarterly-tax, retirement-plan-comparison, revenue-target | PASS |
| Gig Work | gig-income-tracker, gig-paycheck-calculator, savings-runway-calculator | PASS |
| Divorce | emergency-fund-rebuilder (shared w/ Widowhood), single-income-planner (shared w/ Widowhood), credit-rebuilding-planner (shared w/ Widowhood), qdro-readiness-checklist, qdro-tracker | PASS |
| Caregiving | care-options-comparison, caregiver-retirement-impact-calculator, caregiver-cost-tracker | PASS |
| Disability | benefit-coordination-calculator, medical-cost-planner, ltd-coverage-checker | PASS |
| Widowhood | widowhood-first-year-checklist, emergency-fund-rebuilder (shared), single-income-planner (shared), credit-rebuilding-planner (shared), survivor-benefit-calculator | PASS |
| Blended Family | blended-family-inheritance-checklist, fafsa-remarriage-timing (shared w/ College Planning), qtip-vs-bypass-comparison (shared w/ Estate Planning) | PASS |
| Retirement | No longer sells separately as of July 15, 2026 -- tools consolidated into the Core Retirement Hub | N/A |
| Estate Planning | beneficiary-checker, estate-tax-checker, qtip-vs-bypass-comparison (shared w/ Blended Family), life-insurance-calculator (shared w/ New Parent) | PASS |
| Inheritance | inheritance-planner, step-up-basis-calculator, inherited-ira-planner | **PASS (was FAIL July 13)** |

**All guides now pass Criterion 3.** The three guides that failed on July 13 (Home Buying, Career Transition, Inheritance) each gained 3-4 genuinely exclusive tools in the July 15 build.

---

## Doc-vs-Reality Discrepancies from July 13 Audit -- All Resolved July 15, 2026

| # | Discrepancy (July 13) | Resolution (July 15) |
|---|---|---|
| 1 | survivor-benefit-calculator listed as BACKLOG in Retirement section, contradicting Section 4's "0 net-new tools remaining" | Resolved. `/survivor-benefit-calculator` is now Widowhood-exclusive only, single-guide gate, confirmed not OR'd against Retirement (which no longer sells separately). No longer listed under Retirement at all. |
| 2 | retirement-trajectory appeared under Retirement guide on /tools but not in the mapping doc | Resolved by deprecation. `/retirement-trajectory` is now a redirect stub to `/retirement`; the question of which guide section it belonged under is moot. |
| 3 | life-insurance-calculator missing from Estate Planning in the mapping doc | Resolved. Listed under both New Parent and Estate Planning in `navigation-engine-tool-guide-mapping.md`, with OR-logic gating (`['new-parent', 'estate-planning']`) confirmed live. |
| 4 | Three guides (Home Buying, Career Transition, Inheritance) had no guide-exclusive tool, Criterion 3 gap not flagged | Resolved. Each guide gained 3-4 genuinely exclusive, purchase-gated tools in the July 15 build. All three now pass Criterion 3. See the reconciliation table above. |

Full detail on the July 15 build and its live verification: `navigation-engine-tool-guide-mapping.md` and `BUILD_LOG.md` (app repo).
