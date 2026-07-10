# Navigation Engine: Tool and Guide Reference

This document maps which Navigation Engine tools connect to which guide, records verified build status for every tool, and tracks the confirmed-gap backlog of net-new tools to build. Core product tools are available to every user. Guide-specific tools are surfaced when a user engages with that guide.

---

## Section 1: Core Product Tools

Tools available to every user regardless of guide.

| Tool | Status |
|---|---|
| Net Worth Tracker | speced-not-built |
| Life Compass | built/live |
| Life Calendar | built/live |
| Krovos Guide | built/live |
| Financial Modes | built/live (detection and display layer; individual mode experiences are speced-not-built) |
| Household Budget Framework | speced-not-built |
| Fair Play household labor framework | speced-not-built |
| Debt Optimizer | speced-not-built |
| Savings Vault System | speced-not-built |
| Emergency Fund Calculator | speced-not-built |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Credit Card Engine | speced-not-built |
| Purchase Strategy Simulator | speced-not-built |
| Refinance Calculator | speced-not-built |

---

## Section 2: Core Product Benchmark Reference

### Money with Katie Wealth Planner: Benchmark for FreedomScenarios and MilestoneEngine Direction

Money with Katie's Wealth Planner validates the FreedomScenarios and MilestoneEngine direction and confirms the Update Engine's differentiation thesis. The benchmark-comparison UX pattern ("you're saving X%, guideline is Y%") should apply directly to the Paycheck Allocation Tool. Krovos extends this model by integrating life-phase context that a standalone planner cannot provide. This is a reference note, not a guide assignment.

---

## Section 3: Guide-Specific Tools

### Starting Out

**Early Career Starter Kit**

| Tool | Status |
|---|---|
| Debt Optimizer | speced-not-built |
| Emergency Fund Calculator | speced-not-built |
| Credit Card Engine | speced-not-built |

| Early Career Decision Engine | built/live at /early-career |

Five-step guided flow: 401k match optimization, Roth vs. traditional recommendation (2026 bracket-aware), emergency fund progress bars (1-month / 3-month / 6-month targets), credit building informational panel, consolidated summary screen. No auth required.

**Newlywed / Wedding and Joining Finances**

| Tool | Status |
|---|---|
| Savings Vault System | speced-not-built |
| Household Budget Framework | speced-not-built |
| Trusted Pro Network | speced-not-built |

| Finance Structure Comparison | built/live at /finance-structure |

Three-column comparison: fully combined, fully separate (equal-dollar or proportional split), hybrid. Per-partner savings breakdown, individual contribution rates, 5-year net worth projection at 6% growth. Highest-outcome structure highlighted in gold. No auth required.

**Immigration and Finances**

| Tool | Status |
|---|---|
| Household Budget Framework | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Credit Health Dashboard | backlog |

BACKLOG NOTE: Immigration-specific USCIS-timeline financial modeling tool. Confirmed genuine whitespace -- no existing product models the financial planning gap created by authorization delays alongside the immigration cost timeline. No precedent to match.

---

### Building a Household

**Home Buying**

| Tool | Status |
|---|---|
| Refinance Calculator | speced-not-built |

BACKLOG: Rent-vs-buy calculator. Gold standard: NYT's calculator (opportunity cost modeling, time-horizon-first UX, no paywall). Build to match.

**New Parent / Pregnancy, Baby and Family Leave**

| Tool | Status |
|---|---|
| Savings Vault System | speced-not-built |
| Insurance Adequacy Analyzer | speced-not-built |
| Annual Enrollment Comparison | speced-not-built |
| Document Vault | speced-not-built |
| Fair Play household labor framework | built/live in core product; surfaced here at the moment it matters most |

**College Planning**

| Tool | Status |
|---|---|
| Tax Planning Toolkit | backlog (for state 529 deduction comparison) |

| 529 Projection Tool | built/live at /college-529 |

Child age, school type (in-state public / out-of-state public / private), current balance, monthly contribution, expected return rate. Monthly compound projection loop, year-by-year table, 3-summary cards, color-coded coverage bar (gold >=100%, mist blue >=60%, ember orange <60%). 2026 base costs. No auth required.

BACKLOG: State 529 deduction comparison (requires per-state deduction data; Tax Planning Toolkit integration needed).

BACKLOG (separate, unaffected): Blended-family FAFSA-timing modeling gap. No existing tool models the strategic timing interaction between a remarriage date and FAFSA eligibility windows, or the tradeoff between custodial-parent vs. stepparent income exposure under FAFSA methodology. Confirmed genuine whitespace. Shared backlog item with Blended Family guide.

---

### Career

**Career Transition**

