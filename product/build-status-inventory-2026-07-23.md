# Krovos Build-Status Inventory

**Date:** July 23, 2026  
**Purpose:** Replace ambiguous historical backlog language with one current status taxonomy.

## Status types

1. **Built and live:** implemented, pushed, deployed, and verified at the level stated.
2. **Built, verification or release pending:** implementation exists, but a named deployment or test gate remains.
3. **Partially built:** a useful foundation exists, but the promised workflow is incomplete.
4. **Speced-not-built:** product behavior is sufficiently defined to recognize the feature, but implementation has not started.
5. **Scoped prerequisite:** investigation or architecture exists, but a prerequisite must be built before the feature.
6. **Confirmed backlog:** a validated need that still requires detailed scoping.
7. **Strategic decision:** a real product fork requiring a founder decision before implementation.
8. **Idea-stage:** captured for preservation, not approved or scoped.
9. **QA or release gate:** built work that still requires human, authenticated, accessibility, boundary, or beta verification.
10. **Business or legal:** non-product operating work.
11. **Deprecated compatibility route:** retained only to redirect old links.
12. **Blocked by external dependency:** cannot complete until a vendor, account, data set, or outside decision is available.

## Current speced-not-built Core products

| Product | Current boundary |
|---|---|
| Document Vault | Secure durable document repository with per-item private/shared control. Current Life Profile uploads are not a Vault. |
| Household Budget Framework | Reusable household operating budget across paydays and life phases. Current Budget & Spending and payday check-ins are foundations, not the complete framework. |
| Trusted Pro Network | Curated referral and handoff system for professional help. Referral categories exist in guide thinking, but the network product is not built. |

## Partially built or prerequisite-dependent

| Product | Current boundary |
|---|---|
| Financial Modes | Detection and display exist; the distinct full operating experience for every mode is not complete. |
| Caring for Two Directions | Data audit is complete. The two-direction snapshot and trend require durable, queryable child-future and eldercare history before release. |
| Life Updates | Exact-value payday and life-change review, history, reconciliation notices, gig month-end handling, and downstream refresh exist. Goal-specific realignment, recovery/error UX, and controlled authenticated testing remain. |
| Krovos Guide to Life Updates handoff | Guide conversations are durable, but a confirmed-change proposal should enter Life Updates for review rather than silently changing the Life Graph. |

## Confirmed backlog or future plan

- VerifyPass-based deeper student discount, after the defined revenue or subscriber trigger.
- Grief-aware occasion preferences and deceased-partner date suppression.
- Partner-access cancellation communication variant.
- Medicare Advantage versus Medigap comparison, if Core Retirement validation confirms the need.
- Public, bounded Investment Priority lead magnet, subject to launch testing.

## Strategic decisions

- Goal-Based Investing: remain guidance-only or eventually execute/manage investments.
- Estate documents: organize-and-refer only or draft documents.
- Trusted Pro Network: operating model, vetting, liability boundary, and launch scope.

## Idea-stage

- Swipe-based mutual-choice experiences.
- Productivity and habit tracking, including a pregnancy-week overlay.
- Second-brain and note-taking workspace.
- Registry builder.
- Party planner.
- Digital-envelope budgeting.
- Additional retirement drawdown modeling after checking the existing Withdrawal Planner.

## Resolved stale historical entries

The following must not remain labeled speced-not-built:

- First Apartment is built at `/first-apartment-calculator`.
- Student Loan Repayment is built at `/student-loan-repayment-planner`.
- Early-career first-job decisions are built at `/first-job-decisions`.
- Net Worth is built at `/net-worth`.
- Life Labor is built at `/life-labor`.
- Connected-user household access and the Unified Life Calendar are built; remaining enhancements must be named individually.

Historical documents may preserve older language as an audit trail, but this file governs current status until a newer dated inventory replaces it.
