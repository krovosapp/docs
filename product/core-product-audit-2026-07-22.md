# Core Product Audit

**Date:** July 22, 2026
**Audit type:** Source, data-flow, and product-design review
**Current overall product-design score:** 91/A-
**Target:** 97/A+ or higher before founder walkthroughs begin

This audit evaluates whether Core delivers the Krovos promise: white-glove burden relief, personalized navigation, durable account-backed memory, safe correction, and useful action across time. A route existing is not evidence that the workflow is complete. Scores below do not become production-readiness scores until authenticated walkthroughs confirm them.

## Scoring standard

Each area is evaluated on personalization, persistence, actionability, burden relief, correction and recovery, household handling, safety and evidence boundaries, and integration with the rest of Core. A feature cannot earn A-level status if a key outcome disappears on refresh, requires the member to re-enter known facts, records a change without applying it, or exposes another member's information.

## Executive findings

1. **The member-workflow authorization boundary requires remediation.** A private security review identified multiple routes that must bind member-owned reads and writes to a verified session before beta. Detailed affected-route information is intentionally maintained only in the private Krovos OS repository.
2. **Krovos Guide memory and cost controls are now built.** Full RLS-protected conversations persist across refresh and devices while model context is bounded to recent messages. Daily and monthly response caps, question length, output limits, and actual token metering constrain cost. Controlled authenticated QA remains.
3. **The Update Engine now has governed exact-value apply paths and completed downstream refresh processing.** Payday check-ins capture and review recurring take-home, a new named monthly expense, an existing debt balance, and current cash/savings balances before applying them with before/after history. One-time pay changes do not overwrite the baseline. Existing-expense changes and brand-new debts route to their full records because one number is insufficient. Typed and dated life changes enter a durable reconciliation inbox. Applied changes refresh current Core context, mark affected saved plans for review, and create a member-visible reconciliation notice.
4. **Core money tools now meet A-range product design, with controlled verification still required.** Eight Core workflows save and restore outcomes: Emergency Fund, Debt Payoff Planner, Insurance Gap Checker, Paycheck Planner, Savings Goals, Open Enrollment Comparison, Refinance Calculator, and Rent vs. Buy. Rent vs. Buy persists for every Core member while still mirroring into Home Buying when owned. Refinance now compares the actual remaining term and balances rather than assuming a new 30-year current loan. Purchase Planner and the remaining decision-shaped Core surfaces still need review.
5. **Life Compass now meets the governed three-zone product design at A level; Life Graph remains incomplete against its historical specification.** Compass governs one strongest current focus, provides payday context, renders a fact-derived six-month timeline with expandable action detail, orders Life Graph health critical-first, and maintains a persistent Krovos Guide handoff without spending a response until the member sends. Competing local-only and speculative priority surfaces were removed. Authenticated persona, mobile, accessibility, boundary-date, and restoration QA remain release gates. Life Graph labels every relationship and explains the captured and missing facts behind each completeness score, but still lacks a historical arc and correction provenance.
6. **Life Calendar has begun moving from generated display toward a managed system.** It derives useful events from Life Graph and now renders dated Krovos tasks as independently filterable household, personal, work, and guide-linked layers. Some dates remain estimates, and durable creation, correction, recurrence expansion, imports, and notifications are not complete.
7. **Retirement is the current Core benchmark.** Its saved operating plan, deterministic Investment Priority, calculator results, and decision records demonstrate the architecture the rest of Core should follow.

## Scorecard

