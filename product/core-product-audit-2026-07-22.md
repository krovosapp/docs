# Core Product Audit

**Date:** July 22, 2026
**Audit type:** Source, data-flow, and product-design review
**Current overall product-design score:** 80/B-
**Target:** 97/A+ or higher before founder walkthroughs begin

This audit evaluates whether Core delivers the Krovos promise: white-glove burden relief, personalized navigation, durable account-backed memory, safe correction, and useful action across time. A route existing is not evidence that the workflow is complete. Scores below do not become production-readiness scores until authenticated walkthroughs confirm them.

## Scoring standard

Each area is evaluated on personalization, persistence, actionability, burden relief, correction and recovery, household handling, safety and evidence boundaries, and integration with the rest of Core. A feature cannot earn A-level status if a key outcome disappears on refresh, requires the member to re-enter known facts, records a change without applying it, or exposes another member's information.

## Executive findings

1. **The member-workflow authorization boundary requires remediation.** A private security review identified multiple routes that must bind member-owned reads and writes to a verified session before beta. Detailed affected-route information is intentionally maintained only in the private Krovos OS repository.
2. **Krovos Guide memory and cost controls are now built.** Full RLS-protected conversations persist across refresh and devices while model context is bounded to recent messages. Daily and monthly response caps, question length, output limits, and actual token metering constrain cost. Controlled authenticated QA remains.
3. **The Update Engine now has governed exact-value apply paths.** Payday check-ins capture and review recurring take-home, a new named monthly expense, an existing debt balance, and current cash/savings balances before applying them with before/after history. One-time pay changes do not overwrite the baseline. Existing-expense changes and brand-new debts route to their full records because one number is insufficient. Typed and dated life changes enter a durable reconciliation inbox. Applied changes create downstream refresh work for Life Compass, Life Goals, saved plans, and Krovos Guide; consuming that queue and completing automatic realignment remain.
4. **Several advertised Core tools are calculators rather than durable Krovos workflows.** Paycheck Planner, Savings Goals, Emergency Fund, Debt Payoff Planner, Open Enrollment Comparison, Insurance Gap Checker, and other Core tools pre-fill some known facts but generally do not save the completed decision or feed Core forward. Savings Goals explicitly states that its data is session-only.
5. **Life Compass and Life Graph are useful but incomplete against their governing specifications.** The current Compass includes real report actions, goals, payday context, and health summaries, but lacks the fully governed urgent-callout routing, integrated milestone strip, and automatic update response. Life Graph lacks historical arc, labeled relationships, and detailed evidence/rationale panels.
6. **Life Calendar is generated rather than managed.** It derives useful events from Life Graph, but some dates are estimates, there is no durable user-created event layer, and the approved schedule/chore overlay is not present.
7. **Retirement is the current Core benchmark.** Its saved operating plan, deterministic Investment Priority, calculator results, and decision records demonstrate the architecture the rest of Core should follow.

## Scorecard

| Core area | Score | Grade | What works | What prevents A+ |
|---|---:|:---:|---|---|
| Life Profile and document-assisted intake | 92 | A- | Eight-step adaptive intake, document upload/extraction, correction path, durable Life Graph | Extraction and correction need authenticated accuracy, duplicate, failure, and document-retention testing; some declared fields remain unwired |
| Life View | 84 | B | Personalized generated report, durable report record, clear transition into Core | Static report does not fully reconcile later changes or visibly distinguish current facts from historical generation-time facts |
| Life Compass | 79 | C+ | Real report actions, saved action statuses, payday context, goals, zone health | Missing governed three-zone completion, strongest urgent action routing, full milestone strip, and automatic response to updates |
| Life Graph | 84 | B | Durable central record, ten-node map, shared scoring, profile correction links | No historical arc, limited rationale/evidence detail, unlabeled relationships, incomplete household write model |
| Krovos Guide | 93 | A- design | Personalized Life Graph context, durable conversations, refresh/device restoration, archive flow, bounded model context, daily/monthly caps, actual token metering | Full authorization inventory and authenticated restoration/cap tests remain; no confirmed-change handoff into the Update Engine yet |
| Update Engine | 94 | A design | Low-burden check-in, durable snapshots, side-by-side review, exact income/expense/debt/savings application, change history, life-change inbox, honest unresolved routing, downstream refresh queue, and month-end gig-income reconciliation | Queue consumers, cross-system realignment, recovery/error UX, and controlled authenticated testing remain |
| Life Goals | 88 | B+ | Durable goals, contributions, completion states, derived-to-stored precedence | Connected member cannot update primary household goals; tool outcomes do not reliably feed goals; limited reminders and plan coordination |
| Life Calendar | 72 | C | Useful derived paydays, bills, goals, and life events | Estimated anchors can look authoritative; no durable custom events, source labels, correction, schedule/chore overlay, or notification workflow |
| Net Worth | 93 | A | Life Graph-derived assets/liabilities and durable snapshots | Needs correction/source traceability, household edge cases, import reconciliation, and authenticated restoration testing |
| Core money tools | 58 | F | Broad coverage and substantial calculation logic; many tools pre-fill Life Graph | Most completed outcomes are not account-backed, do not update goals/plans, and do not create follow-up work; known facts are inconsistently reused |
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
2. Connect Paycheck Planner, Emergency Fund, Debt Payoff Planner, Savings Goals, Open Enrollment Comparison, and Insurance Gap Checker first.
3. Let saved outcomes create or update a goal, decision, deadline, or Life Graph correction only after member confirmation.
4. Continue through the remaining Core tools and remove all session-only outcome language once persistence is real.

### Batch 3: Navigation over time

1. Complete the governed Life Compass three-zone design.
2. Add Life Graph history, evidence, relationship labels, and correction provenance.
3. Add durable calendar events, source labels, correction, reminders, and the household schedule/chore overlay.
4. Finish connected household goal and update permissions.

### Batch 4: Controlled verification

1. Run security tests before using any realistic test-user data.
2. Run founder accounts through onboarding, report, Compass, Guide, updates, goals, calendar, tools, Retirement, household connection, cancellation, and re-subscription.
3. Verify cross-device restoration, mobile, keyboard and screen-reader behavior, failure recovery, stale facts, duplicate submissions, and contradictory documents.
4. Re-score from observed behavior and resolve every score below 97 before beta.

## Product decision

Core should use the Retirement architecture as its default pattern: reuse the existing Life Graph and Life View, ask only for missing or changed facts, calculate without another model call where possible, save the outcome and evidence, create the relevant follow-up, and remain correctable. The audit does not recommend adding more surface area until the trust, memory, and persistence gaps above are closed.
