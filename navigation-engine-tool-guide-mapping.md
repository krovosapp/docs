# Navigation Engine: Tool and Guide Reference

This document maps which Navigation Engine tools connect to which guide, records verified build status for every tool, and tracks the confirmed-gap backlog of net-new tools to build. Core product tools are available to every user. Guide-specific tools are surfaced when a user engages with that guide.

---

## Section 1: Core Product Tools

Tools available to every user regardless of guide.

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
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Credit Card Engine | built/live at /credit-card-optimizer |
| Purchase Strategy Simulator | built/live at /purchase-strategy |
| Refinance Calculator | built/live at /refinance-calculator |
| Open Enrollment Comparison | built/live at /open-enrollment-comparison |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy |
| Roth Conversion Modeling | built/live at /roth-conversion |
| Withdrawal Sequencing | built/live at /withdrawal-sequencing |
| Inherited IRA Distribution Strategy | built/live at /inherited-ira-distribution |
| Credit Health Dashboard | built/live at /credit-health |
| Retirement Trajectory | built/live at /retirement-trajectory |
| Rent vs. Buy Calculator | built/live at /rent-vs-buy |

---

## Section 2: Core Product Benchmark Reference

### Money with Katie Wealth Planner: Benchmark for FreedomScenarios and MilestoneEngine Direction

Money with Katie's Wealth Planner validates the FreedomScenarios and MilestoneEngine direction and confirms the Update Engine's differentiation thesis. The benchmark-comparison UX pattern ("you're saving X%, guideline is Y%") should apply directly to the Paycheck Allocation Tool. Krovos extends this model by integrating life-phase context that a standalone planner cannot provide. This is a reference note, not a guide assignment.

FreedomScenarios status: built as a component (app/components/FreedomScenarios.tsx), embedded in Life View and Preview pages. Supports single-date and dual-retirement-date modes. Dual mode: projects portfolio to first retirement age, calls computeBridgePeriod() per scenario, renders BridgeTimeline inline within each scenario card showing bridge phase coverage. FI achievement status shown per scenario at withdrawal start.

---

## Section 3: Guide-Specific Tools

### Starting Out

**Early Career Starter Kit**

| Tool | Status |
|---|---|
| Debt Optimizer | built/live at /debt-optimizer |
| Emergency Fund Calculator | built/live at /emergency-fund |
| Credit Card Engine | built/live at /credit-card-optimizer |

| Early Career Decision Engine | built/live at /early-career |

Five-step guided flow: 401k match optimization, Roth vs. traditional recommendation (2026 bracket-aware), emergency fund progress bars (1-month / 3-month / 6-month targets), credit building informational panel, consolidated summary screen. No auth required.

**Newlywed / Wedding and Joining Finances**

| Tool | Status |
|---|---|
| Savings Vault System | built/live at /savings-vaults |
| Household Budget Framework | speced-not-built |
| Trusted Pro Network | speced-not-built |

| Finance Structure Comparison | built/live at /finance-structure |

Three-column comparison: fully combined, fully separate (equal-dollar or proportional split), hybrid. Per-partner savings breakdown, individual contribution rates, 5-year net worth projection at 6% growth. Highest-outcome structure highlighted in gold. No auth required.

**Immigration and Finances**

| Tool | Route | Status |
|---|---|---|
| Authorization-Gap Bridge Planner | /auth-gap | built, live |
| Path-to-Residency Cost Planner | /residency-costs | built, live |
| Credit-Building-From-Zero | /credit-building | built, live |
| Household Budget Framework | -- | speced-not-built |
| Trusted Pro Network | -- | speced-not-built |
| Credit Health Dashboard | -- | backlog |

Authorization-Gap Bridge Planner: Visa type, USCIS processing time estimate (user-entered, links to egov.uscis.gov/processing-times/), household spending, continuing income, savings. Three-phase timeline (filing / waiting / authorized). Shows monthly gap, runway, flags when USCIS estimate exceeds savings. Path-to-Residency Cost Planner: Hardcoded USCIS fee table (last verified April 1, 2024 -- I-485 $1,440 all-in, N-400 $760, I-130 $675, I-140 $715, I-526 $11,160), attorney fee input, timeline in years, monthly savings target, optional progress tracking. Credit-Building-From-Zero: Country-of-origin field for Nova Credit eligibility (Mexico, India, UK, Canada, Australia, and others), three starting points, authorized-user option as parallel accelerant, milestone sequence from no-history to conventional credit access. Separate from /credit-rebuilding (divorce/widowhood).

