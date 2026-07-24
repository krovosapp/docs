# Krovos Remaining Work Inventory

**Date:** July 24, 2026
**Purpose:** One reconciled view of everything genuinely still open, sourced against the canonical coordination record (`3_current_state.md`, last refreshed July 23, 2026) and `docs/product/build-status-inventory-2026-07-23.md`. Organized by the kind of work required, not by feature area, so pre-beta blockers are never mixed in with strategic maybes or pure documentation debt.

This file does not restate what is already built. For that, see the canonical coordination record and `build-status-inventory-2026-07-23.md`. It exists to answer one question: **what is actually left, and in which bucket does each item belong.**

---

## 1. Pre-beta release gates

Built work that cannot honestly be called release-ready until a specific human, authenticated, or live-environment check happens. None of these are missing features; all are missing verification.

- Founder walkthrough: Jordan Ellis full fresh 8-step Life Profile walkthrough, using cleared Supabase data so the generated Life View reflects only what the new flow produces.
- Founder walkthrough: Christine's own full walkthrough, after Jordan's is verified clean.
- Morgan Callahan and Alex Nguyen test walkthroughs, after Jordan and Christine.
- Retirement workspace: authenticated household-mode QA, mobile/accessibility QA, and beta-comprehension testing (code is complete and scored 98/A+; human comprehension is the only remaining gate).
- Life Compass: authenticated workflow QA (mobile, accessibility, boundary-date, Guide-handoff) beyond the completed design pass.
- Life View: authenticated factual-accuracy, legacy-report, and audit-log restoration QA.
- Life Graph: historical scoring, correction provenance, and authenticated household edge-case QA (currently 90/A-).
- Collaborative Life Goals: authenticated two-person permission, denial, concurrency, restoration, mobile, and accessibility QA.
- Core money tools (durable-result batch): authenticated restoration and calculation-boundary QA across the converted tools.
- Full route-by-route visual-density, responsive, accessibility, and restored/error-state QA across the app (tracked in `product/app-ui-and-input-audit-2026-07-22.md`), including the still-open "meaningful route-specific illustrations, diagrams, richer charts, and branded empty states" pre-beta visual-composition gate.
- Repository-wide ESLint is blocked by a pre-existing ~378-error/116-warning backlog unrelated to any single feature; it is documented as a known gap, not represented as passing, and is a standing pre-beta cleanup item.
- Stripe referral-code attribution: confirmed correct at the code level, not yet live-tested with a real test-mode checkout.
- CRON_SECRET rotation: completed and verified July 21, 2026 (superseded — CLAUDE.md's "PENDING" note on this is stale, see Section 5 below).
- Google Calendar provider authorization: live and verified. Microsoft OAuth authorization and an Apple founder-authorized real iCloud import test remain pending before the calendar's external-import feature is fully release-ready.
- Gift subscriptions: CPA confirmation of Stripe/QuickBooks accounting and tax treatment for paid-yet-unredeemed gift balances, before public sale. Legal review of the no-cash-value and refund wording is also still open.
- Jordan Ellis's exact guide ownership (Home Buying, Starting a Business, Caregiving) should get one more explicit confirmation pass so a future session isn't surprised again; Alex Nguyen and Morgan Callahan's guide ownership has not been independently re-audited beyond earlier synthetic entitlements.

## 2. Genuine speced-not-built features

Product behavior is defined well enough to recognize the feature, but no implementation exists yet.

- **Document Vault** — a dedicated, durable document repository with per-item private/shared control. Current Life Profile document uploads are not a Vault and should not be described as one. Blocked on the connected-user model being proven stable in production first.
- **Household Budget Framework** — a full shared household operating budget across paydays and life phases, with per-category visibility and edit-access controls. Current Budget & Spending and payday check-ins (Life Updates) are foundations, not this framework.
- **Trusted Pro Network** — a curated referral/handoff directory for licensed professional help (CPAs, estate attorneys, insurance advisors). Referenced across guide content as a future resource; the network product itself is not built. Monetization model undecided (see Section 3).
- **Connected-user financial edit access (write path)** — the `financial_edit_access` toggle exists in `household_sharing_settings` and is exposed in the invite/settings UI, but no endpoint lets a connected user actually write to the primary's `life_graph` row. Currently display-only.
- **Life Graph `goals[].scope` field** — the top-level `goals` JSONB column exists but nothing writes to it yet; no `scope` (individual/household) field exists on any goal entry; cross-record sync for household-scoped goals was never built.
- **Household Connections Phase 2 (widowhood/emergency access)** and **Phase 3 (subscriber merge)** — both explicitly deferred, no code exists, do not start without a dedicated design session.
- **Family Mode priority-tool sequencing** — Family Mode now correctly activates/deactivates (fixed July 23), but its configured priority-tool list is not rendered or sequenced. Members see four focus-area labels with no ordered workflow, direct tool actions, completion state, or next-step logic yet.
- **Krovos Guide → Life Updates handoff** — Guide conversations are durable, but a confirmed change surfaced in conversation should route into Life Updates for review rather than silently changing the Life Graph; this handoff isn't built.
- **Calendar reminder delivery** — the Unified Life Calendar's import, recurrence, and external work-feed layers are built and live; reminder/notification delivery for calendar items remains open, separate work.

## 3. Deferred strategic decisions

Real product forks that need a founder decision before any implementation should start.

- **Goal-Based Investing**: remain guidance-only, or eventually execute/manage investments directly.
- **Estate documents**: organize-and-refer only, or actually draft documents.
- **Trusted Pro Network**: operating model, vetting process, liability boundary, and launch scope (referral fee vs. curated listing vs. flat fee).
- **VerifyPass-based steeper student/military/etc. discount tier**: paired with a lower AI-usage quota; wait for a defined revenue or subscriber trigger before building the verification integration.
- **Occasion-based messaging suppression** (Mother's/Father's Day, grief-aware preferences) and **deceased-partner date suppression on the Life Calendar**: both need a standing preference-center design; blocked in part on the not-yet-built widowhood/emergency-access model (Phase 2 above).
- **Light-mode logo/hero variant**: deferred pending outside feedback on whether the current dark-optimized icon and hero read correctly in Daylight Lantern (the light theme itself has since shipped; this is specifically about the icon/hero art).
- **AI "heads of department" concept** (specialized agents per domain instead of one general Krovos Guide): not scoped; needs one concrete recurring task identified where a specialized agent would clearly outperform the current single-agent model before any scoping begins.
- **Medicare Advantage vs. Medigap comparison tool**: build only if Core Retirement validation confirms real member need.
- **Public, bounded Investment Priority lead magnet**: subject to launch testing before a decision.
- Christine's running product-ideas backlog (captured July 19, 2026, not yet scoped): reusable swipe/card decision mechanic across guide areas, a broader productivity/habit-tracking tool set, a "second brain" notetaking direction, a New Parent registry-builder tool, a kids'-party planner, a digital-envelope weekly budgeting companion, and — flagged by Christine as the largest single idea in this list — the fully-integrated, multi-provider Unified Life Calendar vision beyond what's already shipped (one-time import + read-only work feed is built; deeper cross-tool integration ideas from the Perplexity research sweep remain unscoped).

## 4. Business, legal, and launch work

Non-product operating work that has to happen regardless of what ships in the app.

- Trademark clearance call with Luedeka Neely (865-546-4305) — pending.
- Blount County business license — pending.
- Solo 401k — pending, deadline December 31, 2026.
- CPA confirmation of gift-subscription revenue recognition and tax treatment before public sale (also listed in Section 1 as a release gate for that specific feature).
- Legal review of gift-subscription no-cash-value and refund language.
- Verify Stripe referral attribution in test mode before opening Founding Member checkout publicly.
- Google Cloud OAuth app for Calendar remains in Testing status; publishing/verification with Google is an open decision, not yet started.
- Microsoft OAuth app registration for Outlook Calendar integration — not yet done.
- Apple iCloud CalDAV import — needs a founder-authorized real test with an app-specific password (and Christine should revoke/regenerate the password that was displayed during earlier UI testing, per the standing instruction from that session).

## 5. Documentation-only cleanup

Nothing here requires a product or business decision — it's stale text correction, and most of it is now done as part of this pass.

**Completed in this pass (see commit referenced in the session summary):**
- Renamed stale product terminology (Smart Start → Life Profile, Life Understanding Report → Life View, Navigation Scoreboard → Life Compass, "Trusted Guide Layer" → Krovos Guide) throughout `governing/`, `operating/`, `strategic/`, `evidence/`, `conviction/`, and `index.mdx`, while preserving the original wording as marked historical context rather than deleting it.
- Corrected the Life Phase Guides count in `strategic/product-overview.mdx` from a stale "16" to the current 17.
- Corrected `strategic/product-roadmap.md`'s stale "Navigation Scoreboard enhancements" backlog line.
- Flagged (but did not silently rewrite) `governing/blueprint.mdx` and `operating/product-os.mdx` as early founding-era documents whose "Built & Live" / "Roadmap" sections predate most of 2026; added superseding notes pointing to this inventory and to `build-status-inventory-2026-07-23.md` instead of overwriting their historical roadmap lists.

**Still open, out of this pass's scope (CLAUDE.md itself, in the `krovos` repo, not the `docs` repo this pass was scoped to):**
- `CLAUDE.md`'s "Future / Unscoped / Deferred" entry for **gift subscriptions** ("not built... no gift flow exists anywhere in the codebase or Stripe configuration") is stale. Gift subscriptions are fully built and live in production (see the canonical record's Section 11, "Krovos gift subscriptions and migration-claim stacking — fully shipped and verified July 23, 2026"). This needs a direct edit in `CLAUDE.md`, which lives in the `krovos` repo — out of scope for this docs-only pass. Recommend updating it in the next `krovos`-repo session.
- `CLAUDE.md`'s Product Decisions Log entries for **Life Graph page** ("Status: Not yet built...") and **Life Compass rebuild** ("Status: Not yet built to this spec...") are stale against CLAUDE.md's own Pending Issues section, which correctly marks both as built. Same out-of-scope note applies.
- `CLAUDE.md`'s Milestone and Motivation Layer entry ("Status: Plan for post-launch, design for now") is stale: a first Core implementation, "Progress and wins," shipped July 23, 2026 (canonical record Section 4). Same out-of-scope note applies.
- `CLAUDE.md`'s pending-issues list overall has not been reconciled against the canonical record in some time; a dedicated `krovos`-repo session should diff CLAUDE.md's backlog against `3_current_state.md` line by line rather than this docs-repo pass attempting it secondhand.

---

## How this inventory was built

Sourced directly from `/Users/christine.smith/mcp/csuite/context/3_current_state.md` (read in full, all ~675 lines, dated through July 24, 2026) and `docs/product/build-status-inventory-2026-07-23.md`. Nothing here is claimed as independently re-verified live by this session; where the canonical record already states something was live-verified, that verification is cited, not repeated as a new claim.
