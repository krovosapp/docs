# Krovos Open Items and Backlog

Single running source of truth for all open items, deferred decisions, and pending research that do not yet have a permanent home elsewhere in the docs. Check and update this document at the start and end of every working session, the same way CLAUDE.md is treated as living persistent context.

**Last updated: 2026-07-10 (evening -- eMoney gap confirmed; Life Gaps checkup item closed; dual-date bridge logic extracted to shared module)**

---

## Items Requiring Dedicated Scoping (not ready to build)

These tools are confirmed genuine market whitespace with no adequate competitive analogue. None has had a scoping session. None should be handed to a builder without one.

### 1. Immigration USCIS-Timeline Financial Modeling Tool

**What is confirmed:** Genuine whitespace. No existing product models the financial planning gap created by authorization delays alongside the immigration cost timeline in one place. Confirmed during competitive research; no analogue found across any surveyed fintech or planning tool. Assigned to the Immigration and Finances guide.

**What is still open:** Full feature scoping. Inputs required (visa type, filing date, USCIS processing time estimate, immigration legal cost timeline, partner work authorization status, household income if partner authorized vs. not). Output format (monthly cash flow projection through authorization window, cost runway, income gap period). UI approach (guided flow vs. calculator). Whether it overlaps with or references the Runway Calculator already built at /runway.

**Decision needed next:** Scoping session. Do not start implementation until inputs, outputs, and gap/overlap with /runway are resolved.

---

### 2. Blended-Family Decision Engine

**What is confirmed:** Genuine whitespace confirmed in two independent research passes. No existing tool combines FAFSA-timing-vs-remarriage modeling with a plain-language QTIP / bypass / marital-trust decision walkthrough. Shared across two guides: College Planning (FAFSA eligibility timing relative to remarriage date) and Remarriage and Blended Families (trust structure walkthrough). The FAFSA-timing component is its own confirmed gap; the trust walkthrough is its own confirmed gap. The question is whether they belong as one tool or two.

**What is still open:** Whether this is one tool or two. If one: the flow design connecting a FAFSA eligibility question to a trust structure question. If two: where each lives and how they cross-reference. Neither has defined inputs, outputs, or UI approach.

**Decision needed next:** Scoping session. Resolve one-or-two-tool question first, then scope each piece.

---

### 3. QDRO Navigator

**What is confirmed:** Genuine whitespace. No interactive tool exists for plan-specific QDRO (Qualified Domestic Relations Order) modeling for divorcing couples. Current tools on the market require an attorney or QDRO specialist; none provide an interactive planning layer. Assigned to the Divorce and Transition guide.

**What is still open:** Full scoping. Key unknown is what plan-specific modeling means practically, since QDRO rules vary by plan type (401k, 403b, pension, defined benefit vs. defined contribution). Whether the tool models the order itself or models the financial outcome of different division scenarios. Whether it routes to Trusted Pro Network for actual drafting (expected yes, consistent with the legal-document-generation standing decision in the master reference).

**Decision needed next:** Scoping session. Clarify scope boundary between what the tool models and what it refers out.

---

## Architecture Questions Pending Decision

### 1. Dual-Retirement-Date Logic as Shared Infrastructure

**What is confirmed:** The SWR Calculator (built and live at /swr) now includes a complete dual-retirement-date mode with three-phase bridge-period modeling: active earning period, bridge period with partial income coverage, and full withdrawal phase. The logic includes bridge growth rate, partial/full coverage detection, early-withdrawal penalty warning (Rule of 55, SEPP/72(t)), and Life Graph pre-fill for partner name and retirement ages.

The Social Security Survivor Optimizer (backlog, not yet built) also involves two-person retirement date handling, since survivor benefits interact with claiming age for both the deceased and surviving partner.

**What is still open:** Whether the bridge-period and household-timeline modeling built into the SWR Calculator should be extracted into shared infrastructure and reused by CoastFI, any FreedomScenarios tool if built, and LeverAnalysis if built. Currently it lives only in app/swr/page.tsx as self-contained logic. Before any new tool that involves two retirement dates gets built, this overlap should be checked. The SS Survivor Optimizer design may benefit from or conflict with the SWR bridge model; this should be reviewed before the Optimizer is scoped.