---

### Building a Household

**Home Buying**

| Tool | Status |
|---|---|
| Refinance Calculator | built/live at /refinance-calculator |
| Rent vs. Buy Calculator | built/live at /rent-vs-buy |

**New Parent / Pregnancy, Baby and Family Leave**

| Tool | Status |
|---|---|
| Savings Vault System | built/live at /savings-vaults |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy |
| Open Enrollment Comparison | built/live at /open-enrollment-comparison |
| Parental Leave Navigator | built/live at /parental-leave-navigator |
| Childcare Bubble Calculator | built/live at /childcare-bubble |
| Children's Planning Hub | built/live at /childrens-planning |
| Life Insurance Needs Calculator | built/live at /life-insurance-needs |
| Document Vault | speced-not-built |
| Fair Play household labor framework | built/live in core product; surfaced here at the moment it matters most |

**College Planning**

| Tool | Status |
|---|---|
| Tax Planning Toolkit | backlog (for state 529 deduction comparison) |

| 529 Projection Tool | built/live at /college-529 |

Child age, school type (in-state public / out-of-state public / private), current balance, monthly contribution, expected return rate. Monthly compound projection loop, year-by-year table, 3-summary cards, color-coded coverage bar (gold >=100%, mist blue >=60%, ember orange <60%). 2026 base costs. No auth required.

BACKLOG: State 529 deduction comparison (requires per-state deduction data; Tax Planning Toolkit integration needed).

FAFSA Remarriage-Timing Planner: built/live at /fafsa-remarriage-timing. Shared with Blended Family guide. See Remarriage and Blended Families section for full description.

---

### Career

**Career Transition**

| Tool | Status |
|---|---|
| Emergency Fund Calculator | built/live at /emergency-fund |
| Credit Health Dashboard | built/live at /credit-health |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy |
| Open Enrollment Comparison | built/live at /open-enrollment-comparison |

BENCHMARK NOTE: WealthSpott's equity-discounting offer comparison model is the confirmed bar for a future enhancement to offer comparison logic in this guide.

**Starting a Business**

| Tool | Status |
|---|---|
| Quarterly Tax Calculator | built/live at /quarterly-tax |
| Retirement Plan Comparison (Solo 401k / SEP / SIMPLE) | built/live at /retirement-plan-comparison |

Quarterly Tax Calculator: SE income + filing status -> SE tax + income tax via TaxEngine -> quarterly payment schedule (4 due dates, 2026). Safe harbor methods: 90% current-year (default), 100% prior-year (when known), or equal installments.

Retirement Plan Comparison: Solo 401k / SEP-IRA / SIMPLE IRA contribution limits, eligibility, complexity, and recommendation logic. Uses SE income, business structure, employees, age, and filing status.

**Gig Work / Freelance Income**

| Tool | Status |
|---|---|
| Gig Income Engine | built/live at /gig-engine |
| Runway Calculator | built/live at /runway |

Gig Income Engine: Income history (multi-entry, multi-platform), baseline-from-lowest-month detection, buffer routing, effective hourly rate, platform comparison, and income variance tab (bar chart vs. running average, std deviation, month-by-month table).

Runway Calculator: Liquid savings + monthly expenses -> months of runway if income stops. Optional trailing income inputs compute partial-income extension (50% of avg scenario). Color-coded at <3mo / 3-6mo / 6mo+ thresholds.

---

### Life Changes

**Divorce and Transition**

| Tool | Status |
|---|---|
| Debt Optimizer | built/live at /debt-optimizer |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Credit Health Dashboard | speced-not-built |
| Emergency Fund Priority Tool | built/live at /emergency-fund-priority |
| Single Income Budget | built/live at /income-stabilization |
| Credit Rebuilding Timeline | built/live at /credit-rebuilding |
| QDRO Readiness Prep | built/live at /qdro-readiness |
| QDRO Tracker and Award Estimator | built/live at /qdro-tracker |

