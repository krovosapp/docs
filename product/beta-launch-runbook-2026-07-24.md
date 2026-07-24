# Beta Launch Runbook

**Date:** July 24, 2026
**Purpose:** One founder-operable sequence for getting from today to a safe beta invitation, built from the canonical current-state record, `product/beta-readiness-test-plan-2026-07-24.md`, `operating/founder-decision-packet-2026-07-24.md`, and every July 24 audit. This is operational, not a new audit, follow it in order. No app code, migrations, services, emails, Stripe, Supabase, or Vercel were touched to produce it.

**How to read the tags:** `[VERIFIED]` means confirmed by direct code inspection today. `[NEEDS LIVE CHECK]` means the code supports it but no one has confirmed it works in a real browser/account yet. `[DECISION PENDING]` means Christine has not yet approved a specific choice; a default recommendation is in the founder decision packet.

**Reconciliation note, added after this runbook was first written:** five app commits landed on `main` after this runbook's original pass (`f2d831d`, `89a635e`, `06e7804`, `c27d7c8`, `81c678c`, `965bf77`, six commits total). Each is verified against the actual diff below, in place, rather than restated as a separate document. Three items that were "safe to build now" backlog entries are now shipped; one ("Daylight Lantern light-mode risk") is now a partial fix, not a complete one, with two of the five originally-flagged routes still fully untouched.

---

## 1. Decisions Christine Must Make Before Beta

Full detail and reasoning in `operating/founder-decision-packet-2026-07-24.md`. Check off only once actually decided, not once read.

- [ ] Approve the five Soap Opera governance conflicts as a bundle (Day 1 timing, "I" voice exception, Maya/Derrick provenance, Resend over Kit, compliance fixes). *Blocks re-enabling any marketing sequence.*
- [ ] Show household-sharing defaults (calendar + Life Labor shared, financial not) to the primary at invite/accept time. *Small UI addition; do before any beta household connection is used by two real people.*
- [ ] Confirm one-directional calendar sharing is a deliberate beta-time choice, not an oversight. *No code change required to confirm this, just a decision to not touch it yet.*
- [ ] Approve the hybrid account-deletion model (hard-delete personal data, anonymize the gift-entitlement financial record). *The single most load-bearing decision blocking account-deletion work from starting at all.*
- [ ] Approve minimal connected-partner handling on primary deletion (auto-downgrade status, no notice email required in v1).
- [ ] Require manual confirmation for the first cohort of real account deletions, then automate.

**Can wait until after beta** (do not let these block launch): the privacy-policy carve-out wording, whether deletion sweeps Life Gaps/waitlist by email, a Stripe-specific retention disclosure, and the bundle of four low-stakes discoverability naming/route decisions. Full list in the founder decision packet, Section "Can Wait Until After Beta."

---

## 2. Safe-to-Build-Now Items (Already Approved, No Decision Needed)

Ranked by impact in the founder decision packet. Restated here as a build checklist:

