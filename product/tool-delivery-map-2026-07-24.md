# Krovos Tool Delivery Map

**Date:** July 24, 2026
**Purpose:** A canonical product-design map for how every currently built tool should be surfaced contextually from a member's Life Profile, goals, life phase, and Core hubs, instead of Krovos behaving as a flat catalog of unrelated calculators. This document is design guidance, not a build record. It proposes delivery behavior; it does not claim any of the proposed behavior is already built.

**Sources:** `product/krovos-tool-catalog.md` (July 17, 2026 renaming pass, canonical tool/route list), `product/navigation-tool-guide-map-2026-07-23.md` (current nav model and member-discovery rules), `strategic/product-overview.mdx`, `governing/founder-edition.mdx` (burden-relief and Ten Laws framing), `/Users/christine.smith/mcp/csuite/context/3_current_state.md` (canonical current-state record, refreshed July 23, 2026), and `product/beta-readiness-test-plan-2026-07-24.md`.

**A note on what is and isn't claimed here:** every "Trigger Signal" and "Why Now" in the tables below is a *proposed* recommendation rule, not a confirmed built behavior, unless the Delivery Classification column says so explicitly and the canonical record backs it. Where the canonical record already confirms a contextual-surfacing mechanism exists (Life Compass's single current-focus card, Retirement's recommended tool set, the Core Tools library's "up to five contextually appropriate actions"), that is stated as current reality in Section 3. Everything else proposed below is a design recommendation for a future build, explicitly not yet implemented.

---

## 1. Delivery Classification Key

Every tool below is assigned exactly one of four classifications:

- **Primary Navigation (Nav)** — belongs in the persistent app shell itself. Per the current (July 23, 2026) universal app shell, this means Today/Life Compass, Goals/Life Goals, Calendar/Life Calendar, Ask Krovos/Krovos Guide, and the Explore items Net Worth and Retirement. No individual calculator should be a nav-level destination; hubs are.
- **Contextual Recommendation (Rec)** — a tool that should be proactively surfaced from a hub (Life Compass, Life Goals, Net Worth, Life Labor, Life Calendar, or a guide page) when a specific Life Graph signal is present, rather than requiring the member to browse for it.
- **Guide-Only (Guide)** — a tool whose relevance is narrow and guide-scoped; it should be reachable through its owning guide's own tool list and preview flow, but does not warrant cross-guide dashboard surfacing.
- **Catalog-Only For Now (Catalog)** — remains discoverable in `/tools` but should not be proactively promoted anywhere yet, either because its trigger is too rare/complex to justify recommendation logic today, or because no reliable signal exists in the Life Graph to detect the trigger.

---

## 2. Per-Tool Delivery Map

### Core Product (never guide-gated)