Emergency Fund Priority Tool: Independent savings (excludes joint/contested funds) + monthly expenses -> three milestones (1/3/6 months), urgency classification (critical/low/building/solid), optional income input for timeline estimate. Starting-over-specific framing distinct from core product Emergency Fund Calculator.

Single Income Budget: Prior combined income + current single income + eight expense categories -> gap/surplus summary, income reduction percentage, per-category flags (housing >35% of income = high, childcare >15% = watch), expandable guidance notes. Consolidates Income Stabilization Planner and Single Income Adjustment Tool (same concept, one tool).

Credit Rebuilding Timeline: Three situation inputs (individual history / authorized user / joint-only) + joint account status + score range + optional joint mortgage -> personalized milestone list across Now / 30-60 days / 3-6 months / 6-12 months / 12-24 months. Separation-context-specific, not generic credit building.

QDRO Readiness Prep: Non-numeric. Account type multi-select (401k, 403b, pension, IRA, government/military). IRA path immediately clarifies no QDRO needed and explains transfer-incident-to-divorce process. Non-IRA path: QDRO status (none/drafted/submitted/approved), decree status (not mentioned/mentioned/finalized pending/in progress), timeline (in progress/recent/6-24 months/over 2 years). Status assessment (on track/at risk/needs immediate attention) with plain-language explanation of urgency risk. Attorney checklist tailored to account type and situation. Glossary: alternate payee, separate interest vs. shared payment, plan administrator, coverture fraction. Prominent disclaimer above first input.

QDRO Tracker and Award Estimator: For decree-finalized situations with a specified account division. Plan type (401k/403b, pension, IRA, government/military). Defined-contribution: percentage or fixed-dollar award applied to current balance -> calculated award amount. Defined-benefit pension: coverture fraction (marriage years during service / total service years) shown transparently as a calculation step; result presented as a monthly range with stated assumptions (shared payment vs. separate interest), never a single precise number; plan administrator's calculation noted as authoritative. IRA: transfer-incident-to-divorce step list. Government/military: agency-specific routing (OPM for FERS/CSRS, TSP board, DFAS for military). QDRO status tracker with urgency flags. Survivor benefit alert when not addressed in pension decree. Life Graph pre-fill for investments_total (best-effort, silent fallback if unauthenticated).

**Caregiving**

| Tool | Status |
|---|---|
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built (includes geriatric care manager referral) |
| Household Budget Framework | speced-not-built |
| Caregiver Ledger | built/live at /caregiver-ledger |

**Disability Insurance and Personal Illness**

| Tool | Status |
|---|---|
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy |
| LTD Analyzer | built/live at /ltd-analyzer |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Tax Planning Toolkit | backlog (for mixed-funding tax treatment calculation) |

BACKLOG: Employer LTD vs. total-compensation replacement rate analyzer. Confirmed genuine gap -- no existing tool models what percentage of total compensation an LTD policy actually replaces when modeled against full comp including benefits.

**Widowhood**

| Tool | Status |
|---|---|
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Household Budget Framework | speced-not-built |
| Emergency Fund Priority Tool | built/live at /emergency-fund-priority (shared with Divorce) |
| Single Income Budget | built/live at /income-stabilization (shared with Divorce) |
| Credit Rebuilding Timeline | built/live at /credit-rebuilding (shared with Divorce) |

Social Security Survivor-Benefit Optimizer: built/live at /ss-survivor-optimizer. Claiming-strategy logic, grief-aware UX. Shared with Retirement guide.

**Remarriage and Blended Families**

| Tool | Route | Status |
|---|---|---|
| FAFSA Remarriage-Timing Planner | /fafsa-remarriage-timing | built, live |
| QTIP vs. Bypass Trust Decision Walkthrough | /qtip-bypass-decision | built, live |
| Document Vault | -- | speced-not-built |
| Trusted Pro Network | -- | speced-not-built |
| Household Budget Framework | -- | speced-not-built |

FAFSA Remarriage-Timing Planner: Custodial parent income and assets, new spouse income and assets, college start year, household size, number of children needing aid. Runs SAI calculation twice (stepparent included vs. excluded) using FAFSA Simplification Act methodology and prior-prior year income rule. Side-by-side comparison with annual and 4-year aid impact difference. Framing presents the figure as one input to a personal decision, not a recommendation to time a marriage around financial aid. Shared with College Planning guide.