**Decision needed next:** Before building any new two-person retirement-date tool, review the SWR bridge logic for reuse potential. Decide whether to extract it as a shared utility or keep tools self-contained.

---

### 2. Life Gaps as Financial Wellness Checkup

**Status: Resolved.** Life Gaps' context-aware 8-question flow, phase-opening recognition, and gap-card output already fulfill the financial wellness checkup function adequately. The tool identifies the user's life situation, surfaces the specific gaps most relevant to their circumstances, and routes to the appropriate next step. No enhancement and no separate internal checkup tool is needed. Closing this item.

---

## eMoney Research Findings (research pass: 2026-07-10, second pass: 2026-07-10)

**What is confirmed:** eMoney Advisor is genuinely superior for advisor-mediated planning depth across every dimension researched. Specific confirmed capabilities include the Decision Center scenario solvers (side-by-side what-if modeling for major financial decisions -- confirmed as an advisor-tier module, present in the product and not removed, simply not surfaced in client-facing navigation), the Vault (secure document repository with advisor and client access), and a tax-aware plan engine that models year-by-year tax impact of planning decisions. These capabilities are real, advisor-facing, and represent a high bar for the category.

**Dual-retirement-date gap: CONFIRMED whitespace.** First-party verification complete. The eMoney Earliest Retirement Age solver explicitly assumes Client and Spouse retire in the same year. There is no mechanism in the current product to model a household where two partners retire at different dates with a bridge period between them. This is a confirmed product gap, not a suspected one. The SWR Calculator's dual-date mode with three-phase bridge-period modeling represents genuine product differentiation with no current eMoney analogue.

**Status: Resolved.** No further research action needed. The dual-retirement-date bridge model is confirmed whitespace as of the second research pass.

---

## Remaining Tool Inventory Backlog

This section tracks tools from the Navigation Engine roadmap (krovos-navigation-engine-master-reference.md, Part 3) and the tool-guide mapping (navigation-engine-tool-guide-mapping.md) that have not yet been built or scoped. The Self-Employed Mode cluster (quarterly tax, retirement plan comparison, gig engine, runway) and the FI/FIRE Mode cluster (CoastFI, SWR) are complete. What remains:

### Phase 1: Financial Foundation (core product, not guide-specific)

| Tool | Status | Notes |
|---|---|---|
| Paycheck Allocation Tool | speced-not-built | Referenced in product-os.mdx. Described as needing to become a real recommendation engine, not just a pay-date reminder. The Update Engine payday check-in (built at /update) is adjacent but different in purpose. |
| Debt Optimizer | speced-not-built | Referenced across multiple guides and CLAUDE.md core product section. No implementation started. |
| Savings Vault System | speced-not-built | Referenced across multiple guides (Newlywed, New Parent). No implementation started. |
| Emergency Fund Calculator | speced-not-built | Referenced in Early Career, Career Transition, Starting a Business guides. No implementation started. |
| Net Worth Tracker | speced-not-built | Listed in core product and Phase 3 Optimizer. A /net-worth route exists in the app (visible in build output) but its content status is unknown -- verify whether it is a real tool or a placeholder before marking complete. |

### Phase 2: Household Operations (core product)

| Tool | Status | Notes |
|---|---|---|
| Document Vault | speced-not-built | Referenced across many guides. Core product. |
| Household Budget Framework | speced-not-built | Referenced across Newlywed, Caregiving, Widowhood, Remarriage guides. |
| Fair Play / Family Calendar | speced-not-built | Designated core product (not guide-gated) per master reference Part 1. |

### Phase 3: Optimizer (core product)

| Tool | Status | Notes |
|---|---|---|
| Credit Card Engine | speced-not-built | Two distinct halves: card selection and card usage optimization. Situational, not guide-bound. |
| Refinance Calculator | speced-not-built | Situational, not guide-bound. |
| Insurance Adequacy Analyzer | speced-not-built | Referenced across Career Transition, New Parent, Disability, Estate guides. |
| Tax Planning Toolkit (TaxEngine integration) | partially built | TaxEngine exists in lib/tax/. Quarterly Tax Calculator uses it. Full Tax Planning Toolkit (Roth conversion modeling, withdrawal sequencing, inheritance 10-year distribution, disability mixed-funding) is not yet built. |
| Annual Enrollment Comparison | speced-not-built | Referenced in Career Transition, New Parent, Disability, Retirement. Medicare content enhancement noted as pending. |
| Purchase Strategy Simulator | speced-not-built | Situational, not guide-bound. |
| Credit Health Dashboard | speced-not-built | Referenced in Career Transition, Divorce, Immigration guides. |
| Trusted Pro Network | speced-not-built | Core product. Referenced across Estate, Caregiving, Widowhood, Remarriage, Divorce, Disability guides. Notary and employer-benefit-check additions noted. |

