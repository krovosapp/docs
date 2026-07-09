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

**Newlywed / Wedding and Joining Finances**

| Tool | Status |
|---|---|
| Savings Vault System | speced-not-built |
| Household Budget Framework | speced-not-built |
| Trusted Pro Network | speced-not-built |

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

BACKLOG: 529 projection tool.

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
| Tax Planning Toolkit / TaxEngine | backlog (quarterly estimated tax) |

BACKLOG: Solo 401k / SEP / SIMPLE contribution comparison tool.

**Gig Work / Freelance Income**

| Tool | Status |
|---|---|
| Tax Planning Toolkit / TaxEngine | backlog (quarterly estimated tax, SE tax modeling) |

BACKLOG: Income-smoothing and buffer-account engine. Confirmed genuine gap -- no existing product models baseline-from-lowest-month detection, automated buffer routing, or cross-platform effective-hourly-rate tracking net of fees and SE tax.

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
| Insurance Adequacy Analyzer | speced-not-built |
| Annual Enrollment Comparison | speced-not-built (pending Medicare content enhancement) |

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

All 8 net-new tools confirmed as genuine market gaps, listed for prioritization reference.

1. Immigration USCIS-timeline financial modeling tool -- models authorization-gap income planning and immigration cost timeline in one place
2. Rent-vs-buy calculator -- time-horizon-first, opportunity-cost-modeling, modeled on NYT standard
3. Solo 401k / SEP / SIMPLE contribution comparison tool -- retirement plan selection for the self-employed
4. Gig income-smoothing and buffer-account engine -- baseline-from-lowest-month detection, buffer routing, cross-platform effective-rate tracking
5. QDRO navigator -- interactive plan-specific QDRO modeling for divorcing couples
6. Sibling cost-sharing fairness ledger -- caregiver cost allocation with localized cost-of-care data
7. Employer LTD replacement rate analyzer -- models actual compensation replacement percentage against total comp
8. Social Security survivor-benefit optimizer -- claiming-strategy logic in grief-aware UX (shared: Widowhood + Retirement)

---

## Section 5: Tools in Core Product That Appear Across Many Guides

Credit Card Engine, Purchase Strategy Simulator, and Refinance Calculator are situational rather than guide-bound. They trigger on an actual transaction or event, not a life phase. Surface them as core product features that guides can reference contextually rather than assigning them to specific guides.
