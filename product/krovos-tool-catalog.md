# Krovos Tool Catalog

**Established:** July 15, 2026
**Status:** Canonical reference for every tool in the running application. Replaces reliance on scattered mapping-doc entries -- when this catalog and `navigation-engine-tool-guide-mapping.md` disagree, re-audit against the running code, do not assume either doc is current without checking.

This is the complete list of every tool live in the Krovos application, grouped by access tier: Core (every subscriber, never guide-gated), Retirement Hub (Core, consolidated), and Guide-Exclusive (purchase-gated per guide, see `docs/product/guide-approval-criteria.md` for the enforcement audit).

---

## Core Product (available to every subscriber, never guide-gated)

| Tool | Route | What it does |
|---|---|---|
| Net Worth Tracker | /net-worth | Aggregates assets and liabilities into a running net worth view |
| Debt Payoff Planner | /debt-payoff-planner | Starts from the user's goal (fastest payoff, lowest monthly commitment, or user-selected strategy), then models payoff order, timing, and interest across multiple debts |
| Paycheck Planner | /paycheck-planner | Splits each paycheck across savings, bills, and goals |
| Savings Goals | /savings-goals | Named, goal-based savings buckets |
| Emergency Fund Calculator | /emergency-fund | Calculates target emergency fund size for your situation |
| Credit Health | /credit-health | Tracks credit score factors over time |
| Credit Card Optimizer | /credit-card-optimizer | Card selection and ongoing usage optimization |
| Purchase Planner | /purchase-planner | Models timing and financing for large purchases |
| Refinance Calculator | /refinance-calculator | Models refinance break-even math for an existing mortgage |
| Rent vs. Buy Calculator | /rent-vs-buy | Basic rent-vs-buy cost comparison; also Home Buying's lead magnet |
| Insurance Gap Checker | /insurance-gap-checker | Checks for coverage gaps across insurance types |
| Open Enrollment Comparison | /open-enrollment-comparison | Compares employer benefits plan options |
| Roth Conversion Calculator | /roth-conversion-calculator | Models the tax impact of a Roth conversion |
| Retirement Withdrawal Planner | /retirement-withdrawal-planner | Models account drawdown order in retirement |
| Life Labor | /life-labor | Household task division for couples, with effort/energy labels, low-effort quick access, a persistent household facts layer, income/home-labor correlation, hour buckets, and season-based rebalancing |
| Milestone Tracker | /milestone-tracker | Tracks progress and achievement across financial goals |
| Life Goals | /life-goals | Create, track, and log contributions toward named financial goals |

