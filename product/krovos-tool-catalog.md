# Krovos Tool Catalog

**Established:** July 15, 2026
**Status:** Canonical reference for every tool in the running application. Replaces reliance on scattered mapping-doc entries -- when this catalog and `navigation-engine-tool-guide-mapping.md` disagree, re-audit against the running code, do not assume either doc is current without checking.

This is the complete list of every tool live in the Krovos application, grouped by access tier: Core (every subscriber, never guide-gated), Retirement Hub (Core, consolidated), and Guide-Exclusive (purchase-gated per guide, see `docs/product/guide-approval-criteria.md` for the enforcement audit).

---

## Core Product (available to every subscriber, never guide-gated)

| Tool | Route | What it does |
|---|---|---|
| Net Worth Tracker | /net-worth | Aggregates assets and liabilities into a running net worth view |
| Debt Optimizer | /debt-optimizer | Models payoff order and strategy across multiple debts |
| Paycheck Allocation Tool | /paycheck-allocation | Splits each paycheck across savings, bills, and goals |
| Savings Vault System | /savings-vaults | Named, goal-based savings buckets |
| Emergency Fund Calculator | /emergency-fund | Calculates target emergency fund size for your situation |
| Credit Health Dashboard | /credit-health | Tracks credit score factors over time |
| Credit Card Engine | /credit-card-optimizer | Card selection and ongoing usage optimization |
| Purchase Strategy Simulator | /purchase-strategy | Models timing and financing for large purchases |
| Refinance Calculator | /refinance-calculator | Models refinance break-even math for an existing mortgage |
| Rent vs. Buy Calculator | /rent-vs-buy | Basic rent-vs-buy cost comparison; also Home Buying's lead magnet |
| Insurance Adequacy Analyzer | /insurance-adequacy | Checks for coverage gaps across insurance types |
| Open Enrollment Comparison | /open-enrollment-comparison | Compares employer benefits plan options |
| Roth Conversion Modeling | /roth-conversion | Models the tax impact of a Roth conversion |
| Withdrawal Sequencing | /withdrawal-sequencing | Models account drawdown order in retirement |
| Household Calendar | /household-calendar | Shared family calendar |
| Milestone Engine | /milestones | Tracks progress and achievement across financial goals |
| Goals | /goals | Create, track, and log contributions toward named financial goals |

**Household labor tool** (name pending): task/labor division for couples, scope being elevated past the original Fair Play model. Not yet built -- needs a separate formal scoping pass before anything is renamed or built. See CLAUDE.md's Note on the Household Labor Tool.

---

## Retirement Hub (Core, consolidated at /retirement)

| Tool | Route | What it does |
|---|---|---|
| Retirement Hub | /retirement | Consolidates all tools below into one page. Single or dual retirement date FI trajectory (Best/Likely/Worst scenarios), editable base return rate. |
| FreedomScenarios | (embedded in /retirement, /life-view, /preview) | Three-scenario FI projection with dual-retirement-date bridge modeling |
| Safe Withdrawal Rate Calculator | /swr | Age-banded sustainable withdrawal rate with bridge modeling |
| CoastFI Calculator | /coast-fi | Coast-to-target FI projection |
| Social Security Claiming Comparison | /social-security-comparison | Models claiming Social Security at two or three ages side by side |
| RMD Deadline Tracker | /rmd-tracker | Tracks required minimum distribution deadlines starting at 73 |

`/retirement-trajectory` is deprecated (redirects to `/retirement`); its adjustable-return-rate feature is absorbed into FreedomScenarios as an editable input.

**Auth note:** `/retirement`, `/swr`, and `/coast-fi` are public (no sign-in required, no purchase gate). `/social-security-comparison` and `/rmd-tracker` require sign-in + active subscription but carry no purchase gate -- an intentional asymmetry from the July 15, 2026 "all 27 tools out of isPublicPath, no exceptions" decision. See CLAUDE.md Product Decisions Log.

---

## Guide-Exclusive Tools (purchase-gated per guide via GuideGate)

