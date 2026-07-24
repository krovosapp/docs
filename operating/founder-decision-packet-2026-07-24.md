# Founder Decision Packet

**Date:** July 24, 2026
**Purpose:** Every decision across today's audits that genuinely needs Christine's approval before implementation, in one place, with a recommended default, the reason, and the practical consequence of each path. This is a synthesis, not a new audit. No code, migrations, settings, providers, or production systems were touched to produce it.

**Sources:** `operating/account-deletion-remediation-contract-2026-07-24.md`, `operating/household-sharing-partner-privacy-audit-2026-07-24.md`, `product/accessibility-responsive-readiness-audit-2026-07-24.md`, `product/product-discoverability-contextual-delivery-audit-2026-07-24.md`, `product/krovos-guide-beta-readiness-audit-2026-07-24.md`, `product/life-calendar-beta-readiness-audit-2026-07-24.md`, `operating/api-access-control-remediation-contract-2026-07-24.md`, `marketing/pre-launch-email-remediation-plan-2026-07-24.md`, and `marketing/soap-opera-sequence-decision-memo-2026-07-24.md`.

**Already resolved, excluded from this packet:** the API access-control contract's three headline items are confirmed shipped in the live codebase as of this writing: `lib/publicPaths.ts` now has an explicit `isPublicApiPath()` allowlist (the blanket `/api` public-path rule is gone), `app/api/life-gaps/prefill/route.ts` now requires a session and uses only the caller's own email, and the `send-sequences` cron's secret check is fail-closed. No decision or further action is needed on any of these three.

---

## Urgent: Before Beta