### Starting Over Mode tools (Divorce and Widowhood guides)

Three tools built 2026-07-10, shared across Divorce and Widowhood guides:

| Tool | Route | Notes |
|---|---|---|
| Emergency Fund Priority Tool | /emergency-fund-priority | Built. Independent savings only, three milestones, urgency classification. Distinct from core product Emergency Fund Calculator by starting-over framing. |
| Single Income Budget | /income-stabilization | Built. Consolidates Income Stabilization Planner and Single Income Adjustment Tool -- confirmed identical concept. Prior vs. current income, eight expense categories, gap/surplus, per-category flags. |
| Credit Rebuilding Timeline | /credit-rebuilding | Built. Situation-specific milestones for separating from joint accounts. Not generic credit building. |

### Confirmed Backlog (genuine whitespace, scoping needed)

Five items remain in the confirmed-gap backlog. Three overlap with the scoping items above. All five are tracked here for completeness.

1. Immigration USCIS-timeline financial modeling tool -- see scoping section above.
2. QDRO navigator -- see scoping section above.
3. Sibling cost-sharing fairness ledger -- caregiver cost allocation with localized cost-of-care data matching CareScout / Genworth. Assigned to Caregiving guide. No scoping session held.
4. Social Security survivor-benefit optimizer -- claiming-strategy logic in grief-aware UX. OpenSocialSecurity-style logic is the confirmed gold standard. Shared: Widowhood and Retirement guides. No scoping session held.
5. Blended-family FAFSA-timing modeling tool -- see scoping section above.

### Phase 4: Young Adult

| Tool | Status | Notes |
|---|---|---|
| Career Path Tool | speced-not-built | Listed in master reference Phase 4. No scoping. |
| Student Loan Strategy | speced-not-built | Listed in master reference Phase 4. No scoping. |
| First Apartment Planner | speced-not-built | Listed in master reference Phase 4. No scoping. |

### Phase 5: Modular Marketplace

The 17 Life Phase Guides are written and live in the docs repo. The guide-specific tools assigned to each are tracked in navigation-engine-tool-guide-mapping.md. What remains in Phase 5 is primarily the Trusted Pro Network build (see Phase 3 above) and any guide-specific tools that don't yet appear in the mapping.

---

## Documentation Health Check Needed

### navigation-engine-tool-guide-mapping.md

**Status: needs verification pass.**

This file has been edited across multiple overlapping sessions in a single day (2026-07-10). The backlog count was manually decremented multiple times as tools moved from speced-not-built to built/live. A count discrepancy was narrowly avoided when a manually edited count did not match the actual list. The file should receive a clean read-through to confirm:

- No duplicate entries (a tool listed under multiple guides that should appear in only one, or appearing in both Section 1 core product and a guide-specific section)
- No stale entries (tools previously marked speced-not-built that were built during the overnight session and may not have been updated)
- Tool descriptions match the actual implementation (particularly the SWR Calculator description, which was updated to reflect the dual-date enhancement, and the Gig Engine, which gained a variance tab)
- The backlog count in Section 4 (currently 5) correctly matches the actual list of 5 items

**Decision needed next:** Manual verification read of the full file. No blocking decision required, but do not use the count from this file as authoritative until the pass is complete.

---

## How to Use This Document

At the start of each working session: read this document and note any items that have resolved since the last update. At the end of each session: update any items that changed, add any new items that surfaced in the session, and update the Last updated date at the top.

Items move out of this document when they have a permanent home: a completed scoping session produces a spec that lives in the appropriate docs/product/ guide file; a resolved architecture question produces a decision entry in the relevant CLAUDE.md Product Decisions Log section; a completed tool build moves to the built/live row of navigation-engine-tool-guide-mapping.md.