### Early Career Starter Kit (guide slug: `early-career`)
- Early Career Decision Engine (/early-career): five-step guided flow covering 401k match optimization and first-job decisions
- Student Loan Repayment Strategy Tool (/student-loan-strategy): income-driven repayment and PSLF eligibility modeling
- First Apartment / Cost of Independence Calculator (/first-apartment-calculator): true cost of moving out for the first time

### Newlywed / Wedding and Joining Finances (guide slug: `newlywed`)
- Finance Structure Comparison (/finance-structure): three-column comparison of combined/separate/hybrid account models
- Wedding Budget Allocator (/wedding-budget-allocator): pre-wedding event budget planning
- Filing Status Tax Impact Comparator (/filing-status-comparator): MFJ vs. MFS tax outcome modeling

### Immigration and Finances (guide slug: `immigration`)
- Authorization-Gap Bridge Planner (/auth-gap): plans finances through a work-authorization gap
- Path-to-Residency Cost Planner (/residency-costs): plans for green card/citizenship process costs
- Credit-Building-From-Zero (/credit-building): builds US credit history with no prior history

### Home Buying (guide slug: `home-buying`)
- Down Payment vs. PMI Strategy Tool (/down-payment-pmi-strategy): compares a larger down payment against investing the difference
- Total Cost of Homeownership Calculator (/homeownership-cost-calculator): ongoing costs beyond the mortgage payment
- Closing Cost and Cash-to-Close Estimator (/closing-cost-estimator): total upfront cash needed to close
- Escrow vs. Self-Managed Tax and Insurance Comparator (/escrow-comparator): lender-managed escrow vs. self-managed HYSA approach

### New Parent / Pregnancy, Baby and Family Leave (guide slug: `new-parent`)
- Parental Leave Navigator (/parental-leave-navigator): models income during parental leave
- Childcare Bubble Calculator (/childcare-bubble): projects childcare costs by type and region
- Children's Planning Hub (/childrens-planning): coordinates child-related planning tasks
- 529 Projection Tool (/college-529, moved from College Planning July 15, 2026): long-horizon 529 savings projection
- Life Insurance Needs Calculator (/life-insurance-needs, shared with Estate Planning, OR-logic): sizes life insurance need with dependents

### College Planning (guide slug: `college-planning`)
- Financial Aid Award Comparison Tool (/financial-aid-comparison): compares multiple schools' aid offers side by side
- Student Loan Optimizer for Parents (/parent-loan-optimizer): Parent PLUS vs. private loan vs. cash comparison
- FAFSA Remarriage-Timing Planner (/fafsa-remarriage-timing, shared with Blended Family, OR-logic): models FAFSA impact of stepparent income

### Career Transition (guide slug: `career-transition`)
- Severance and Benefits Bridge Calculator (/severance-bridge-calculator): models severance/COBRA/unemployment income bridge
- Offer Comparison Tool (/offer-comparison): total comp comparison for a new role
- COBRA vs. Marketplace Coverage Comparator (/cobra-vs-marketplace): the health coverage continuation decision

### Starting a Business (guide slug: `starting-a-business`)
- Business Entity Structure Comparison (/entity-structure-comparison): LLC vs. S-corp vs. sole proprietorship tax comparison
- Quarterly Tax Calculator (/quarterly-tax): estimates quarterly self-employment tax payments
- Retirement Plan Comparison (/retirement-plan-comparison): Solo 401k vs. SEP vs. SIMPLE comparison

### Gig Work / Freelance Income (guide slug: `gig-work-freelance-income` -- not `gig-work`)
- Multi-Platform Income and Expense Tracker (/multi-platform-income-tracker): aggregates income and deductible expenses across gig platforms
- Gig Income Engine (/gig-engine): smooths irregular gig income
- Runway Calculator (/runway): models how long savings last during income gaps