### 1. Pause the Group B and Group C email entry points now
**Recommended default:** disable `scheduleSequence(..., 'C')` in `app/api/waitlist/route.ts` and `scheduleSequence(..., 'B', ...)` in `app/api/life-gaps/submit/route.ts`, or otherwise stop new subscribers from entering either sequence, until real copy exists.
**Reason:** every Group B and Group C template is a `TODO` placeholder, and the cron sends whatever renders with no gate. This is an active condition, not a backlog item, real subscribers today would receive literal `[TODO: ...]` emails.
**Consequence:** pausing costs nothing (Group A's Email 1 and the transactional emails are unaffected); not pausing risks a real subscriber receiving broken copy on the next scheduled send.
**Dependency:** blocks nothing else; this is the one item in this packet that should not wait for any other decision.

### 2. Approve the five Soap Opera governance conflicts as a bundle
**Recommended default:** approve the recommendations already written in `marketing/soap-opera-sequence-decision-memo-2026-07-24.md`: Day 1 timing (not Day 0), a narrow "I" voice exception for Email 1's personal founder story, composite/illustrative provenance for Maya and Derrick, Resend as the system of record over Kit, and the compliance fixes (fail-closed unsubscribe, a real postal address).
**Reason:** each recommendation there is already reasoned through in detail; re-litigating them here would duplicate that work rather than add to it.
**Consequence:** approving unblocks finishing Group A's first three emails safely; leaving them undecided keeps the entire sequence, including the already-approved Email 1, in a state where it can't be honestly called compliant.
**Dependency:** blocks Group A Emails 2-3 from being finalized, and blocks item 1's pause from ever being lifted.

### 3. Show household-sharing defaults to the primary at invite/accept time
**Recommended default:** keep `calendar_shared` and `life_labor_shared`/`life_labor_edit_access` defaulting to on when a connection is accepted (the current behavior), but add a summary shown to the primary at that moment, rather than leaving it discoverable only later in Settings.
**Reason:** real beta testers will have real partners; a silent opt-out default is a trust risk precisely during the window you most need trust, even though the underlying default itself is defensible for collaborative surfaces like a shared calendar and chores.
**Consequence:** approving means a small, additive UI change (a confirmation summary), not a data-model change. Rejecting it in favor of flipping the defaults to opt-in instead would be a larger change with less product upside, since it would also weaken the "shared household" experience for the common case where sharing was exactly what both partners wanted.
**Dependency:** should land before any beta household connection is actually used by two real people.

### 4. Confirm the one-directional calendar-sharing model is a deliberate beta-time choice, not an oversight
**Recommended default:** keep the current one-directional model (connected partner sees the primary's shared items, not the reverse) for beta; treat making it bidirectional as a fast-follow decision after beta, not something to change under time pressure right before real households start using it.
**Reason:** the current model is safe, tested, and working; changing RLS policies for a genuinely mutual calendar is real engineering work with real risk of a sharing bug, exactly the kind of change beta timing argues against.
**Consequence:** deciding to defer costs nothing now and avoids a rushed change; deciding to fix it now delays beta and adds risk for a product-experience improvement, not a safety fix.
**Dependency:** determines whether any calendar-recommendation work from the discoverability audit should be built toward a mutual or primary-owned model; deferring the code change doesn't block that work, since either model can absorb it later.

### 5. Approve the hybrid model for account deletion: hard-delete personal data, retain an anonymized gift-entitlement financial record
**Recommended default:** approve, pending a legal skim of the exact wording.
**Reason:** `gift_entitlements.recipient_user_id` currently uses `ON DELETE RESTRICT`, which permanently blocks deletion for anyone who has redeemed a gift. The proposed fix (null the recipient reference, keep the amount/duration/dates) mirrors a pattern already working correctly elsewhere in the same schema (`gift_codes.redeemed_user_id`) and preserves the accounting record without retaining personal data.
**Consequence:** approving unblocks building the account-deletion job at all for anyone who's ever redeemed a gift. Requiring pure hard-delete instead would mean either destroying the accounting record (likely not acceptable) or leaving the block in place (the status quo, where deletion is impossible for these members).
**Dependency:** blocks starting the account-deletion job's implementation entirely; this is the single most load-bearing decision in this packet.

### 6. Approve the minimal connected-partner handling when a primary's account is deleted
**Recommended default:** when a primary is deleted, automatically downgrade the connected partner's `subscription_status` (the same thing `household/revoke` already does correctly today); do not require a separate notice email in the first version.
**Reason:** reuses existing, proven logic instead of building new notification infrastructure before it's clear that's needed; keeps the first version of account deletion smaller and faster to ship safely.
**Consequence:** approving the minimal version ships sooner; requiring a notice email first adds scope to the highest-risk, most safety-critical part of this packet.
**Dependency:** blocks finalizing the account-deletion job's handling of `household_connections` (Phase 1 of that contract's deletion order).

### 7. Require explicit confirmation for the first cohort of real account deletions
**Recommended default:** yes, for at least the first several real deletions, require Christine's one-click confirmation before the deletion job actually runs; automate fully once it's proven correct in production.
**Reason:** this pipeline touches newly-built, previously-untested logic performing an irreversible action; a manual gate for the first handful of real cases is cheap insurance against an unanticipated edge case.
**Consequence:** approving means a small number of manual confirmations early on; skipping straight to automatic is faster but has a higher blast radius if something was missed.
**Dependency:** should be decided before the deletion job's first production run, not after.

---

## Can Wait Until After Beta

### 8. Privacy-policy wording carve-out for the anonymized gift-entitlement exception
**Recommended default:** add one sentence noting anonymized transaction records may be retained for accounting purposes, once decision 5 is finalized and counsel has reviewed the exact language.
**Consequence:** minor either way; not something a beta tester is likely to test directly.
**Dependency:** depends on decision 5 being made first.

### 9. Whether account deletion should also sweep matching Life Gaps/waitlist records by email
**Recommended default:** yes, as a courtesy, once the core deletion job exists.
**Consequence:** small additional scope either way.

### 10. Whether Stripe-held data needs its own retention disclosure, mirroring the existing Anthropic-specific language
**Recommended default:** yes, once someone confirms what Stripe actually retains.
**Consequence:** minor copy addition either way.

### 11. Making calendar sharing bidirectional (the actual code change deferred by decision 4)
**Recommended default:** revisit once real household usage during beta gives you actual signal on whether members want mutual visibility.
**Consequence:** real engineering work either way; better informed by beta feedback than guessed at now.

### 12. A small bundle of low-stakes discoverability decisions
**Recommended default:** approve all four as routine, five-minute calls, whenever convenient: keep in-product labels ("Today," "Ask Krovos") as canonical and update documentation to match rather than renaming shipped UI; keep `/life-profile/spending` as one dual-purpose route provided a quick check confirms its copy already branches correctly by context; add a one-time "you're now connected" pointer to Life Labor when a household connection is accepted; add a reachable entry point to `/update` outside the payday window (Settings is a reasonable place).
**Consequence:** each is small and additive; none blocks anything else in this packet.

---

## Safe to Build Now Without Another Decision

Ranked by impact. No founder decision gates any of these; they're ready for implementation whenever prioritized.

1. **Fix the Krovos Guide starter-question mismatch.** Starter questions surface immigration, caregiving, pension, and business signals that are never actually included in the system prompt sent to Claude, directly contradicting the Guide's own "never re-explain" principle. Highest-impact confirmed gap in this batch of audits.
2. **Fix the Daylight Lantern inline-style light-mode risk** on Life Compass, Net Worth, Life Goals, Life Graph, and Retirement, where hardcoded hex colors in inline styles have no theme-conditional logic anywhere in the page files.
3. **Add accessible labels to Life Compass and Life Goals' interactive surfaces** (both confirmed at zero `aria-label` attributes despite being high-traffic, highly interactive routes).
4. **Wire the existing `?focus=` Krovos-Guide-link pattern into Life Goals and Net Worth.** The mechanism is already proven on Life Compass; this is reuse, not invention.
5. **Add the missing daily-usage-limit indicator to Krovos Guide's UI.** The data is already fetched; it's simply never displayed, so a member can hit the daily cap with no warning.
6. **Add a labeled reconnect/re-authorize action for an expired calendar connection**, replacing the current disconnect-only option.
7. **Fix the Krovos Guide empty-state overpromise** ("I already know your situation" showing identically whether or not a Life Graph actually exists yet).
8. **Live-verify mobile rendering** on the routes flagged with zero responsive breakpoint classes (Life Compass, Life Graph, Net Worth, Life Labor, Life Goals).
9. **Live-verify Settings' three-connection-state panel is still intact**, a previously real, fixed bug worth re-confirming rather than assuming.
10. **Remediate the win-back email sequence's named content risks** (banned filler language, reason-based routing instead of an assumed cancellation reason, verified pricing claims) before any further live use.

---

**No item in this packet has been built, approved, or acted on.** Every decision above is presented for Christine's approval; every "safe to build now" item is ready for implementation but was not implemented by this document.