| Tool | Route | Hub | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|---|
| Net Worth Tracker | `/net-worth` | Core / Explore nav | Universal — any confirmed investment or debt data | Ongoing baseline; always relevant once any financial data exists | Nav | `financial.investments_total`, debt entries | No — already a nav-level Explore destination |
| Debt Payoff Planner | `/debt-payoff-planner` | Core | Any debt entry present | Interest accrues daily; earlier optimization compounds | Rec | Debt entries in `financial` | Partial — recommended within the Core Tools "up to five" set per canonical record, but not yet from Life Compass directly |
| Paycheck Planner | `/paycheck-planner` | Core | Income entered with no saved allocation plan | The very next paycheck needs a plan | Rec | `financial.income_data` | Partial — same as above |
| Savings Goals | `/savings-goals` | Core | A stated goal exists with no funding vehicle | Time value of starting now vs. later | Rec | Life Goals entry or stated intent from Life Profile | Yes — currently session-only per `CLAUDE.md`'s own backlog; option to promote a vault to a named goal is not built |
| Emergency Fund Calculator | `/emergency-fund` | Core | No confirmed emergency-fund balance, or balance below the calculated target | The gap is the primary near-term risk exposure | Rec | `financial.investment_data.emergency_fund_balance` | Partial — in the contextual "up to five" set, not yet a Life Compass callout tied specifically to this gap |
| Credit Health | `/credit-health` | Core | Active debt, or a credit-building context (Early Career, Divorce/Widowhood credit rebuild, Immigration Credit Builder) | Score trajectory rewards an early start | Rec | Debt entries; life-phase context | Yes — no cross-link from the guide-scoped credit-rebuild tools back to this Core tool is confirmed |
| Credit Card Optimizer | `/credit-card-optimizer` | Core | Member confirms paying statements in full, with active rewards cards | Missed rewards value accrues monthly | Catalog | Explicit payment-behavior confirmation (already required by the tool's own input-safety gate) | No — the tool itself now correctly refuses to recommend a card to a member who carries interest, which is the more urgent conflict this tool used to have |
| Purchase Planner | `/purchase-planner` | Core | A stated large near-term goal with no funding source chosen | Financing terms and promotional windows are time-bound | Rec | A Life Goal of a purchase type | Yes — no link from Life Goals to this tool when a purchase-type goal is created |
| Refinance Calculator | `/refinance-calculator` | Core | Existing mortgage present, member indicates a rate-drop signal | Refinance windows are rate-dependent and can close | Catalog | `household.mortgage`; no live market-rate feed exists to auto-trigger this | No — appropriately catalog-only since Krovos has no live rate data to justify proactive promotion |
| Rent vs. Buy Calculator | `/rent-vs-buy` | Core | Renting, with a home-purchase goal or "coming up: buying a home" selected | Earlier modeling gives more lead time for a down-payment plan | Rec | `household.housing_type = renting`; Life Phase "coming up" selection | Yes — currently only reachable via Home Buying's guide preview as its lead magnet, not surfaced Core-wide to every renter |
| Insurance Gap Checker | `/insurance-gap-checker` | Core | A new dependent, new income source, or new asset with no matching insurance review | Coverage gap exists from the moment of the life event, not from when it's noticed | Rec | `family.children` change, income change, or asset change | Yes — no life-event-triggered nudge into this tool exists today |
| Open Enrollment Comparison | `/open-enrollment-comparison` | Core | Calendar-based open-enrollment window, or a benefits change entered | A fixed annual window; missing it has a real cost | Rec | Calendar date range; `benefits_data` | Yes — no date-driven proactive surfacing confirmed |
| Roth Conversion Calculator | `/roth-conversion-calculator` | Core / Retirement | A detected lower-income year (job loss, sabbatical, early retirement) or post-retirement per Report Rule 9 | Conversion tax cost is lowest in specific years — a closing window | Rec | Income-change detection; retirement status | Yes — no automatic lower-income-year detection confirmed built |
| Retirement Withdrawal Planner | `/retirement-withdrawal-planner` | Core / Retirement | Within a defined number of years of target retirement age | Sequencing decisions must be made before withdrawals start | Guide (folded into Retirement's own flow) | Retirement target age, account balances | No — already reachable from within the Retirement workspace's own guided flow |
| Life Labor | `/life-labor` | Core / Explore nav | A household connection is accepted (a partner exists) | Division-of-labor tension is ongoing and best addressed early in a shared household | Nav | `household_connections` row | No — already promoted into the universal Explore menu per the canonical record |
| Milestone Tracker ("Progress and wins") | `/milestone-tracker` | Core / Life Compass | Universal — any recorded Net Worth history or goal contribution | Motivation/retention surface, relevant once any progress exists | Rec (already live on Today) | Net Worth snapshot history or goal contributions | No — this is the one Core surface confirmed built as a proactive, fact-derived callout on Today |
| Life Goals | `/life-goals` | Core / Explore nav | Universal | Every member should have a place to name and track outcomes | Nav | None | No |

### Retirement Hub (Core, consolidated at `/retirement`)

| Tool | Route | Hub | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|---|
| Retirement Hub | `/retirement` | Retirement / Explore nav | Universal | The connected planning workspace for every subscriber | Nav | Net Worth, active Life Goals, Life Profile income/spending | No |
| Investment Priority | `/investment-priority` | Retirement | Member has more than one funding priority in play (debt, match, HSA, IRA, taxable) with no saved sequence | An unsequenced next dollar is a standing opportunity cost | Rec (per Retirement's own "small decision-based set," per canonical record) | Multiple funding sources present in Life Graph | No — Retirement's Tools view already recommends a small decision-based set rather than the full library |
| Retirement Scenarios (`FreedomScenarios`) | Embedded in `/retirement`, `/life-view`, `/preview` | Retirement | Universal within the Retirement workspace | Core to the guided model itself | Nav (embedded, not a standalone route) | Retirement inputs saved | No |
| Safe Withdrawal Rate | `/swr` | Retirement | Approaching or in retirement | Sustainable-withdrawal question becomes live at the transition point | Rec | Target retirement age proximity | No — public and Core by design |
| CoastFI Calculator | `/coast-fi` | Retirement | Mid-career with meaningful existing retirement assets | Answers "can I stop contributing so aggressively" — a real, ongoing decision | Rec | Investment balance + age | No |
| Social Security Comparison | `/social-security-comparison` | Retirement | Approaching Social Security eligibility age | Claiming-age decision window narrows as the member nears eligibility | Rec | Age, benefit estimate | No |
| RMD Deadline Tracker | `/rmd-tracker` | Retirement | Approaching age 73, or an inherited account with RMD rules | Missed RMDs carry a real penalty on a fixed calendar | Rec | Account type and age | No |

### Early Career (guide slug `early-career`)

Trigger context for the guide overall: Life Phase "current situation" is employed full-time/part-time or between jobs shortly after starting, in the Foundation life phase.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| First Job Decisions | `/first-job-decisions` | First income entry with no benefit elections recorded | Elections (Roth/Traditional, employer match) are often locked to an enrollment window | Guide | `financial.income_data` present, no `benefits_data` | Yes — only discoverable by opening the guide; a member who never opens Early Career after their first paycheck entry gets no nudge |
| Student Loan Repayment Planner | `/student-loan-repayment-planner` | A debt entry of type student loan | Repayment-plan choice affects both the current minimum due and long-term forgiveness eligibility | Rec | Student loan debt entry | Yes — student debt is detectable Core-wide; this tool should be recommendable to any member with a student loan, not gated behind opening Early Career specifically |
| First Apartment Calculator | `/first-apartment-calculator` | Renting + Foundation life phase | Upcoming lease decisions affect monthly margin now | Guide | `household.housing_type = renting`, life phase Foundation | No — narrow enough to stay guide-scoped |

### Newlywed (guide slug `newlywed`)

Trigger context: relationship status recently set to Engaged or Married, or "coming up" includes getting married.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Joint Finances Comparison | `/joint-finances-comparison` | Relationship status newly Married or Engaged | Account-structure decision matters most right after merging finances, not later | Guide | Relationship status field | Yes — this decision is time-sensitive and should be recommended the moment status changes, not left for the member to find |
| Wedding Budget Planner | `/wedding-budget-planner` | "Coming up: getting married" with a date | Vendor deposits and budget lock-in happen before the wedding date, on a fixed timeline | Rec | Wedding date in Life Phase "coming up" | Yes — a dated life event like this should trigger a Life Compass timeline milestone, not just guide access |
| Filing Status Comparison | `/filing-status-comparison` | Marital status changed to Married | Filing status affects the very next tax season | Guide | Relationship status field | No — reasonably guide-scoped since the tax-season trigger is seasonal, not immediate |

### Immigration (guide slug `immigration`)

Trigger context: Life Phase "navigating immigration" flag or visa data entered.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Work Authorization Gap Planner | `/work-authorization-gap-planner` | Partner visa type pending EAD (e.g., K-1) | Income-gap window is time-bound to I-765 processing | Guide | Partner visa/work-authorization field | No — narrow, correctly scoped |
| Green Card Cost Planner | `/green-card-cost-planner` | Immigration flag with a green-card process underway | Fees and costs are scheduled, not optional | Catalog | Visa/process-stage field, currently low-signal | Yes — small population, but should still get a one-time nudge at the moment the process stage is entered rather than requiring the member to browse to it |
| Credit Builder | `/credit-builder` | No US credit history flag | Credit history compounds; earlier start speeds future qualification (mortgage, auto) | Guide | No-prior-credit-history signal | No |

### Home Buying (guide slug `home-buying`)

Trigger context: "coming up" includes buying a home, a home-purchase goal exists, or the member is renting with savings building.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Down Payment vs. PMI Comparison | `/down-payment-vs-pmi-comparison` | Active home-purchase goal with a target down-payment percent | Changes the required savings timeline immediately | Rec | Home-purchase-type Life Goal | Yes — should be recommended the moment a home-purchase goal is created, not left to guide discovery |
| Homeownership Cost | `/homeownership-cost-calculator` | Home-purchase goal nearing target, or active house shopping | Budget accuracy matters before an offer is made | Guide | Home-purchase goal in a late stage | No |
| Closing Cost Calculator | `/closing-cost-calculator` | Home-purchase goal at offer/near-offer stage | Cash-to-close total is needed before closing | Guide | Home-purchase goal, late stage | No |
| Escrow Comparison | `/escrow-comparison` | An existing mortgage present, or recently closed | Escrow vs. self-managed decision is typically made at or soon after closing | Rec | `household.mortgage` recently added | Yes — this decision has a real, short window right after closing and should be recommended proactively, not left for `/tools` browsing |

### New Parent (guide slug `new-parent`)

Trigger context: an expecting entry in `family.children`, or a child under roughly age 5.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Parental Leave Planner | `/parental-leave-planner` | Expecting entry with a due date | Leave-pay elections must be made before leave starts | Rec | `family.children[].expecting = true` with a due date | Yes — should trigger a Life Compass milestone the moment `expecting` is checked, not wait for guide access |
| Childcare Bubble Calculator | `/childcare-bubble` | Expecting, or a child under ~5 | Provider deposits and waitlists are time sensitive, often before birth | Rec | Same as above | Yes — same reasoning, provider waitlists in particular reward very early planning |
| Child Costs Planner | `/child-costs-planner` | A new child added | Ongoing budget needs updating immediately, not at the next scheduled review | Rec | New `family.children` entry | Yes — should trigger the moment a child record is added |
| 529 Projection Calculator | `/529-projection-calculator` | A young child present | Compounding rewards an earlier contribution start | Guide | Child age field | No — appropriately guide-scoped since the New Parent guide is the natural home for this |
| Life Insurance Calculator | `/life-insurance-calculator` | A new dependent added (OR-logic, shared with Estate Planning) | Coverage gap exists from the moment a dependent exists | Rec | New dependent in `family` | Yes — a coverage gap on a new dependent is exactly the kind of burden-relief moment Krovos should surface without being asked |

### College Planning (guide slug `college-planning`)

Trigger context: a child in the family approaching high-school/college age, or a stated education-savings goal.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Financial Aid Comparison | `/financial-aid-comparison` | Award letters received; child in the ~17-18 age band | Decision deadlines (commonly May 1) are short and fixed | Guide | Child age field, award data entered manually | No |
| Parent Loan Comparison | `/parent-loan-optimizer` | A funding gap surfaced by Financial Aid Comparison | Loan decision must be made before the term starts | Guide | Prior Financial Aid Comparison result | No |
| FAFSA Remarriage Planner | `/fafsa-remarriage-timing` | Remarried status + a child in the college-aid window (OR-logic, shared with Blended Family) | FAFSA parent-of-record rules are award-year specific | Guide | Remarried status + child age field | No |

### Career Transition (guide slug `career-transition`)

Trigger context: Life Phase "current situation" changes to between jobs, or "also true" includes planning to leave a W-2.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Severance Bridge Calculator | `/severance-bridge-calculator` | "Between jobs" status newly set | Severance/COBRA/unemployment runway needs modeling immediately | Rec | Life Phase current-situation change | Yes — a status change this significant should surface a Life Compass callout, not require the member to seek out the guide |
| Offer Comparison | `/offer-comparison` | Between-jobs status + a new offer entered | The decision deadline is the offer's own expiration | Guide | Offer data entered manually | No |
| COBRA vs. Marketplace Comparison | `/cobra-vs-marketplace-comparison` | Job loss / benefits end-date approaching | COBRA election window is time-boxed (60 days) | Rec | Benefits end-date field | Yes — a hard 60-day window deserves proactive surfacing, not passive discovery |

### Starting a Business (guide slug `start-business`)

Trigger context: "current situation" is self-employed primary/business owner, or "also true" includes starting a business on the side.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Business Structure Comparison | `/business-structure-comparison` | New business flag with no entity type set | Entity choice affects this year's taxes and liability exposure | Rec | Business flag, no entity type | Yes — should trigger the moment the business flag is set, before the member has to think to look for it |
| Quarterly Tax Calculator | `/quarterly-tax` | Self-employment income present | Quarterly estimated-tax deadlines are fixed calendar dates | Rec | Self-employment income entry | Yes — this is a calendar-bound obligation Krovos should remind about, not wait to be asked |
| Retirement Plan Comparison | `/retirement-plan-comparison` | Business owner with no retirement plan type set | SEP/Solo 401k contribution deadlines are calendar-bound | Guide | Business owner flag | No |
| Revenue Target | `/revenue-target` | W-2 exit timeline entered, or business marked primary | Milestone tells the member exactly when leaving the W-2 is safe | Guide | W-2 exit timeline or business-primary flag | No — this tool already auto-detects its framing from Life Graph data, which is the model the rest of this map is recommending elsewhere |

### Gig Work (guide slug `gig-work`)

Trigger context: income types include gig/1099 income.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Gig Income Tracker | `/gig-income-tracker` | Gig income present, no tracking started | Deductible-expense capture is time sensitive (receipts) | Rec | Gig income type in `financial` | Yes — should be recommended the moment gig income is first entered |
| Gig Paycheck Calculator | `/gig-paycheck-calculator` | Variable gig income | Smoothing is needed for the very next irregular paycheck | Guide | Gig income variability signal | No |
| Savings Runway Calculator | `/savings-runway-calculator` | Gig-only income + low reserve | Income-gap risk is ongoing, not one-time | Guide | Gig-only income + low savings balance | No |

### Divorce (guide slug `divorce`)

Trigger context: relationship status changed to Divorced or Separated.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Emergency Fund Rebuilder | `/emergency-fund-rebuilder` | Separation event (OR-logic, shared with Widowhood) | Single-income emergency exposure begins immediately | Rec | Relationship-status change | Yes — a status change this significant should trigger a proactive nudge, not passive guide discovery |
| Single Income Planner | `/single-income-planner` | Same (OR-logic) | Budget must be rebuilt for the very next pay cycle | Rec | Relationship-status change | Yes — same reasoning |
| Credit Rebuilding Planner | `/credit-rebuilding-planner` | Joint-debt separation (OR-logic) | Credit utilization changes immediately post-separation | Rec | Debt-ownership change | Yes — same reasoning |
| QDRO Readiness Checklist | `/qdro-readiness-checklist` | Divorce + retirement accounts present | QDRO filing windows tie to the divorce decree timeline | Guide | Retirement accounts present at divorce | No |
| QDRO Tracker | `/qdro-tracker` | A QDRO already filed | Tracks an active, time-bound legal process | Catalog | Prior QDRO Readiness Checklist use | No — low-frequency, appropriately catalog/guide-only rather than promoted |

### Caregiving (guide slug `caregiving`)

Trigger context: an aging-parent flag present, or a stated caregiving concern.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Care Options Comparison | `/care-options-comparison` | Aging-parent flag newly set | Placement decisions are often urgent (health-event driven) | Rec | `family` aging-parent flag | Yes — an urgent, often health-event-triggered decision deserves proactive surfacing, not passive discovery |
| Caregiving Retirement Impact | `/caregiver-retirement-impact-calculator` | Reduced work hours or considering leaving the workforce to caregive | Earlier modeling keeps the decision more reversible | Guide | Work-hours-change signal | No |
| Caregiver Cost Tracker | `/caregiver-cost-tracker` | Caregiving + siblings involved in cost-splitting | Real-time cost tracking, not after-the-fact reconciliation | Guide | Household/family context indicating shared caregiving | No |

### Disability (guide slug `disability`)

Trigger context: a disability concern flagged, or a leave/claim event.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Disability Benefits Calculator | `/benefit-coordination-calculator` | Disability leave started | Benefit timeline determines income during leave right now | Rec | Leave-status field | Yes — an active leave is exactly the moment this should surface unprompted |
| Medical Cost Planner | `/medical-cost-planner` | Ongoing medical condition flagged | HSA/FSA elections are typically annual; timing matters | Guide | Medical-condition flag | No |
| LTD Coverage Checker | `/ltd-coverage-checker` | Disability flag present, no LTD confirmed | Coverage adequacy should be known before a claim, not during one | Rec | Disability flag, no LTD data | Yes — this is precisely the kind of gap Krovos should flag before a crisis, not only within a guide the member has to find |

### Widowhood (guide slug `widowhood`)

Trigger context: relationship status newly set to Widowed.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Widowhood Checklist | `/widowhood-first-year-checklist` | Widowed status just set | Time-bound first-year tasks (claims, authority, estate) | Rec | Relationship-status change | Yes — this is the single clearest case in the whole catalog for an immediate, proactive nudge rather than passive discovery |
| Survivor Benefit Calculator | `/survivor-benefit-calculator` | Widowed + approaching Social Security age | Claiming-timing decision windows | Guide | Relationship status + age | No |
| (shares Divorce's Emergency Fund Rebuilder, Single Income Planner, Credit Rebuilding Planner) | — | Same as Divorce | Single-income exposure begins immediately | Rec | Relationship-status change | Yes — same reasoning as the Divorce entries above |

### Blended Family (guide slug `blended-family`)

Trigger context: Remarried status with children present from either partner.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Blended Family Inheritance | `/blended-family-inheritance-checklist` | Remarried + estate documents predating the remarriage | Conflicting beneficiary designations are an active risk now, not hypothetical | Rec | Remarried status + prior estate documents flag | Yes — should be recommended immediately upon a remarriage status change, since the risk exists from that moment |
| FAFSA Remarriage Planner | `/fafsa-remarriage-timing` | Shared with College Planning | Award-year-specific rules | Guide | Same as College Planning entry | No |
| QTIP vs. Bypass Comparison | `/qtip-vs-bypass-comparison` | Remarried + meaningful estate value (OR-logic, shared with Estate Planning) | Existing estate documents may misallocate assets right now | Guide | Remarried status + estate-value signal | No |

### Estate Planning (guide slug `estate-planning`)

Trigger context: a stated estate/legacy concern, or proximity to retirement/high net worth.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Beneficiary Checker | `/beneficiary-checker` | Any account opened or changed, or a major life event (marriage, divorce, birth) | An unreviewed beneficiary is a standing risk best checked immediately after any life event | Rec | Account change or life-event flag | Yes — this is a Core-adjacent safety check that arguably deserves the same universal, event-triggered treatment as Insurance Gap Checker, not guide-gating |
| Estate Tax Checker | `/estate-tax-checker` | Net worth crossing state/federal estate-tax thresholds | Exposure exists now regardless of when documents get drafted | Catalog | Net worth threshold crossing — rare, and no threshold-crossing detection is confirmed built | No — appropriately catalog-only until threshold-crossing detection exists; low-frequency trigger |
| (shares QTIP/Bypass, Life Insurance Calculator) | — | Shared triggers as above | — | Guide | — | — |

### Inheritance (guide slug `inheritance`)

Trigger context: a windfall/inheritance event entered.

| Tool | Route | Trigger Signal | Why Now | Classification | Dependency / Prerequisite | White-Glove Conflict? |
|---|---|---|---|---|---|---|
| Inheritance Planner | `/inheritance-planner` | Inheritance received | Allocation decision window exists before the money gets absorbed into ordinary spending | Rec | Windfall event entered | Yes — a windfall is a rare but high-stakes moment that deserves proactive surfacing the instant it's recorded |
| Step-Up Basis Calculator | `/step-up-basis-calculator` | Inherited asset with unknown basis | Basis matters the moment a sale is considered | Catalog | Inherited-asset entry, sale not yet under consideration | No — appropriately deferred until the member indicates they're considering a sale |
| Inherited IRA Planner | `/inherited-ira-planner` | Inherited retirement account | Distribution timing rules are calendar-bound (10-year rule, RMDs) | Rec | Inherited retirement account entry | Yes — the calendar-bound rule makes this a strong candidate for proactive surfacing, not passive `/tools` discovery |

### Not a Tool

Life Gaps (`/life-gaps`) is a lead-magnet quiz, not a calculator or planner, and stays public by design. It is out of scope for this delivery map.

---

## 3. Current Reality vs. Proposed Future Delivery

**Current reality, confirmed by the canonical record:**
- The persistent nav shell (Today/Goals/Calendar/Ask Krovos/Explore/Settings) is built and live.
- Life Compass ("Today") resolves and shows exactly **one** governed current-focus card at a time, derived from a confirmed change requiring plan review, the first Life View priority, or the weakest incomplete Life Graph zone — this is real, working contextual recommendation, just narrow in scope (one recommendation, not a tool-specific one).
- The Retirement workspace's Tools view recommends "a small decision-based set" rather than showing its full tool library by default.
- The Core Tools library derives "up to five contextually appropriate actions from recorded Life Graph and Life Goal facts" with a stated reason for each — this is the closest existing analog to what this document proposes extending guide-wide.
- `/tools` itself is explicitly designed as "a complete secondary library and fallback," not the primary experience, per `product/navigation-tool-guide-map-2026-07-23.md`.
- Guide-exclusive tools are reachable through their owning guide's page once purchased, with locked tools showing an in-place explanation rather than a checkout redirect.
- Revenue Target already auto-detects its framing from Life Graph facts with no member self-selection — this is the single clearest existing example of the exact behavior this document recommends generalizing.

**Proposed future delivery, not yet built, not claimed as built anywhere above:**
- Extending the existing "up to five contextual actions" mechanism beyond Core tools to include high-frequency guide-exclusive tools the member already owns (marked "Rec" with a "Yes" conflict flag throughout Section 2).
- Life-event-triggered proactive nudges (a Life Compass callout, not just a locked/unlocked tool state) for status changes that are inherently time-sensitive: relationship-status changes (Newlywed, Divorce, Widowhood, Blended Family), a new dependent added, an expecting entry, a business flag set, a between-jobs status change, and a windfall/inheritance entry.
- A "recommended next" pattern on Life Goals, Net Worth, Life Labor, and Life Calendar analogous to what Life Compass and Retirement already do (Section 4).
- Any cross-guide sharing of a recommendation (e.g., recommending Beneficiary Checker universally on any life event, not only within a purchased Estate Planning guide).
- Threshold-crossing detection (e.g., net worth crossing an estate-tax threshold) to justify promoting currently catalog-only tools later.

---

## 4. Recommended Slim Global Navigation Model

This does not propose changing the current nav shell structure, which is already slim and hub-based per the July 23, 2026 build. It proposes keeping it exactly this size and resisting the urge to add individual tools to it as new tools ship:

- **Today** (Life Compass) — the single governed current-focus surface. Home base.
- **Goals** (Life Goals) — outcomes the member is working toward.
- **Calendar** (Life Calendar) — dated household, personal, work, and Guide-linked items.
- **Ask Krovos** (Krovos Guide) — contextual conversation.
- **Explore** (secondary menu) — Guides, Tools, Life Profile, Life Graph, Net Worth, Retirement. This is correctly where Net Worth and Retirement already sit as the only two individual hubs promoted to nav-adjacent status, because both are ongoing, always-relevant surfaces rather than point-in-time decisions.
- **Settings** — account, privacy, sharing, subscription, access controls.

**Recommendation: do not add any additional individual tool to primary nav or Explore.** Every tool besides Net Worth and Retirement is either a point-in-time decision (best delivered as a contextual recommendation at the moment its trigger fires) or a guide-scoped tool (best delivered through its guide). Adding more individual tools to the nav shell would recreate the flat-catalog problem this document exists to prevent.

---

## 5. "Recommended Next" Pattern, Per Hub

Current reality first, proposal second, for each of the six hubs named in the request.

### Dashboard (Life Compass / Today)
- **Current:** shows exactly one governed current-focus card, resolved in priority order (confirmed change → Life View priority → weakest Life Graph zone), plus the milestone timeline and "Progress and wins."
- **Proposed:** when the current-focus card's underlying signal maps to a specific tool (not just a Krovos Guide prompt), the card's action should deep-link directly to that tool with the relevant fields pre-filled, rather than only opening a Guide conversation. This is a refinement of an existing mechanism, not a new one.

### Life Goals
- **Current:** create, edit, log contributions, complete/abandon, tab view. No confirmed contextual tool recommendation exists here per the canonical record.
- **Proposed:** when a goal of a recognizable type is created (home purchase, wedding, education, business launch), surface the one or two directly relevant tools (e.g., Down Payment vs. PMI Comparison for a home-purchase goal) as a "get ready for this goal" prompt on the goal's own detail view, not as a separate list to browse.

### Retirement
- **Current:** already recommends a small decision-based set of tools rather than the full library; this hub is the most mature example of the pattern proposed elsewhere in this document.
- **Proposed:** extend the same recommendation logic to surface Roth Conversion Calculator specifically in a detected lower-income year, rather than only listing it as one of several always-available options.

### Net Worth
- **Current:** aggregates assets and liabilities; no confirmed contextual recommendation surface exists here.
- **Proposed:** when a specific asset/liability category changes meaningfully (a new mortgage, a debt paid off, an inheritance recorded), show a single "what this changes" prompt linking to the one most relevant tool (Escrow Comparison, Credit Health, Inheritance Planner respectively), instead of leaving Net Worth as a purely descriptive view.

### Life Labor
- **Current:** effort/energy tagging, quick-access mode, Money and Home Work correlation, Seasons rebalancing prompts, household facts layer — already a rich, self-contained experience.
- **Proposed:** no additional tool recommendation is needed here; Life Labor's own Seasons/rebalancing prompt already functions as its internal "recommended next" mechanism. Do not force an unrelated financial-tool recommendation into this surface.

### Life Calendar
- **Current:** one-time and recurring task/event layers, Google import and read-only work feed live; Microsoft/Apple import pending.
- **Proposed:** when a dated milestone on the calendar corresponds to a tool trigger (a wedding date, a due date, a COBRA election deadline), show that tool as the milestone's own action, mirroring the existing Life Compass milestone-to-Guide-action pattern rather than inventing a new interaction model.

---

## 6. Catalog-Only-For-Now: Short List

Tools that should remain discoverable in `/tools` but should **not** be proactively promoted anywhere until a reliable Life Graph signal exists for their trigger, because the trigger is rare, requires data Krovos doesn't currently detect automatically, or the population affected is small enough that a blanket recommendation rule would be noise for everyone else:

- **Refinance Calculator** — no live mortgage-rate feed exists to detect a genuine refinance window.
- **Green Card Cost Planner** — low-signal, low-frequency trigger; a one-time manual nudge at process-stage entry is more appropriate than ongoing promotion.
- **QDRO Tracker** — only relevant after QDRO Readiness Checklist has already been used; naturally sequential, not a fresh recommendation target.
- **Estate Tax Checker** — depends on net-worth-threshold-crossing detection that does not exist yet.
- **Step-Up Basis Calculator** — depends on the member actively considering a sale of an inherited asset, which is not detectable from data alone.

Do not build promotion logic for these until the specific missing signal (a rate feed, threshold detection, or an explicit member-stated intent) exists. Promoting them today would mean guessing at a trigger Krovos cannot actually confirm, which conflicts with the founding principle "never invent a member fact to create a recommendation."

---

## 7. Implementation Priority Sequence

Design and product sequencing only. No app code, migrations, or deployed configuration is touched by this document or by acting on it without a separate build session.

1. **Define one shared "life signal" taxonomy.** Before building any new recommendation logic, formally document which Life Graph fields map to which triggers (the "Trigger Signal" column above is a first draft of this). Today this logic is implicit and scattered per tool; it should become one referenced spec, analogous to how `lib/health-scores.ts` is already a single shared scoring utility for Life Graph and Life Compass.
2. **Extend the existing Core Tools "up to five contextual actions" mechanism** to include owned guide-exclusive tools flagged "Rec" above, before building any new UI surface. This reuses a mechanism already built rather than inventing a second one.
3. **Add life-event-triggered Life Compass callouts** for the highest-stakes, clearest-signal cases first: a relationship-status change to Divorced/Separated/Widowed/Remarried, a new dependent added, and a business flag newly set. These are the cases in Section 2 flagged with the strongest "why now" urgency and the clearest existing Life Graph fields to detect them.
4. **Build the "recommended next" pattern into Life Goals** next, since goal creation is already a clean, well-defined trigger point (Section 5).
5. **Extend the same pattern to Net Worth and Life Calendar**, once the Life Goals version is validated.
6. **Leave Life Labor alone** — it already has its own internal recommendation mechanism (Seasons/rebalancing) and does not need a financial-tool recommendation grafted onto it.
7. **Defer all Section 6 catalog-only tools** until their specific missing signal (rate feed, threshold detection, explicit stated intent) is separately designed and built — do not promote them as part of this sequence.
8. **Re-run the beta-readiness test plan's route-access and functional gates** after any of the above ships, since new contextual-recommendation logic is exactly the kind of change that could silently violate the existing "never invent a member fact" and "no purchase-loop redirect" rules already governing this product.