Life Labor (built July 15-16, 2026, renamed and rebuilt from the original Fair Play model, see CLAUDE.md's Product Decisions Log): `/household-calendar` now 301-redirects to `/life-labor`.

Debt Payoff Planner (renamed July 17, 2026, twice in the same day): trademark screen confirmed "Debt Optimizer" was an exact-name match to an actively sold consumer finance app (Solve Finance's "Debt Optimizer"). First renamed to "Debt Strategy Engine," then corrected to "Debt Payoff Planner" the same day once it was clarified that the "Engine" suffix pattern (as it existed at the time, in tools like the original Milestone Engine, Credit Card Engine, Early Career Decision Engine, and Gig Income Engine) was itself being eliminated catalog-wide in an upcoming renaming pass, not adopted as precedent. `/debt-optimizer` 301-redirects directly to `/debt-payoff-planner`, no intermediate hop through the briefly-used `/debt-strategy-engine` name, which was never committed or deployed.

Full catalog-wide renaming pass completed July 17, 2026 (see the "July 17, 2026 Renaming Pass" note at the bottom of this document): every tool name and route in this catalog now reflects the final, current state. Do not treat any tool name as fixed going forward without checking this catalog first.

---

## Retirement Hub (Core, consolidated at /retirement)

| Tool | Route | What it does |
|---|---|---|
| Retirement Hub | /retirement | Consolidates all tools below into one page. Single or dual retirement date FI trajectory (Best/Likely/Worst scenarios), editable base return rate. |
| Investment Priority | /investment-priority | Creates and saves a next-dollar list across stability, debt, match, HSA, workplace plans, IRA paths, owner plans, partner opportunities, and taxable investing; labels missing evidence instead of claiming an incomplete sequence is optimal. |
| Retirement Scenarios | (embedded in /retirement, /life-view, /preview; internal component `FreedomScenarios`) | Three-scenario FI projection with dual-retirement-date bridge modeling |
| Safe Withdrawal Rate | /swr | Age-banded sustainable withdrawal rate with bridge modeling |
| CoastFI Calculator | /coast-fi | Coast-to-target FI projection |
| Social Security Comparison | /social-security-comparison | Models claiming Social Security at two or three ages side by side |
| RMD Deadline Tracker | /rmd-tracker | Tracks required minimum distribution deadlines starting at 73 |

`/retirement-trajectory` is deprecated (redirects to `/retirement`); its adjustable-return-rate feature is absorbed into Retirement Scenarios as an editable input.

**Auth note:** `/retirement`, `/swr`, and `/coast-fi` are public (no sign-in required, no purchase gate). `/social-security-comparison` and `/rmd-tracker` require sign-in + active subscription but carry no purchase gate -- an intentional asymmetry from the July 15, 2026 "all 27 tools out of isPublicPath, no exceptions" decision. See CLAUDE.md Product Decisions Log.

---

## Guide-Exclusive Tools (purchase-gated per guide via GuideGate)

### Early Career (guide slug: `early-career`)
- First Job Decisions (/first-job-decisions): saved benefit, employer-match, first-paycheck, and retirement-tax-treatment decision record
- Student Loan Repayment Planner (/student-loan-repayment-planner): saves current official federal or private repayment options, loan-timing facts, and confirmed PSLF progress without recreating changing eligibility formulas
- First Apartment Calculator (/first-apartment-calculator): monthly-margin, written move-in-cost, protected-cash, and lease-readiness record

### Newlywed (guide slug: `newlywed`)
- Joint Finances Comparison (/joint-finances-comparison): three-column comparison of combined/separate/hybrid account models
- Wedding Budget Planner (/wedding-budget-planner): pre-wedding event budget planning
- Filing Status Comparison (/filing-status-comparison): MFJ vs. MFS tax outcome modeling

### Immigration (guide slug: `immigration`)
- Work Authorization Gap Planner (/work-authorization-gap-planner): plans finances through a work-authorization gap
- Green Card Cost Planner (/green-card-cost-planner): plans for green card/citizenship process costs
- Credit Builder (/credit-builder): builds US credit history with no prior history

### Home Buying (guide slug: `home-buying`)
- Down Payment vs. PMI Comparison (/down-payment-vs-pmi-comparison): compares a larger down payment against investing the difference
- Homeownership Cost (/homeownership-cost-calculator): ongoing costs beyond the mortgage payment
- Closing Cost Calculator (/closing-cost-calculator): total upfront cash needed to close
- Escrow Comparison (/escrow-comparison): lender-managed escrow vs. self-managed HYSA approach

### New Parent (guide slug: `new-parent`)
- Parental Leave Planner (/parental-leave-planner): models income during parental leave
- Childcare Bubble Calculator (/childcare-bubble): projects childcare costs by type and region
- Child Costs Planner (/child-costs-planner): coordinates child-related planning tasks
- 529 Projection Calculator (/529-projection-calculator, moved from College Planning July 15, 2026): long-horizon 529 savings projection
- Life Insurance Calculator (/life-insurance-calculator, shared with Estate Planning, OR-logic): sizes life insurance need with dependents

### College Planning (guide slug: `college-planning`)
- Financial Aid Comparison (/financial-aid-comparison): normalizes school offers, separates grants/work/loans, makes four-year repetition assumptions explicit, and saves the selected funding record
- Parent Loan Comparison (/parent-loan-optimizer): compares current entered PLUS and private disclosures with cash while protecting a household cash floor and saving the parent decision
- FAFSA Remarriage Planner (/fafsa-remarriage-timing, shared with Blended Family, OR-logic): saves the award-year-specific official parent/contributor, account, deadline, and special-circumstance workflow without estimating aid from marriage timing

### Career Transition (guide slug: `career-transition`)
- Severance Bridge Calculator (/severance-bridge-calculator): models severance/COBRA/unemployment income bridge
- Offer Comparison (/offer-comparison): total comp comparison for a new role
- COBRA vs. Marketplace Comparison (/cobra-vs-marketplace-comparison): the health coverage continuation decision

### Starting a Business (guide slug: `start-business`)
- Business Structure Comparison (/business-structure-comparison): LLC vs. S-corp vs. sole proprietorship tax comparison
- Quarterly Tax Calculator (/quarterly-tax): estimates quarterly self-employment tax payments
- Retirement Plan Comparison (/retirement-plan-comparison): Solo 401k vs. SEP vs. SIMPLE comparison
- Revenue Target (/revenue-target, renamed July 20, 2026; `/w2-exit-milestone-calculator` redirects): finds the monthly or annual business revenue needed to support the user's next step. It auto-detects which of three scenarios applies (has a W-2 to replace, has no earned income but a partner does, or already has steady 1099/gig income to formalize) from Life Graph data and renders only that framing with real numbers, no self-selection required.

### Gig Work (guide slug: `gig-work`)
- Gig Income Tracker (/gig-income-tracker): aggregates income and deductible expenses across gig platforms
- Gig Paycheck Calculator (/gig-paycheck-calculator): smooths irregular gig income
- Savings Runway Calculator (/savings-runway-calculator): models how long savings last during income gaps

### Divorce (guide slug: `divorce`)
- Emergency Fund Rebuilder (/emergency-fund-rebuilder, shared with Widowhood, OR-logic): prioritizes rebuilding an emergency fund post-separation
- Single Income Planner (/single-income-planner, shared with Widowhood, OR-logic): rebuilds a budget for one income
- Credit Rebuilding Planner (/credit-rebuilding-planner, shared with Widowhood, OR-logic): rebuilds credit after joint-debt separation
- QDRO Readiness Checklist (/qdro-readiness-checklist): prepares for dividing retirement accounts via QDRO
- QDRO Tracker (/qdro-tracker): tracks QDRO status and estimates award

### Caregiving (guide slug: `caregiving`)
- Care Options Comparison (/care-options-comparison): compares in-home/assisted living/nursing home costs against assets
- Caregiving Retirement Impact (/caregiver-retirement-impact-calculator): models the retirement savings gap from reduced work or leaving the workforce to caregive
- Caregiver Cost Tracker (/caregiver-cost-tracker): splits and tracks care costs among siblings

### Disability (guide slug: `disability`)
- Disability Benefits Calculator (/benefit-coordination-calculator): builds a member-confirmed benefit timeline and distinguishes approved, pending, offset, and unavailable income
- Medical Cost Planner (/medical-cost-planner): plans ongoing medical costs against HSA/FSA balances
- LTD Coverage Checker (/ltd-coverage-checker): analyzes long-term disability coverage adequacy

### Widowhood (guide slug: `widowhood`)
- Widowhood Checklist (/widowhood-first-year-checklist): sequences authority, cash, claim, coverage, estate, and professional-handoff tasks across the first year
- Survivor Benefit Calculator (/survivor-benefit-calculator, Widowhood-exclusive only -- no longer shared with Retirement, which does not sell separately): compares member-confirmed survivor-benefit scenarios without recommending a claiming strategy
- Shares Divorce's Emergency Fund Priority, Single Income Planner, and Credit Rebuilding tools

### Blended Family (guide slug: `blended-family`)
- Blended Family Inheritance (/blended-family-inheritance-checklist): flags title, beneficiary, child, spouse-support, and prior-obligation conflicts for attorney preparation
- Shares College Planning's FAFSA Remarriage Planner
- QTIP vs. Bypass Comparison (/qtip-vs-bypass-comparison, shared with Estate Planning, OR-logic): prepares goals, tradeoffs, and questions for counsel without selecting a trust

### Estate Planning (guide slug: `estate-planning`)
- Beneficiary Checker (/beneficiary-checker): reviews beneficiary designations across accounts
- Estate Tax Checker (/estate-tax-checker): checks federal and state estate tax exposure
- Shares Remarriage's QTIP/Bypass tool and New Parent's Life Insurance Calculator

### Inheritance (guide slug: `inheritance`)
- Inheritance Planner (/inheritance-planner): allocates non-retirement inherited assets across goals
- Step-Up Basis Calculator (/step-up-basis-calculator): estimates capital gains impact using step-up in basis
- Inherited IRA Planner (/inherited-ira-planner, moved from Core July 15, 2026): models inherited retirement account distribution timing

---

## Not a Tool

Life Gaps (/life-gaps): a lead-magnet quiz, not a calculator or planner, stays public by design.

---

## Slug Normalization History

Three guide slugs were originally mismatched between code and production. That history is superseded by `20260720_guide_name_slug_normalization.sql`. The canonical post-migration slugs are:

| Guide display name | Correct slug | Wrong slug previously used |
|---|---|---|
| Disability | `disability` | `disability-planning` |
| Gig Work | `gig-work` | `gig-work-freelance-income` |
| Blended Family | `blended-family` | `remarriage` |
| Starting a Business | `start-business` | `starting-a-business` |

All code, catalog entries, content migrations, and purchase gates must use the canonical post-migration slug. Do not use the historical values in new work.

**Third instance found and fixed, July 17, 2026:** `app/tools/page.tsx`'s own category array had the identical three wrong slugs, never touched in the July 15 pass since that pass only checked `GuideGate` and `GUIDE_TOOLS`. Its "Guide" link for Disability, Gig Work, and Blended Family was silently pointing at a non-existent slug, falling through to the guide marketplace instead of the real guide page. Fixed as part of the `/tools` locked-tool visual treatment rebuild the same day.

Guide slugs are internal identifiers stored in `life_phase_guides.slug` and are unaffected by the July 17, 2026 tool renaming pass below -- only tool display names and tool routes changed, not guide slugs.

---

## July 17, 2026 Renaming Pass

Approximately 50 tools were renamed in a single pass, per an explicit product decision that no existing tool name (including the "Engine" suffix used in Milestone Engine, Credit Card Engine, Early Career Decision Engine, and Gig Income Engine) should be treated as a naming precedent for anything going forward. Every tool name and route in this catalog above already reflects the final, current state -- this section exists only as a historical record of what changed and to confirm the redirect pattern.

Every route change got a single-hop 301 redirect from the old route directly to the new route (no intermediate hops), added to `next.config.ts`. Guide-exclusive tool routes were confirmed to remain correctly excluded from `proxy.ts`'s `isPublicPath` (per the July 15, 2026 enforcement decision) both before and after the rename -- renaming a route does not change its enforcement status.

A handful of tools kept their existing route because the route already matched the new name in kebab-case (for example, Credit Card Optimizer was already at `/credit-card-optimizer`) -- only the display name needed updating in those cases, not a route change or redirect. Several others (Financial Aid Comparison, Parent Loan Comparison, Severance Bridge Calculator, Offer Comparison, QDRO Tracker, Medical Cost Planner, Estate Tax Checker, Step-Up Basis Calculator) similarly kept their existing routes with only a display-name change.

Verified: `npm run build` clean, all new and redirected routes confirmed live via direct navigation and `curl -I` for single-hop 308 redirects, `/tools` page confirmed rendering every new name correctly via live browser check, zero old tool names remaining anywhere in the app or in this docs folder (internal lib file names and code comments excepted, since those are not user-facing).