QTIP vs. Bypass Trust Decision Walkthrough: Five-step sequential flow (estate size, children relationship, spouse income need, state of residence, trust-in-spouse question). Three outcomes: QTIP trust likely fits, bypass trust likely fits, or will with bequests may suffice. Generates a personalized attorney question list based on answers. Prominent disclaimer positions the tool as prep work for an attorney meeting, not a legal recommendation. State-specific alerts for estate and inheritance tax states. Shared with Estate Planning guide.

---

### Later Life

**Retirement**

| Tool | Status |
|---|---|
| Roth Conversion Modeling | built/live at /roth-conversion |
| Withdrawal Sequencing | built/live at /withdrawal-sequencing |
| Safe Withdrawal Rate Calculator | built/live at /swr |
| CoastFI Calculator | built/live at /coast-fi |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy |
| Open Enrollment Comparison | built/live at /open-enrollment-comparison |

Safe Withdrawal Rate Calculator: Portfolio + annual spending + retirement age -> implied withdrawal rate vs. Money Guy Show six-band age-adjusted table (under 45: 3.0% through above 75: 5.5%). Morningstar 3.9% and Bengen 4.7% as reference anchors. Variable tolerance bands. Single-date and dual-retirement-date modes. Dual mode: three-phase bridge timeline, bridge growth modeling, partial/full coverage detection, early-withdrawal penalty warning (Rule of 55 and SEPP/72(t) named). Life Graph pre-fill for partner name and retirement ages.

CoastFI Calculator: Current portfolio + annual spending (25x FI number) + age + return rate -> CoastFI threshold, progress percentage, and projected portfolio at retirement with no further contributions. Custom FI number override. Single-date and dual-retirement-date modes. Dual mode: calls computeBridgePeriod() to model bridge growth and drawdown, then checks whether portfolioAtWithdrawalStart meets the FI number. Shows pre-bridge and post-bridge status separately. Life Graph pre-fill for portfolio, retirement ages, and annual spending.

BACKLOG: Social Security survivor-benefit optimizer (shared with Widowhood guide).

**Estate Planning**

| Tool | Status |
|---|---|
| QTIP vs. Bypass Trust Decision Walkthrough | built/live at /qtip-bypass-decision (shared with Blended Family guide) |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built (includes notary and employer-benefit-check additions) |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy |

**Inheritance**

| Tool | Status |
|---|---|
| Inherited IRA Distribution Strategy | built/live at /inherited-ira-distribution |

Inherited IRA Distribution Strategy: SECURE Act 10-year rule. Level, front-loaded, and back-loaded strategy comparison with year-by-year tax modeling. Per-year income override grid for retirement or career-break scenarios. Best strategy highlighted in gold.

---

## Section 4: Confirmed Tool Gap Backlog

0 net-new tools remaining. All confirmed gaps are now built.

Removed from backlog (now built): Solo 401k / SEP / SIMPLE comparison tool (/retirement-plan-comparison), income-smoothing and buffer engine (/gig-engine), distribution-strategy comparison (separate tool), state 529 deduction comparison (out of scope until per-state data available), immigration USCIS-timeline financial modeling tool (three tools built 2026-07-10), blended-family FAFSA-timing modeling tool (/fafsa-remarriage-timing), QTIP/bypass trust walkthrough (/qtip-bypass-decision), QDRO navigator (two tools: /qdro-readiness and /qdro-tracker, built 2026-07-10), sibling cost-sharing fairness ledger (/caregiver-ledger, built 2026-07-11), Social Security survivor-benefit optimizer (/ss-survivor-optimizer, built 2026-07-11).

---

## Section 5: Tools in Core Product That Appear Across Many Guides

Credit Card Engine (/credit-card-optimizer, built/live), Purchase Strategy Simulator (/purchase-strategy, built/live), and Refinance Calculator (/refinance-calculator, built/live) are situational rather than guide-bound. They trigger on an actual transaction or event, not a life phase. Surface them as core product features that guides can reference contextually rather than assigning them to specific guides.

Credit Health Dashboard (/credit-health, built/live) is a hub that provides a credit snapshot, utilization optimizer, FICO-band key actions, and routing to /credit-card-optimizer and /credit-rebuilding.