- [x] `[SHIPPED, commit 89a635e]` Krovos Guide starter-question-to-system-prompt mismatch: **substantially fixed, not fully closed.** `app/api/krovos-guide/route.ts` now builds a "RELEVANT LIFE CONTEXT" block covering business ownership, FIRE planning, wedding planning, immigration/work-authorization, aging-parent caregiving, college planning, pension planning, variable/RSU household income, and self-employed/gig income, and includes it in the system prompt sent to Claude. This closes the headline example (immigration/visa) and most of the originally-named signals. **Residual gap, not yet covered by this commit:** `remittance`, an `expecting` child, `life_insurance`/estate context, and `three_phase_retirement`, all of which still drive a starter question in `lib/krovos-guide-starters.ts` but are not yet mirrored into this new context block. Low priority relative to what shipped; note for a future pass, not a beta blocker.
- [ ] Fix the Daylight Lantern inline-style light-mode risk on Life Compass, Net Worth, Life Goals, Life Graph, Retirement. **Partially shipped, commit `06e7804`, see the corrected Light Mode checkpoint in Section 5**, two of the five originally-flagged routes (Life Graph, Retirement) are confirmed still fully untouched.
- [ ] Add accessible labels to Life Compass and Life Goals (both confirmed at zero `aria-label` attributes). **Not addressed by any of the five reconciled commits; still open.**
- [x] `[SHIPPED, commits 81c678c and 965bf77]` Wire the existing `?focus=` Krovos-Guide-link pattern into Life Goals and Net Worth. Both now show a link into a pre-loaded Krovos Guide conversation ("Talk through my next goal step" on Life Goals, "Put this in context" on Net Worth, correctly hidden when Net Worth has no data yet). Simpler than the fuller per-goal-type recommendation the discoverability audit envisioned, but functionally correct and shipped.
- [x] `[SHIPPED, commit f2d831d]` Add the missing daily-usage-limit indicator to Krovos Guide's UI. `app/krovos-guide/page.tsx` now tracks and displays `dailyTurns`/`dailyLimit` alongside the existing monthly figure. Confirmed correct from the diff; a trivial live glance is still worthwhile, but this is not a meaningful-risk item anymore.
- [ ] Add a labeled reconnect/re-authorize action for an expired calendar connection. **Not addressed; still open.**
- [ ] Fix the Krovos Guide empty-state overpromise for a member with no Life Graph yet. **Not addressed; still open.**
- [ ] Remediate the win-back sequence's named content risks (banned language, reason-based routing, verified pricing) before any further live use. **Not addressed; still open.**