| Tool | Status |
|---|---|
| Emergency Fund Calculator | speced-not-built |
| Credit Health Dashboard | speced-not-built |
| Insurance Adequacy Analyzer | speced-not-built |
| Annual Enrollment Comparison | speced-not-built |

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
| Debt Optimizer | speced-not-built |
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Credit Health Dashboard | speced-not-built |

BACKLOG: QDRO navigator. Confirmed genuine gap -- no interactive tool exists for plan-specific QDRO modeling.

**Caregiving**

| Tool | Status |
|---|---|
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built (includes geriatric care manager referral) |
| Household Budget Framework | speced-not-built |

BACKLOG: Sibling cost-sharing fairness ledger tool. Confirmed gap. Cost-of-care estimation should match CareScout / Genworth localized data as the confirmed bar.

**Disability Insurance and Personal Illness**

| Tool | Status |
|---|---|
| Insurance Adequacy Analyzer | speced-not-built |
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

BACKLOG: Social Security survivor-benefit optimizer. Gold standard: OpenSocialSecurity-style claiming-strategy logic, wrapped in grief-aware UX. Shared backlog item with Retirement guide.

**Remarriage and Blended Families**

| Tool | Status |
|---|---|
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built |
| Household Budget Framework | speced-not-built |

BACKLOG: Blended-family decision engine. Confirmed genuine gap -- no existing tool combines FAFSA-timing-vs-remarriage modeling with a plain-language QTIP / bypass / marital-trust decision walkthrough.

---

### Later Life

**Retirement**

| Tool | Status |
|---|---|
| Tax Planning Toolkit / TaxEngine | backlog (Roth conversion modeling and withdrawal sequencing) |
| Safe Withdrawal Rate Calculator | built/live at /swr |
| CoastFI Calculator | built/live at /coast-fi |
| Insurance Adequacy Analyzer | speced-not-built |
| Annual Enrollment Comparison | speced-not-built (pending Medicare content enhancement) |

Safe Withdrawal Rate Calculator: Portfolio + annual spending + retirement age -> implied withdrawal rate vs. Money Guy Show six-band age-adjusted table (under 45: 3.0% through above 75: 5.5%). Morningstar 3.9% and Bengen 4.7% as reference anchors. Variable tolerance bands. Single-date and dual-retirement-date modes. Dual mode: three-phase bridge timeline, bridge growth modeling, partial/full coverage detection, early-withdrawal penalty warning (Rule of 55 and SEPP/72(t) named). Life Graph pre-fill for partner name and retirement ages.

CoastFI Calculator: Current portfolio + annual spending (25x FI number) + age + return rate -> CoastFI threshold, progress percentage, and projected portfolio at retirement with no further contributions. Custom FI number override.

BACKLOG: Social Security survivor-benefit optimizer (shared with Widowhood guide).

**Estate Planning**

| Tool | Status |
|---|---|
| Document Vault | speced-not-built |
| Trusted Pro Network | speced-not-built (includes notary and employer-benefit-check additions) |
| Insurance Adequacy Analyzer | speced-not-built |

**Inheritance**

| Tool | Status |
|---|---|
| Tax Planning Toolkit / TaxEngine | backlog (10-year distribution modeling) |

BACKLOG: Distribution-strategy comparison engine. Confirmed genuine gap -- no existing calculator lets a user compare level, front-loaded, and back-loaded distribution strategies with real year-by-year tax modeling across the 10-year window.

---

## Section 4: Confirmed Tool Gap Backlog

5 net-new tools remaining as confirmed genuine market gaps. Built and live since last update: quarterly tax calculator, retirement plan comparison (Solo 401k / SEP / SIMPLE), gig income engine with variance tab, runway calculator, CoastFI calculator, SWR calculator.

1. Immigration USCIS-timeline financial modeling tool -- models authorization-gap income planning and immigration cost timeline in one place
2. QDRO navigator -- interactive plan-specific QDRO modeling for divorcing couples
3. Sibling cost-sharing fairness ledger -- caregiver cost allocation with localized cost-of-care data
4. Social Security survivor-benefit optimizer -- claiming-strategy logic in grief-aware UX (shared: Widowhood + Retirement)
5. Blended-family FAFSA-timing modeling tool -- remarriage date vs. FAFSA eligibility window tradeoffs, custodial vs. stepparent income exposure under FAFSA methodology (shared: College Planning + Blended Family); confirmed whitespace

Removed from backlog (now built): Solo 401k / SEP / SIMPLE comparison tool (built as /retirement-plan-comparison), income-smoothing and buffer engine (built as /gig-engine), distribution-strategy comparison (built as separate tool), state 529 deduction comparison (out of scope until per-state data available).

---

## Section 5: Tools in Core Product That Appear Across Many Guides

Credit Card Engine, Purchase Strategy Simulator, and Refinance Calculator are situational rather than guide-bound. They trigger on an actual transaction or event, not a life phase. Surface them as core product features that guides can reference contextually rather than assigning them to specific guides.