| Core area | Score | Grade | What works | What prevents A+ |
|---|---:|:---:|---|---|
| Life Profile and document-assisted intake | 92 | A- | Eight-step adaptive intake, document upload/extraction, correction path, durable Life Graph | Extraction and correction need authenticated accuracy, duplicate, failure, and document-retention testing; some declared fields remain unwired |
| Life View | 94 | A | Personalized durable report, visible historical status and generation date, current-Life-Graph change detection, confirmed change-category audit trail, current-plan/update routing, portable source metadata for new reports, and clearly separated live planning tools | Authenticated factual-accuracy, legacy-report, change-log restoration, mobile, and accessibility QA remain before production-readiness grading |
| Life Compass | 94 | A | Governed three-zone orientation, single-focus routing, change reconciliation, prefilled Guide handoff, correct greeting behavior, payday context, fact-derived six-month timeline with progress/action detail, real report actions, saved outcomes, goals, and critical-first zone health | Authenticated persona, mobile, accessibility, milestone-boundary, and restoration QA remain before production-readiness grading |
| Life Graph | 90 | A- | Durable central record, ten-node map, named relationships, shared scoring, node-level evidence and missing-input rationale, profile correction links, keyboard-accessible nodes | No historical scoring arc or correction provenance; authenticated household edge cases remain unverified |
| Krovos Guide | 93 | A- design | Personalized Life Graph context, durable conversations, refresh/device restoration, archive flow, bounded model context, daily/monthly caps, actual token metering | Full authorization inventory and authenticated restoration/cap tests remain; no confirmed-change handoff into the Update Engine yet |
| Update Engine | 96 | A design | Low-burden check-in, durable snapshots, side-by-side review, exact income/expense/debt/savings application, change history, life-change inbox, honest unresolved routing, month-end gig reconciliation, completed refresh processing, plan-staleness controls, and member-visible cross-system review | Richer goal-specific realignment, recovery/error UX, and controlled authenticated testing remain |
| Life Goals | 94 | A | Durable goals and contributions, explicit individual/household scope, permission-gated connected-partner editing, before/after audit history, stale-write protection, preserved completed/abandoned history, and derived-to-stored precedence | Authenticated two-person permission, denial, concurrency, restoration, mobile, and accessibility QA remain; only genuinely goal-shaped tool outcomes should receive future goal handoffs |
| Life Calendar | 78 | C+ | Useful derived paydays, bills, goals, and life events; durable dated-task schema and independently filterable household, personal, work, and guide-linked layers | Estimated anchors can look authoritative; no durable in-calendar creation/editing, recurrence expansion, source correction, external imports, or notification workflow |
| Net Worth | 93 | A | Life Graph-derived assets/liabilities and durable snapshots | Needs correction/source traceability, household edge cases, import reconciliation, and authenticated restoration testing |
| Core money tools | 90 | A- design | Eight saved/restorable Core outcomes; explicit Paycheck, Open Enrollment, and Refinance choices; Savings Goals-to-Life Goals handoff; corrected HSA and refinance comparisons; Core-wide Rent vs. Buy persistence with optional guide mirroring | Purchase Planner and other remaining decision-shaped surfaces need review; authenticated restoration, calculation-boundary, error, mobile, and accessibility testing remain incomplete |
| Household connection | 78 | C+ | Sharing settings, merged display, invitation/access lifecycle | Connected write paths remain incomplete; ownership and correction semantics need end-to-end verification |
| Core Retirement | 99 | A+ design | Saved plan, saved calculator results, deterministic prioritization, healthcare/Medicare/pension evidence | Authenticated, mobile, household, accessibility, and restoration testing still required before production-readiness grading |

## Immediate release blockers

### P0: Repair the member-workflow authorization boundary

- Bind every member-owned read and mutation to a verified session.
- Keep intentionally public tokenized and webhook workflows on separate, purpose-specific authorization paths.
- Add unauthenticated and cross-user tests proving access and mutation fail.
- Track affected routes, exact test cases, and completion evidence in the private Krovos OS security remediation record.

### P0: Finish Krovos Guide verification and handoff

- Verify conversation restoration, selection, archive, RLS, and cap boundaries with controlled authenticated accounts.
- Add explicit rename controls if beta behavior shows automatic first-message titles are insufficient.
- Feed confirmed changes into a reviewable Update Engine proposal rather than silently changing Life Graph.

### P0: Close the Update Engine loop

- Convert a check-in into explicit proposed changes.
- Show old value, new value, downstream effects, and the member's confirmation.
- Apply confirmed changes to Life Graph and affected goals, milestones, plan actions, and Guide context.
- Route off-track items into a saved realignment conversation.
- Add the approved end-of-month gig-income workflow.

## Upgrade sequence

### Batch 1: Trust and memory

1. Repair and test the member-workflow authorization boundary using the private remediation record.
2. Complete authenticated Guide memory and cap verification.
3. Build proposed-change review and the Update Engine apply path.

### Batch 2: Durable Core money system

1. Define a shared `core_tool_results` record using the proven Retirement pattern.
2. Paycheck Planner, Emergency Fund, Debt Payoff Planner, Savings Goals, Insurance Gap Checker, and Open Enrollment Comparison are connected; continue through the remaining Core tools.
3. Let saved outcomes create or update a goal, decision, deadline, or Life Graph correction only after member confirmation.
4. Continue through the remaining Core tools and remove all session-only outcome language once persistence is real.

### Batch 3: Navigation over time

1. Run authenticated Life Compass QA across test personas, mobile/accessibility states, milestone boundaries, Guide handoff, dismissal, and cross-device restoration; the governed three-zone design is complete.
2. Add Life Graph score history and correction provenance; evidence explanations and relationship labels are now complete.
3. Complete durable calendar creation/editing, source labels, correction, recurrence expansion, imports, reminders, and notification workflows. The dated household/task overlay foundation is complete.
4. Finish connected household goal and update permissions.

### Batch 4: Controlled verification

1. Run security tests before using any realistic test-user data.
2. Run founder accounts through onboarding, report, Compass, Guide, updates, goals, calendar, tools, Retirement, household connection, cancellation, and re-subscription.
3. Verify cross-device restoration, mobile, keyboard and screen-reader behavior, failure recovery, stale facts, duplicate submissions, and contradictory documents.
4. Re-score from observed behavior and resolve every score below 97 before beta.

## Product decision

Core should use the Retirement architecture as its default pattern: reuse the existing Life Graph and Life View, ask only for missing or changed facts, calculate without another model call where possible, save the outcome and evidence, create the relevant follow-up, and remain correctable. The audit does not recommend adding more surface area until the trust, memory, and persistence gaps above are closed.
