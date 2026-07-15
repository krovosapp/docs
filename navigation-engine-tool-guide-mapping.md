# Navigation Engine: Tool and Guide Reference

This document maps which Navigation Engine tools connect to which guide, records verified build status for every tool, and tracks the confirmed-gap backlog of net-new tools to build. Core product tools are available to every user. Guide-specific tools are surfaced when a user engages with that guide, and are purchase-gated (see Section 6).

**Last reconciled:** July 15, 2026, against the running codebase (`app/tools/page.tsx`, `app/life-phase-guides/[slug]/page.tsx` GUIDE_TOOLS, `proxy.ts`, and each route's `page.tsx`) -- not against a prior version of this document.

---

## Section 1: Core Product Tools

Tools available to every user regardless of guide. Not purchase-gated. Auth + active subscription required (standard middleware check), same as any non-public route.

| Tool | Status |
|---|---|
| Net Worth Tracker | built/live at /net-worth |
| Life Compass | built/live |
| Life Calendar | built/live |
| Krovos Guide | built/live |
| Financial Modes | built/live (detection and display layer; individual mode experiences are speced-not-built) |
| Household Budget Framework | speced-not-built |
| Fair Play household labor framework | built/live at /household-calendar |
| Debt Optimizer | built/live at /debt-optimizer |
| Paycheck Allocation Tool | built/live at /paycheck-allocation |
| Savings Vault System | built/live at /savings-vaults |
| Milestone Engine | built/live at /milestones |
| Emergency Fund Calculator | built/live at /emergency-fund |
| Goals | built/live at /goals (auth + active subscription required; intentionally excluded from /tools directory) |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Credit Card Engine | built/live at /credit-card-optimizer |
| Purchase Strategy Simulator | built/live at /purchase-strategy |
| Refinance Calculator | built/live at /refinance-calculator |
| Rent vs. Buy Calculator | built/live at /rent-vs-buy (deliberate lead magnet for Home Buying guide, stays public/ungated by design) |
| Open Enrollment Comparison | built/live at /open-enrollment-comparison |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy |
| Roth Conversion Modeling | built/live at /roth-conversion |
| Withdrawal Sequencing | built/live at /withdrawal-sequencing |
| Credit Health Dashboard | built/live at /credit-health |

**Moved out of Core, July 15, 2026:**
- **Inherited IRA Distribution Strategy** moved from Core to Inheritance-exclusive. Now gated (`GuideGate guideSlugs="inheritance"`) at `/inherited-ira-distribution`. See Section 3, Inheritance.
- **Retirement Trajectory** deprecated. `/retirement-trajectory` is now a redirect stub (`router.replace('/retirement')`). Its one distinct feature, the user-adjustable return rate, was folded into FreedomScenarios as an editable input field. See Section 2.

---

## Section 2: Retirement Hub (Core, Consolidated)

**Built:** July 15, 2026, at `/retirement`. Core product, not purchase-gated, but does require auth + active subscription (removed from `isPublicPath` along with every other tool touched in the July 15 build).

Consolidates FreedomScenarios, Safe Withdrawal Rate Calculator, CoastFI Calculator, Social Security Claiming Comparison, and RMD Deadline Tracker into a single hub page. FreedomScenarios remains embedded in Life View and Preview pages as well -- the hub is an additional home, not a replacement.

| Tool | Status |
|---|---|
| Retirement Hub (FI trajectory, single or dual retirement date, editable base return rate) | built/live at /retirement |
| Safe Withdrawal Rate Calculator | built/live at /swr |
| CoastFI Calculator | built/live at /coast-fi |
| Social Security Claiming Comparison | built/live at /social-security-comparison |
| RMD Deadline Tracker | built/live at /rmd-tracker |

**Auth note:** `/social-security-comparison` and `/rmd-tracker` are Retirement Hub tools with no purchase gate, same as `/swr` and `/coast-fi` and `/retirement` itself -- but unlike those three, they were swept out of `isPublicPath` in the July 15 "all 27, no exceptions" pass and now require sign-in before rendering. This is a real, intentional asymmetry, not an oversight; see CLAUDE.md Product Decisions Log ("Guide-Exclusive Tool Enforcement -- Resolved") for the full rationale. Revisit only with a product decision if the inconsistency needs resolving.

**FreedomScenarios read logic:** `app/components/FreedomScenarios.tsx` itself remains a prop-fed component -- it does not read Life Graph directly. The Life Graph read (age, portfolio, savings, spending, pension, Social Security) lives one level up, in `app/retirement/page.tsx`, which fetches Life Graph data and passes derived defaults down as props. Confirmed live-verified: field values differ from generic placeholders (e.g. annual savings populated from actual Life Graph data, not the $28,000 placeholder default).

### Money with Katie Wealth Planner: Benchmark Reference

Money with Katie's Wealth Planner validates the FreedomScenarios and MilestoneEngine direction and confirms the Update Engine's differentiation thesis. The benchmark-comparison UX pattern ("you're saving X%, guideline is Y%") should apply directly to the Paycheck Allocation Tool. Krovos extends this model by integrating life-phase context that a standalone planner cannot provide. This is a reference note, not a guide assignment.

---

## Section 3: Guide-Specific Tools

All tools in this section are purchase-gated via `GuideGate` (see Section 6) unless explicitly noted as shared with Core.

### Starting Out

**Early Career Starter Kit** (guide slug: `early-career`)

| Tool | Status |
|---|---|
| Early Career Decision Engine | built/live at /early-career |
| Student Loan Repayment Strategy Tool | built/live at /student-loan-strategy |
| First Apartment / Cost of Independence Calculator | built/live at /first-apartment-calculator |
| Debt Optimizer | built/live at /debt-optimizer (shared with Core) |
| Emergency Fund Calculator | built/live at /emergency-fund (shared with Core) |
| Credit Card Engine | built/live at /credit-card-optimizer (shared with Core) |

Early Career Decision Engine: five-step guided flow, 401k match optimization, Roth vs. traditional recommendation (2026 bracket-aware), emergency fund progress bars (1/3/6-month targets), credit building informational panel, consolidated summary screen.

Student Loan Repayment Strategy Tool: income-driven repayment plan comparison and PSLF eligibility modeling.

First Apartment / Cost of Independence Calculator: true cost of moving out the first time, modeled against take-home pay.

**Newlywed / Wedding and Joining Finances** (guide slug: `newlywed`)

| Tool | Status |
|---|---|
| Finance Structure Comparison | built/live at /finance-structure |
| Wedding Budget Allocator | built/live at /wedding-budget-allocator |
| Filing Status Tax Impact Comparator | built/live at /filing-status-comparator |
| Savings Vault System | built/live at /savings-vaults (shared with Core) |

Finance Structure Comparison: three-column comparison (fully combined, fully separate, hybrid). Per-partner savings breakdown, individual contribution rates, 5-year net worth projection at 6% growth. Highest-outcome structure highlighted in gold.

Wedding Budget Allocator: pre-wedding event budget planning across categories.

Filing Status Tax Impact Comparator: Married Filing Jointly vs. Married Filing Separately outcome modeling given both partners' incomes.

**Immigration and Finances** (guide slug: `immigration`)

| Tool | Route | Status |
|---|---|---|
| Authorization-Gap Bridge Planner | /auth-gap | built, live |
| Path-to-Residency Cost Planner | /residency-costs | built, live |
| Credit-Building-From-Zero | /credit-building | built, live |
| Household Budget Framework | -- | speced-not-built |
| Trusted Pro Network | -- | speced-not-built |
| Credit Health Dashboard | -- | backlog |

Authorization-Gap Bridge Planner: visa type, USCIS processing time estimate (user-entered, links to egov.uscis.gov/processing-times/), household spending, continuing income, savings. Three-phase timeline (filing / waiting / authorized). Shows monthly gap, runway, flags when USCIS estimate exceeds savings.

Path-to-Residency Cost Planner: hardcoded USCIS fee table, attorney fee input, timeline in years, monthly savings target, optional progress tracking.

Credit-Building-From-Zero: country-of-origin field for Nova Credit eligibility, three starting points, authorized-user option as parallel accelerant, milestone sequence from no-history to conventional credit access. Separate from /credit-rebuilding (divorce/widowhood).

---

### Building a Household

**Home Buying** (guide slug: `home-buying`)

*Resolves the July 13 Criterion 3 FAIL -- this guide previously had only Core-shared tools (rent-vs-buy, refinance-calculator). It now has four genuinely exclusive tools.*

| Tool | Status |
|---|---|
| Down Payment vs. PMI Strategy Tool | built/live at /down-payment-pmi-strategy |
| Total Cost of Homeownership Calculator | built/live at /homeownership-cost-calculator |
| Closing Cost and Cash-to-Close Estimator | built/live at /closing-cost-estimator |
| Escrow vs. Self-Managed Tax and Insurance Comparator | built/live at /escrow-comparator |
| Rent vs. Buy Calculator | built/live at /rent-vs-buy (shared with Core, deliberately public lead magnet, NOT gated) |
| Refinance Calculator | built/live at /refinance-calculator (shared with Core) |

Down Payment vs. PMI Strategy Tool: larger down payment to avoid PMI vs. investing the difference, factoring in PMI removal timeline.

Total Cost of Homeownership Calculator: property tax growth, maintenance percentage, insurance, HOA.

Closing Cost and Cash-to-Close Estimator: total upfront cash needed beyond the down payment.

Escrow vs. Self-Managed Tax and Insurance Comparator: lender-managed escrow vs. self-managing via HYSA. States plainly, before showing any numbers, that not all lenders permit waiving escrow and some charge a rate adjustment for the option.

**New Parent / Pregnancy, Baby and Family Leave** (guide slug: `new-parent`)

| Tool | Status |
|---|---|
| Parental Leave Navigator | built/live at /parental-leave-navigator |
| Childcare Bubble Calculator | built/live at /childcare-bubble |
| Children's Planning Hub | built/live at /childrens-planning |
| 529 Projection Tool | built/live at /college-529 (moved here from College Planning, July 15 -- see below) |
| Life Insurance Needs Calculator | built/live at /life-insurance-needs (shared with Estate Planning, OR-logic) |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy (shared with Core) |
| Open Enrollment Comparison | built/live at /open-enrollment-comparison (shared with Core) |
| Savings Vault System | built/live at /savings-vaults (shared with Core) |
| Document Vault | speced-not-built |
| Fair Play household labor framework | built/live in Core; surfaced here at the moment it matters most |

**529 Projection Tool move, confirmed July 15, 2026:** `/college-529` gates exclusively to `new-parent` (`GuideGate guideSlugs="new-parent"`, single guide, not an array). It is fully removed from College Planning -- not cross-listed anywhere in `app/tools/page.tsx` or the GUIDE_TOOLS mapping. Child age, school type, current balance, monthly contribution, expected return rate. Monthly compound projection loop, year-by-year table, 3-summary cards, color-coded coverage bar. 2026 base costs.

**College Planning** (guide slug: `college-planning`)

*529 Projection Tool removed from this guide (moved to New Parent-exclusive, above). Replaced with two new exclusive tools.*

| Tool | Status |
|---|---|
| Financial Aid Award Comparison Tool | built/live at /financial-aid-comparison |
| Student Loan Optimizer for Parents | built/live at /parent-loan-optimizer |
| FAFSA Remarriage-Timing Planner | built/live at /fafsa-remarriage-timing (shared with Blended Family, OR-logic) |
| Tax Planning Toolkit | backlog (state 529 deduction comparison) |

Financial Aid Award Comparison Tool: multiple schools' aid offers side by side, net cost after aid.

Student Loan Optimizer for Parents: Parent PLUS vs. private loan vs. cash comparison, general audience.

FAFSA Remarriage-Timing Planner: custodial parent income and assets, new spouse income and assets, college start year, household size, number of children needing aid. Runs SAI calculation twice (stepparent included vs. excluded). Side-by-side comparison with annual and 4-year aid impact difference.

BACKLOG: state 529 deduction comparison (requires per-state deduction data; Tax Planning Toolkit integration needed).

---

### Career

**Career Transition** (guide slug: `career-transition`)

*Resolves the July 13 Criterion 3 FAIL -- this guide previously had only Core-shared tools. It now has three genuinely exclusive tools.*

| Tool | Status |
|---|---|
| Severance and Benefits Bridge Calculator | built/live at /severance-bridge-calculator |
| Offer Comparison Tool | built/live at /offer-comparison |
| COBRA vs. Marketplace Coverage Comparator | built/live at /cobra-vs-marketplace |
| Emergency Fund Calculator | built/live at /emergency-fund (shared with Core) |
| Credit Health Dashboard | built/live at /credit-health (shared with Core) |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy (shared with Core) |
| Open Enrollment Comparison | built/live at /open-enrollment-comparison (shared with Core) |

Severance and Benefits Bridge Calculator: severance plus COBRA duration, unemployment timing, the real monthly gap.

Offer Comparison Tool: total comp comparison for someone transitioning into a new role.

COBRA vs. Marketplace Coverage Comparator: the specific health continuation decision.

BENCHMARK NOTE: WealthSpott's equity-discounting offer comparison model is the confirmed bar for a future enhancement to offer comparison logic in this guide.

**Starting a Business** (guide slug: `starting-a-business`)

| Tool | Status |
|---|---|
| Business Entity Structure Comparison | built/live at /entity-structure-comparison |
| Quarterly Tax Calculator | built/live at /quarterly-tax |
| Retirement Plan Comparison (Solo 401k / SEP / SIMPLE) | built/live at /retirement-plan-comparison |

Business Entity Structure Comparison: LLC vs. S-corp vs. sole proprietorship tax implications.

Quarterly Tax Calculator: SE income + filing status -> SE tax + income tax via TaxEngine -> quarterly payment schedule. Safe harbor methods: 90% current-year (default), 100% prior-year, or equal installments.

Retirement Plan Comparison: Solo 401k / SEP-IRA / SIMPLE IRA contribution limits, eligibility, complexity, and recommendation logic.

**Gig Work / Freelance Income** (guide slug: `gig-work-freelance-income`)

*Note: guide slug is `gig-work-freelance-income`, not `gig-work`. A slug mismatch here (both in GuideGate and in the GUIDE_TOOLS lookup keyed by URL param) was found and fixed July 15, 2026 -- the guide page was silently rendering zero attached tools until corrected.*

| Tool | Status |
|---|---|
| Multi-Platform Income and Expense Tracker | built/live at /multi-platform-income-tracker |
| Gig Income Engine | built/live at /gig-engine |
| Runway Calculator | built/live at /runway |

Multi-Platform Income and Expense Tracker: aggregate income across platforms plus deductible expense tracking.

Gig Income Engine: income history (multi-entry, multi-platform), baseline-from-lowest-month detection, buffer routing, effective hourly rate, platform comparison, income variance tab.

Runway Calculator: liquid savings + monthly expenses -> months of runway if income stops. Optional trailing income inputs compute partial-income extension. Color-coded at <3mo / 3-6mo / 6mo+ thresholds.

---

### Life Changes

**Divorce and Transition** (guide slug: `divorce`)

| Tool | Status |
|---|---|
| Debt Optimizer | built/live at /debt-optimizer (shared with Core) |
| Emergency Fund Priority Tool | built/live at /emergency-fund-priority (shared with Widowhood, OR-logic) |
| Single Income Budget | built/live at /income-stabilization (shared with Widowhood, OR-logic) |
| Credit Rebuilding Timeline | built/live at /credit-rebuilding (shared with Widowhood, OR-logic) |
| QDRO Readiness Prep | built/live at /qdro-readiness |
| QDRO Tracker and Award Estimator | built/live at /qdro-tracker |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |

Emergency Fund Priority Tool: independent savings (excludes joint/contested funds) + monthly expenses -> three milestones, urgency classification, optional income input for timeline estimate.

Single Income Budget: prior combined income + current single income + eight expense categories -> gap/surplus summary, per-category flags, expandable guidance notes. Consolidates Income Stabilization Planner and Single Income Adjustment Tool.

Credit Rebuilding Timeline: three situation inputs (individual history / authorized user / joint-only) + joint account status + score range + optional joint mortgage -> personalized milestone list.

QDRO Readiness Prep: account type multi-select. IRA path clarifies no QDRO needed. Non-IRA path: QDRO status, decree status, timeline, status assessment, attorney checklist, glossary. Prominent disclaimer above first input.

QDRO Tracker and Award Estimator: for decree-finalized situations with a specified account division. Defined-contribution and defined-benefit pension modeling, coverture fraction shown transparently, government/military agency-specific routing, survivor benefit alert.

**Caregiving** (guide slug: `caregiving`)

| Tool | Status |
|---|---|
| Care Options Cost Comparison | built/live at /care-options-cost-comparison |
| Caregiver Impact on Retirement Calculator | built/live at /caregiver-retirement-impact |
| Caregiver Ledger | built/live at /caregiver-ledger |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built (includes geriatric care manager referral) |
| Household Budget Framework | speced-not-built |

Care Options Cost Comparison: in-home vs. assisted living vs. nursing home against assets and Medicaid spend-down thresholds.

Caregiver Impact on Retirement Calculator: models the long-term retirement savings gap from reduced hours or leaving the workforce to caregive. Links to the Retirement hub.

**Disability Insurance and Personal Illness** (guide slug: `disability-planning`)

*Note: guide slug is `disability-planning`, not `disability`. Same slug-mismatch bug as Gig Work above, found and fixed July 15, 2026.*

| Tool | Status |
|---|---|
| Benefit Coordination Calculator | built/live at /benefit-coordination-calculator |
| Out-of-Pocket Medical Cost Planner | built/live at /medical-cost-planner |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy (shared with Core) |
| LTD Analyzer | built/live at /ltd-analyzer |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Tax Planning Toolkit | backlog (mixed-funding tax treatment calculation) |

Benefit Coordination Calculator: how STD, LTD, and SSDI stack and offset each other.

Out-of-Pocket Medical Cost Planner: ongoing medical expense projection against HSA/FSA and out-of-pocket max.

BACKLOG: employer LTD vs. total-compensation replacement rate analyzer.

**Widowhood** (guide slug: `widowhood`)

| Tool | Status |
|---|---|
| First-Year Financial Checklist | built/live at /first-year-financial-checklist |
| Emergency Fund Priority Tool | built/live at /emergency-fund-priority (shared with Divorce) |
| Single Income Budget | built/live at /income-stabilization (shared with Divorce) |
| Credit Rebuilding Timeline | built/live at /credit-rebuilding (shared with Divorce) |
| Social Security Survivor-Benefit Optimizer | built/live at /ss-survivor-optimizer |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Household Budget Framework | speced-not-built |

First-Year Financial Checklist: practical task tracker, accounts to notify/transfer, benefits to claim, timeline. Dignity-preserving tone, no assumption of the user's emotional state.

Social Security Survivor-Benefit Optimizer: claiming-strategy logic, grief-aware UX. **Gated to `widowhood` alone, single-guide check, confirmed NOT OR'd against Retirement** (Retirement no longer sells separately, so an OR would be silently, quietly wrong -- confirmed correct as of July 15, 2026).

**Remarriage and Blended Families** (guide slug: `blended-family`)

*Note: guide slug is `blended-family`, not `remarriage`. Same slug-mismatch bug as Gig Work and Disability above, found and fixed July 15, 2026.*

| Tool | Status |
|---|---|
| Blended Family Conflict Checker | built/live at /blended-family-conflict-checker |
| FAFSA Remarriage-Timing Planner | built/live at /fafsa-remarriage-timing (shared with College Planning, OR-logic) |
| QTIP vs. Bypass Trust Decision Walkthrough | built/live at /qtip-bypass-decision (shared with Estate Planning, OR-logic) |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Household Budget Framework | speced-not-built |

Blended Family Conflict Checker: flags common conflict scenarios before a trust structure decision.

QTIP vs. Bypass Trust Decision Walkthrough: five-step sequential flow. Three outcomes: QTIP trust likely fits, bypass trust likely fits, or will with bequests may suffice. Generates a personalized attorney question list. State-specific alerts for estate and inheritance tax states.

---

### Later Life

**Retirement** (guide slug: `retirement` -- no longer sells separately as of July 15, 2026; tools consolidated into the Retirement Hub, Section 2)

| Tool | Status |
|---|---|
| Retirement Hub (includes FreedomScenarios, SWR, CoastFI, Social Security Claiming Comparison, RMD Deadline Tracker) | built/live at /retirement |
| Roth Conversion Modeling | built/live at /roth-conversion (shared with Core) |
| Withdrawal Sequencing | built/live at /withdrawal-sequencing (shared with Core) |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy (shared with Core) |
| Open Enrollment Comparison | built/live at /open-enrollment-comparison (shared with Core) |
| Social Security Survivor-Benefit Optimizer | built/live at /ss-survivor-optimizer (Widowhood-exclusive only as of July 15 -- see Widowhood section; no longer gated to Retirement) |

**Retirement Trajectory, formally deprecated July 15, 2026:** `/retirement-trajectory` now redirects to `/retirement`. Its one distinct feature (user-adjustable base return rate) is now an editable input field in FreedomScenarios.

---

### Estate Planning (guide slug: `estate-planning`)

| Tool | Status |
|---|---|
| Beneficiary Designation Audit | built/live at /beneficiary-designation-audit |
| Estate Tax Threshold and State Exposure Checker | built/live at /estate-tax-checker |
| QTIP vs. Bypass Trust Decision Walkthrough | built/live at /qtip-bypass-decision (shared with Blended Family) |
| Life Insurance Needs Calculator | built/live at /life-insurance-needs (shared with New Parent) |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy (shared with Core) |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built (includes notary and employer-benefit-check additions) |

Beneficiary Designation Audit: checklist review of beneficiary designations across accounts.

Estate Tax Threshold and State Exposure Checker: federal and state-specific estate/inheritance tax exposure.

---

### Inheritance (guide slug: `inheritance`)

*Resolves the July 13 Criterion 3 FAIL -- this guide previously had only one tool, itself only just moved from Core. It now has three genuinely exclusive tools.*

| Tool | Status |
|---|---|
| Windfall Allocation Tool | built/live at /windfall-allocation |
| Step-Up in Basis and Capital Gains Estimator | built/live at /step-up-basis-calculator |
| Inherited IRA Distribution Strategy | built/live at /inherited-ira-distribution (moved from Core, July 15, 2026) |

Windfall Allocation Tool: non-retirement inherited assets, debt payoff vs. investing vs. emergency fund vs. step-up-in-basis awareness.

Step-Up in Basis and Capital Gains Estimator: a widely misunderstood, inheritance-specific tax concept.

Inherited IRA Distribution Strategy: SECURE Act 10-year rule. Level, front-loaded, and back-loaded strategy comparison with year-by-year tax modeling.

---

## Section 4: Confirmed Tool Gap Backlog

0 net-new tools remaining. All confirmed gaps are now built, including the 27 tools built July 15, 2026 across all 17 guides and the Retirement Hub.

---

## Section 5: Tools in Core Product That Appear Across Many Guides

Credit Card Engine (/credit-card-optimizer, built/live), Purchase Strategy Simulator (/purchase-strategy, built/live), and Refinance Calculator (/refinance-calculator, built/live) are situational rather than guide-bound. They trigger on an actual transaction or event, not a life phase. Surface them as core product features that guides can reference contextually rather than assigning them to specific guides.

Credit Health Dashboard (/credit-health, built/live) is a hub that provides a credit snapshot, utilization optimizer, FICO-band key actions, and routing to /credit-card-optimizer and /credit-rebuilding.

---

## Section 6: Enforcement (July 15, 2026)

All 27 new tools plus all 24 pre-existing guide-exclusive tools are wrapped in `GuideGate` (`app/components/GuideGate.tsx`), which checks `user_guide_purchases`, falls back to `/api/household/guide-access` for connected-subscriber inheritance, and redirects unauthenticated visitors to sign-in. Every guide-exclusive tool route is also removed from `isPublicPath` in `proxy.ts`, so server-side auth + subscription checks apply uniformly underneath the client-side purchase gate.

Shared tools use OR-logic (`guideSlugs` as an array): `life-insurance-needs` (new-parent OR estate-planning), `fafsa-remarriage-timing` (college-planning OR blended-family), `qtip-bypass-decision` (blended-family OR estate-planning), `emergency-fund-priority` / `income-stabilization` / `credit-rebuilding` (divorce OR widowhood each). `ss-survivor-optimizer` is single-guide (widowhood only), confirmed not OR'd against Retirement.

Full detail on the enforcement audit, the July 15 fix, and live verification results: `docs/product/guide-approval-criteria.md`.

---

## Doc-vs-Reality Discrepancies from July 13 Audit -- All Resolved July 15, 2026

The four discrepancies flagged in the July 13 audit (`docs/product/guide-approval-criteria.md`) are resolved:

1. **ss-survivor-optimizer BACKLOG listing** -- resolved. Now correctly listed as built/live under Widowhood only (Retirement no longer sells separately, so the guide is not listed twice).
2. **retirement-trajectory placement mismatch** -- resolved by deprecation. The route no longer exists as a standalone tool; it redirects to `/retirement`.
3. **life-insurance-needs missing from Estate Planning** -- resolved. Listed under both New Parent and Estate Planning above, with OR-logic gating confirmed live.
4. **Three guides with no guide-exclusive tool (Home Buying, Career Transition, Inheritance)** -- resolved. Each now has 3-4 genuinely exclusive tools built July 15, 2026 (see their sections above). All three now pass Criterion 3.