Three of the original eight items are now shipped, confirmed against the actual diffs, not just the commit messages. The remaining five (Daylight Lantern's two untouched routes, both accessible-label gaps, the reconnect action, the empty-state overpromise, and win-back remediation) should still be prioritized before Section 3's founder walkthrough, since that walkthrough is exactly where they'd surface.

---

## 3. Founder Walkthroughs Before Inviting Testers

Full sequence and rationale in `product/beta-readiness-test-plan-2026-07-24.md`, Section 6. Condensed to a single pass here; use disposable or designated test accounts throughout, never real payment methods.

- [ ] Fresh signup, no seeded data: complete the 8-step Life Profile flow end to end; confirm document upload/pre-fill on every applicable step; confirm generation runs Review → loading → Life View with no `/confirm` page.
- [ ] Read the generated Life View: every figure traces to entered/extracted data, no em dashes, "What To Do First" reads as Krovos-owned action, not homework.
- [ ] Life Compass/Today: confirm the first-visit greeting, the empty "Progress and wins" state, and that exactly one current-focus card shows (not zero, not multiple).
- [ ] Life Graph: open the map, tap 2-3 nodes, read the evidence panel, confirm keyboard navigation. `[NEEDS LIVE CHECK]` per the accessibility audit, SVG node keyboard accessibility was not confirmable from code alone.
- [ ] Life Goals: create, log a contribution, mark one complete and one abandoned; confirm no destructive delete option exists (status-only).
- [ ] Life Calendar: add a personal event, confirm a recurring Life Labor occurrence renders; if Google is connected, run a real import.
- [ ] Retirement: run the full guided workspace solo, save the model, confirm Next decisions/Decision tools/Saved plan unlock only after saving.
- [ ] Core money tools: spot-check Emergency Fund, Debt Payoff Planner, Credit Card Optimizer, Purchase Planner for blank-state correctness (no fabricated starting numbers).
- [ ] Krovos Guide: open a fresh conversation from a sparse account, confirm relevant starters; deliberately trigger a failed send, confirm the retry state (never renders as a received reply).
- [ ] Household connection, second account: invite, accept, confirm shared Life Compass banner, Retirement "Me + partner" mode, Life Labor sharing/edit-access gating, shared calendar layer, collaborative goal editing, each toggle tested independently.
- [ ] Guide purchase path: as a zero-guide account, confirm every guide-exclusive tool renders locked with an in-place explanation, never a direct-to-checkout redirect; grant one guide, confirm its tools unlock including one OR-logic shared tool.
- [ ] Settings: walk all three connection states (unconnected/primary/connected), confirm the correct panel each time. `[NEEDS LIVE CHECK]`, this is a previously real, fixed bug, worth re-confirming, not assuming.
- [ ] Toggle theme light/dark and click through 8-10 core routes for Daylight Lantern legibility. `[NEEDS LIVE CHECK]`, prioritize Life Graph and Retirement specifically, both confirmed still fully untouched by the partial fix in commit `06e7804` (see the corrected Light Mode checkpoint in Section 5).
- [ ] Mobile pass: resize to phone width, re-walk steps 1, 3, 7, and 10 above.
- [ ] Payment surfaces, **test mode only**: Founding Member checkout with a referral code, discount-attestation checkout, gift purchase and redemption, `/cancel` flow through to pause and to full cancellation.
- [ ] Feedback and email: submit one in-app feedback item, confirm the Resend alert arrives; confirm one transactional email reaches `delivered`, not just `queued`.

**Only after every item above passes:** proceed to Section 4.

---

## 4. Controlled Beta Invitation Process

**Who to invite first:** one or two people Christine already knows well enough to get honest, detailed feedback, not a broad batch. Use the existing `/beta?code=XXXX` flow (a durable `beta_tester` account, independent of the invite code itself). Recommend the first invite be a household pair (two people who will actually accept a household connection with each other), since that is the highest-complexity, least-tested real-world path (Section 3, household connection step) and best done with people who will report problems constructively rather than churn silently.

**What to observe, per invite:**
- Whether the app-inventory and guide-selection prompts surface at the correct, already-designed timing (after onboarding and some real Core usage, never immediately at signup).
- Whether the person completes onboarding without asking Christine a clarifying question that the product should have answered itself, each one is a real UX gap, not noise.
- Whether Daylight Lantern light mode is used at all, and whether it's legible, especially on Life Graph and Retirement specifically, per the corrected Section 5 checkpoint.
- Whether the person discovers Krovos Guide, Life Goals, and Retirement on their own, or needs to be told where to find them. Life Updates is now a named item in the Explore menu (commit `c27d7c8`, confirmed shipped), so this specific discoverability gap from the original audit is closed; watch instead for whether testers notice and use the new Life Goals/Net Worth "talk this through" links from commits `81c678c`/`965bf77`.

**Where feedback lands:** the in-app feedback button (every authenticated page, categories Bug/Design Feedback/General Thoughts/Feature Idea, screenshots supported) and the separate `/beta-feedback` structured survey. Both route to founder@krovos.app via Resend. Check both, they are deliberately separate systems.

**Stop rules, pause new invites immediately if:**
- Any beta tester reports receiving an email with `TODO` or placeholder-looking content (confirms the marketing-sequence pause in Section 6 failed or was reverted).
- Any beta tester reports seeing another person's data, in any surface (Guide, calendar, goals, financial figures).
- Any beta tester's account deletion or cancellation request cannot be honored the way the privacy page promises (expected today, since account deletion isn't built yet, see Section 6; if this comes up, handle it manually and do not promise a timeline until Section 1's decisions are made).
- More than one light-mode illegibility report comes in before Section 5's light-mode checkpoint is separately verified.

---

## 5. Exact Verification Checkpoints

Each checkpoint states current status precisely, do not treat a "confirmed built" item as "confirmed working in production" without the live check.

**Subscriptions**
- [ ] `[VERIFIED]` No route bypasses subscription enforcement: `authenticateSubscribedRequest()` now gates `generate-report`, `krovos-guide`, `update/apply`, `extract-documents*`, `gifts/redeem`, `household/goals`, `life-labor/occurrences`, `calendar/connections`, `calendar/import/apple`, `stripe/guide-checkout`, confirmed live in the codebase as of this runbook.
- [ ] `[NEEDS LIVE CHECK]` A test-mode Founding Member checkout with a referral code writes `referral_code` metadata correctly to `profiles`.

**Gifts**
- [ ] `[VERIFIED]` Gift checkout rejects discount/coupon parameters server-side; no money moves until Stripe's Checkout Session completes; personal-import-style token handling is not applicable here, gift purchase is unauthenticated by design.
- [ ] `[NEEDS LIVE CHECK]` A test-mode gift purchase and redemption completes for at least one 3-month and one 12-month duration, and redemption correctly handles a recipient with no subscription, an active subscription, and a scheduled-cancellation subscription.
- [ ] `[DECISION PENDING]` What happens to a redeemed gift entitlement on account deletion is not yet resolved (Section 1).

**Cancellation**
- [ ] `[VERIFIED]` `/cancel` correctly sets `cancel_at_period_end`, and Stripe's own `customer.subscription.deleted` webhook correctly starts the 30-day `cancelled_grace_period` window.
- [ ] `[NEEDS LIVE CHECK]` The full `/cancel` flow (interstitial, survey, save offer, pause, confirm) in test mode, and that the resulting Stripe state matches the member's choice.

**Guide limits**
- [ ] `[VERIFIED]` Monthly and daily turn limits, plus a separate monthly cost cap, all enforced server-side.
- [x] `[SHIPPED, commit f2d831d]` The daily limit is now fetched and displayed in the UI alongside the monthly figure; the "member can hit it with no warning" gap is closed.
- [ ] `[NEEDS LIVE CHECK]` Deliberately exhaust the daily limit on a test account and confirm the displayed count updates correctly and the error message a real member would see is accurate.

**Calendar sharing**
- [ ] `[VERIFIED]` `work` and `guide_linked` calendar layers are RLS-hard-blocked from ever reaching a connected partner, under any toggle combination.
- [ ] `[VERIFIED]` Personal and household calendar sharing is one-directional (connected party reads/writes the primary's data, never the reverse); this is a confirmed, deliberate-for-now state, not a bug to fix before beta (Section 1).
- [ ] `[NEEDS LIVE CHECK]` A real two-account household connection walk of the shared calendar, per Section 3.

**Light mode**
- [x] `[PARTIALLY SHIPPED, commit 06e7804]` Life Compass, Net Worth, and Life Goals each got a `krovos-inline-daylight` class on their root element, paired with new `[data-theme="light"]` CSS in `app/globals.css` that targets specific inline-style value patterns (`background: '#1B2230'`, `color: '#E9EEF2'`, `color: 'rgba(127,166,187...'`, and two border-color patterns) via attribute selectors with `!important`. This is a real, working technique for overriding React inline styles, contradicting this runbook's earlier assumption that no CSS mechanism could reach them; the defined CSS variables (`--light-surface-raised`, `--midnight-slate`, `--mist-on-light`, `--light-border`) all resolve correctly. **It is a targeted patch covering six specific literal-value patterns, not every color literal in these three files**, so `[NEEDS LIVE CHECK]` remains appropriate for confirming full-page legibility, not just the patterns explicitly remapped.
- [ ] `[CONFIRMED CODE-LEVEL RISK, unchanged]` **Life Graph and Retirement received no changes in commit `06e7804` or anywhere else reconciled here.** Neither file references the new `krovos-inline-daylight` class. Both remain exactly as originally flagged: inline `style={{}}` color literals with zero theme-conditional logic. These two routes, not all five, are now this runbook's highest-priority live-verification item.
- [ ] `[NEEDS LIVE CHECK]` Load Life Compass, Net Worth, and Life Goals in Daylight Lantern mode and confirm full-page legibility, not only the six remapped patterns; separately and with higher priority, load Life Graph and Retirement and expect them to still show the original risk until they receive the same treatment.

**Tools**
- [ ] `[VERIFIED]` The locked-tool dialog uses correct `role="dialog" aria-modal aria-labelledby` semantics; no tool computes a result from blank/default inputs (extensive input-safety correction pass already shipped).
- [ ] `[NEEDS LIVE CHECK]` Keyboard focus trapping and Escape-to-close inside the locked-tool dialog specifically; ARIA correctness doesn't guarantee focus management is right.

**Privacy**
- [ ] `[VERIFIED]` Krovos Guide context is fully isolated per user; a connected partner's data never reaches Anthropic under any sharing configuration.
- [ ] `[VERIFIED]` No route trusts a client-supplied identity for any cross-account authorization decision.
- [ ] `[CONFIRMED GAP]` No automated account-deletion process exists; no code ever deletes a Storage object; six Life Labor tables plus `household_connections` have no cascade from `auth.users`, meaning a deletion attempt would fail outright for most real accounts today. **Do not promise a specific deletion timeline to any beta tester until Section 1's account-deletion decisions are made and built.**

---

## 6. What Must Remain Paused or Disabled at Beta Launch

- [ ] `[VERIFIED PAUSED]` **Marketing sequences (Groups A, B, and C).** `MARKETING_SEQUENCES_ENABLED = false` in `lib/email-sequences.ts`, confirmed live on `main` (commit `1727f49`), enforced both at scheduling and at send time. This covers Group A (including the previously-live Email 1) as well as B and C. Transactional email and Group W win-back are unaffected. **Do not re-enable until:** every scheduled template has real approved copy, Section 1's five governance conflicts are decided, the compliance fixes (fail-closed unsubscribe, a real postal address) are in place, and a written measurement contract exists.
- [ ] **Win-back sequence (Group W)** should not be relied on for any real cancellation during beta until its named content risks (Section 2) are remediated; it is technically live and untouched by the pause above.
- [ ] **Account deletion**, do not advertise or promise a specific deletion outcome to any beta tester; the process does not exist yet (Section 5, Privacy).
- [ ] **Anything requiring real payment**, only ever exercise checkout, gifts, and cancellation in Stripe test mode during this phase, per Section 3.
- [ ] **Public/wide beta invites**, do not expand beyond the first one or two invites (Section 4) until Section 7's first full week of monitoring shows no stop-rule trigger.

---

## 7. Daily and Weekly Beta-Monitoring Checklist

**Daily**
- [ ] Check the in-app feedback inbox and `/beta-feedback` submissions.
- [ ] Check Resend for any transactional email stuck at `queued`/`bounced` beyond ordinary latency.
- [ ] Confirm `MARKETING_SEQUENCES_ENABLED` is still `false` (a stray revert or a manual override would be the single worst-case regression this runbook guards against).
- [ ] Skim for any Krovos Guide daily-limit complaints; the display fix is shipped (commit `f2d831d`), so a complaint now likely signals a real display or counting bug, not the previously-known missing-indicator gap.

**Weekly**
- [ ] Review every open beta feedback item against the "stop rules" in Section 4, escalate anything matching them immediately rather than waiting for the weekly review.
- [ ] Re-run the light-mode check (Section 5) on any route that shipped a code change that week.
- [ ] Confirm the household-connection walkthrough (Section 3) still passes if any sharing-related code shipped that week.
- [ ] Review Section 1's decision list, anything still pending should have a reason it's still pending, not silence.

---

## 8. Exit Criteria

**Expand beta (invite more testers):**
- A full week of monitoring with no stop-rule trigger.
- Section 5's light-mode and calendar-sharing live checks both pass.
- At least one full household-connection walkthrough completed by real beta testers, not only the founder walkthrough.

**Pause beta (stop new invites, do not necessarily roll back existing testers):**
- Any stop rule in Section 4 triggers.
- A `TODO`/placeholder email reaches a real subscriber (should be structurally impossible per Section 6, but treat any occurrence as an immediate pause trigger and an investigation into how the pause was bypassed).
- Any confirmed cross-account data exposure, of any kind.

**Move toward public launch:**
- Every item in Section 1 is decided and, where it required a build, shipped.
- The account-deletion process exists and has passed its own live-verification plan (`operating/account-deletion-remediation-contract-2026-07-24.md`, Section 8).
- Marketing sequences are either re-enabled under Section 6's stated conditions, or a deliberate decision has been made to launch without them.
- No open item remains in this runbook's Section 5 marked `[NEEDS LIVE CHECK]` without having actually been checked.

---

**No item in this runbook has been executed. Every checkbox reflects a step to take, not a step already taken, except where explicitly marked `[VERIFIED]` against code inspected on July 24, 2026.**