### Divorce and Transition (guide slug: `divorce`)
- Emergency Fund Priority Tool (/emergency-fund-priority, shared with Widowhood, OR-logic): prioritizes rebuilding an emergency fund post-separation
- Single Income Budget (/income-stabilization, shared with Widowhood, OR-logic): rebuilds a budget for one income
- Credit Rebuilding Timeline (/credit-rebuilding, shared with Widowhood, OR-logic): rebuilds credit after joint-debt separation
- QDRO Readiness Prep (/qdro-readiness): prepares for dividing retirement accounts via QDRO
- QDRO Tracker and Award Estimator (/qdro-tracker): tracks QDRO status and estimates award

### Caregiving (guide slug: `caregiving`)
- Care Options Cost Comparison (/care-options-cost-comparison): compares in-home/assisted living/nursing home costs against assets
- Caregiver Impact on Retirement Calculator (/caregiver-retirement-impact): models the retirement savings gap from reduced work or leaving the workforce to caregive
- Caregiver Ledger (/caregiver-ledger): splits and tracks care costs among siblings

### Disability Insurance and Personal Illness (guide slug: `disability-planning` -- not `disability`)
- Benefit Coordination Calculator (/benefit-coordination-calculator): models STD/LTD/SSDI offset and combined income
- Out-of-Pocket Medical Cost Planner (/medical-cost-planner): plans ongoing medical costs against HSA/FSA balances
- LTD Analyzer (/ltd-analyzer): analyzes long-term disability coverage adequacy

### Widowhood and Losing a Partner (guide slug: `widowhood`)
- First-Year Financial Checklist (/first-year-financial-checklist): task tracker for the first year after a loss
- SS Survivor-Benefit Optimizer (/ss-survivor-optimizer, Widowhood-exclusive only -- no longer shared with Retirement, which does not sell separately): models survivor benefit claiming sequencing
- Shares Divorce's Emergency Fund Priority, Single Income Budget, and Credit Rebuilding tools

### Remarriage and Blended Families (guide slug: `blended-family` -- not `remarriage`)
- Blended Family Conflict Checker (/blended-family-conflict-checker): flags likely inheritance conflict scenarios before a structure decision
- Shares College Planning's FAFSA Remarriage-Timing Planner
- QTIP vs. Bypass Trust Walkthrough (/qtip-bypass-decision, shared with Estate Planning, OR-logic): walks through trust structures for blended families

### Estate Planning (guide slug: `estate-planning`)
- Beneficiary Designation Audit (/beneficiary-designation-audit): reviews beneficiary designations across accounts
- Estate Tax Threshold and State Exposure Checker (/estate-tax-checker): checks federal and state estate tax exposure
- Shares Remarriage's QTIP/Bypass tool and New Parent's Life Insurance Needs Calculator

### Inheritance (guide slug: `inheritance`)
- Windfall Allocation Tool (/windfall-allocation): allocates non-retirement inherited assets across goals
- Step-Up in Basis and Capital Gains Estimator (/step-up-basis-calculator): estimates capital gains impact using step-up in basis
- Inherited IRA Distribution Strategy (/inherited-ira-distribution, moved from Core July 15, 2026): models inherited retirement account distribution timing

---

## Not a Tool

Life Gaps (/life-gaps): a lead-magnet quiz, not a calculator or planner, stays public by design.

---

## Slug Corrections (July 15, 2026)

Three guide slugs were found mismatched between `GuideGate`/`GUIDE_TOOLS` code and the real `life_phase_guides.slug` values in production, causing silent bugs (fail-open access grants in `GuideGate`, and an empty "Tools for this guide" section from the separate `GUIDE_TOOLS` lookup). Use these exact slugs, not the guide's display name, when referencing a guide in code:

| Guide display name | Correct slug | Wrong slug previously used |
|---|---|---|
| Disability & Personal Illness | `disability-planning` | `disability` |
| Gig Work & Freelance Income | `gig-work-freelance-income` | `gig-work` |
| Blended Family | `blended-family` | `remarriage` |

All other guide slugs (`early-career`, `newlywed`, `immigration`, `home-buying`, `new-parent`, `college-planning`, `career-transition`, `starting-a-business`, `divorce`, `caregiving`, `widowhood`, `retirement`, `estate-planning`, `inheritance`) matched correctly and needed no fix.
