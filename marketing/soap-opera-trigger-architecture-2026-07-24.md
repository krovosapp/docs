# Krovos Soap Opera Trigger Architecture

**Date:** July 24, 2026
**Purpose:** A trigger map for Krovos's lifecycle and audience-acquisition email strategy, extending the existing Group A sequence with humane, signal-driven branches instead of a generic funnel. This document does not draft final email copy, per instruction, except where an existing approved sequence already supplies enough material to summarize accurately. Every new branch below is marked copy needed.

**Sources:** `CLAUDE.md`'s Email Architecture section (krovos repo, read only, not edited), `content/emails/README.md` and the individual Group A email files (`krovos-os` repo, read only), `content/marketing/soap-opera-sequence-audit-2026-07-20.md` (`krovos-os` repo, read only), `strategic/audience-acquisition-and-profit-system-2026-07-20.md`, `strategic/revscan-evaluation-2026-07-20.md`, `product/life-gaps-original-build-spec.md`, `product/tool-delivery-map-2026-07-24.md`, `product/guide-personalization-contract-2026-07-24.md`, and `/Users/christine.smith/mcp/csuite/context/3_current_state.md` (canonical current-state record, refreshed July 23, 2026). No message is sent, drafted for sending, or scheduled by this document.

---

## 1. Existing Confirmed Sequences and Materials

### Confirmed live architecture (per `CLAUDE.md` and the canonical record)

- **Transactional (Category 1):** T1 Waitlist Confirmation ("You are on the list."), sent to any subscriber who joins the waitlist. T2 Life Gaps Results Delivery, sent when a member submits their email at the Life Gaps gate, dynamically rendering their actual gap content.
- **Soap Opera Sequences (Category 2):** scheduled via `lib/email-sequences.ts`, executed by a daily cron at 9:00 UTC, stored in the `email_schedule` table.
  - **Group A** (completed Life Gaps and joined waitlist): Day 0 sends T2 then T1; Day 1 onward runs a 7-email sequence on days 1, 3, 5, 8, 12, 16, 20.
  - **Group B** (completed Life Gaps, no waitlist): Day 0 sends T2 only; Day 1 onward runs a 5-email sequence on days 1, 3, 6, 9, 13, designed to convert into the waitlist.
  - **Group C** (joined waitlist, no Life Gaps): Day 0 sends T1 only; Day 1 onward runs a 4-email sequence on days 1, 3, 5, 8, designed to drive completion of Life Gaps. If a Group C subscriber completes Life Gaps before their sequence ends, remaining Group C messages stop and they enter Group A or B from Day 1 of that sequence.
- **Launch and post-launch (Category 3):** a manually triggered Email 8 launch announcement, sent to all groups at once at launch. A post-launch Seinfeld track (independent daily broadcasts) is planned, not built.
- **Retired:** the original Path A (`lib/waitlist-emails.ts`), Path B (inline templates in the Life Gaps submit route), and Path C (`path-c-emails` cron) systems were fully replaced by the Group A/B/C architecture above. Their crons were removed. Do not reintroduce them.
- **Win-back sequence:** four implemented templates, sent on days 14, 19, 24, and 28 after a subscription-deletion event, tracked in `email_schedule` as Group W. Per the `krovos-os` audit, these are implementation evidence, not governed, approved copy, and the audit names specific pre-launch fixes still required (banned filler language, reason-based routing instead of an assumed low-use reason, verifying current pricing/discount claims, verifying the 30-day data-retention statement against the actual deletion process).
- **Beta welcome email:** a post-signup welcome email was added to the beta flow via Resend, after a real gap was found where the prior flow auto-signed-in and redirected with no explanation of durable account status.
- **Cancellation confirmation:** sent on both in-app and Stripe-originated cancellation paths, subject "Your cancellation is confirmed," with delivery-ledger-based duplicate suppression.
- **Birthday recognition:** a dated cron (`birthday-check`) sends a birthday email once per year per profile, tracked via `birthday_email_sent_year`.

### Group A story-content state (per `krovos-os/content/emails/README.md` and the July 20, 2026 audit)

| Email | Story | Copy state | Application state |
|---|---|---|---|
| 1 | Founder oil-light story | Approved | Implemented (`GroupA01Founder.tsx`) |
| 2 | Maya | File says approved | Placeholder/TODO in the dispatcher |
| 3 | Derrick | File says approved | Placeholder/TODO in the dispatcher |
| 4 | James and Sarah | Founder source supplied July 20, 2026; durable repository copy not yet preserved | Placeholder schedule key |
| 5 | Marcus and Jade | Founder source supplied July 20, 2026; durable repository copy not yet preserved | Placeholder schedule key |
| 6 | Elena | Founder source supplied July 20, 2026; durable repository copy not yet preserved | Placeholder schedule key |
| 7 | Founder creates Krovos | Draft prepared for founder review, not approved | Placeholder schedule key |

Approved subjects that already exist for Emails 1 through 3: "I still think about that dashboard light." (Email 1), "She thought the awkward part was finally over." (Email 2), and "...You're serious?" (Email 3). A subject/preheader recommendation set exists for all seven emails, including recommended (not yet approved) subjects for Emails 4 through 7.

### What remains unknown or unresolved

The July 20, 2026 audit records five specific unresolved conflicts that this document does not attempt to resolve, since resolving them requires a founder decision, not a docs-repo task:

1. **Day 0 vs. Day 1 timing conflict.** The Email 1 file says Day 0, immediate on signup. The current architecture reserves Day 0 for T2/T1 transactional sends and starts the Soap Opera sequence on Day 1. Emails 2 and 3 also treat Email 1 as happening on Day 1. This document assumes the Day 1 architecture is current and correct, consistent with `CLAUDE.md`'s stated schedule, but the underlying file-level inconsistency has not been reconciled.
2. **Voice-governance conflict.** The approved founder story is told in "I" voice; the full Attractive Character specifies "we" voice for the origin story. Not resolved.
3. **Story provenance.** Whether Maya and Derrick are real, consented stories, documented anonymized cases, or composites is not recorded anywhere this document has access to. The Storytelling Bible and AC both require this to be known before further use.
4. **Provider ownership.** `CLAUDE.md` names Kit as the marketing-email platform and Resend as transactional, but the live sequence code sends scheduled marketing messages through Resend. No migration or resolution is recorded.
5. **Compliance gaps.** Email 1's note incorrectly assumes no unsubscribe is required for a welcome email; the shared template only renders an unsubscribe link when a subscriber record resolves successfully, which the audit flags as a fail-open risk rather than a fail-closed one; and the shared footer does not confirm a valid physical postal address, which CAN-SPAM requires.

Also unknown: whether any Group B or Group C story content exists anywhere, beyond the placeholder schedule keys; whether gift-subscription delivery has any lifecycle follow-up beyond the one recipient email already confirmed in the canonical record; and whether any social-to-email capture mechanism beyond the waitlist/Life Gaps forms exists (see Section 5).

**Because Email 1's copy still contains the banned filler word "genuinely" per the audit, and because the founder-voice/provenance/provider questions above are unresolved, this document treats none of the existing Group A copy as safe to reuse verbatim in any new branch below without Christine's review first.**

---

## 2. Trigger Map

Universal suppression rules that apply to every trigger below, restated from the July 20, 2026 audit and `CLAUDE.md`'s Email Architecture, rather than repeated in each row:

- A subscriber never runs two active sequences whose goals conflict (Group A and Group C must never run simultaneously; completing Life Gaps mid-Group-C stops Group C and enters Group A/B).
- A purchase, beta activation, unsubscribe, hard bounce, spam complaint, or manual suppression stops any sequence whose goal is already satisfied or whose consent no longer applies.
- Sensitive Life Gaps details never appear in subject lines or general campaign logs.
- One suppression source governs every provider in use, so a stop in one system is a stop everywhere.
- Transactional messages stay functionally separate from marketing consent and reporting.

### 2a. Life Gaps result

- **Who receives it:** anyone who completes the Life Gaps quiz and submits an email.
- **Suppression rules:** none beyond the universal rules; this is the entry event, not a suppression target, except that a subscriber already in `life_gaps_submissions` with `email_path = waitlist` gets a silent success response rather than a duplicate send, per the existing deduplication order.
- **Timing:** immediate (T2, Day 0), then Group A or B begins Day 1 depending on prior waitlist status.
- **Emotional / job-to-be-done:** the member just saw their own specific gaps named back to them; the job is to feel recognized, not sold to, in this first message.
- **Sequence purpose:** confirm the recognition moment landed, then carry it into either the full Group A story arc (if already on the waitlist) or the Group B arc (if not).
- **CTA destination in Krovos:** the results view itself first; the secondary CTA is the waitlist join, not a direct subscription pitch.
- **What must not be assumed or overpromised:** never assume every gap flagged in the quiz is equally urgent to this specific person; never promise the guide or product roadmap will address a gap that isn't actually built yet.

### 2b. Waitlist

- **Who receives it:** anyone who joins the waitlist without having completed Life Gaps (Group C), or who joins after already completing Life Gaps (folds into Group A).
- **Suppression rules:** Group C stops entirely the moment Life Gaps is completed, before any further Group C message sends.
- **Timing:** T1 immediate, then the 4-email Group C sequence on days 1, 3, 5, 8.
- **Emotional / job-to-be-done:** curiosity about what Krovos actually is, without yet having experienced the recognition moment; the job of this sequence is to earn that moment, not to describe features.
- **Sequence purpose:** drive completion of Life Gaps, since that is the actual recognition event, not the waitlist signup itself.
- **CTA destination in Krovos:** `/life-gaps`.
- **What must not be assumed or overpromised:** never imply the waitlist itself is the product experience; never promise a launch date that isn't confirmed.

### 2c. Gift redemption

- **Who receives it:** a gift recipient who redeems a Krovos gift code at `/gift/redeem`, and separately, the original purchaser once the gift is delivered.
- **Suppression rules:** must not overlap with Group A/B/C at all for a first-time recipient with no prior Life Gaps or waitlist history; a redeeming recipient who already has a waitlist or Life Gaps history should be evaluated against the same dedup rule used elsewhere rather than started fresh.
- **Timing:** confirmed built today is a single delivery email at the scheduled gift-delivery moment (per the canonical record's daily protected gift-delivery cron). No lifecycle follow-up beyond that single email is confirmed to exist.
- **Emotional / job-to-be-done:** someone gave this person Krovos; the job is to make redemption feel like receiving a thoughtful gift, not activating a coupon.
- **Sequence purpose (proposed, copy needed):** a short welcome arc distinct from Group A, since this member did not choose Krovos through the same discovery path, they arrived because someone who knows them chose it for them. A first email confirming the gift and orienting to first steps; a mid-duration check-in relevant to the gift's length (3 or 12 months); and, for a 12-month gift, a renewal-minded email before the gift period ends.
- **CTA destination in Krovos:** `/life-profile` for a recipient who hasn't onboarded yet; `/life-compass` for one who has.
- **What must not be assumed or overpromised:** never assume the recipient wanted a financial product; never treat the gift-giver's relationship to the recipient as known; never promise the gift converts to a discounted subscription unless that pricing is actually decided.
- **Copy needed:** no existing draft copy for this branch exists anywhere in the reviewed material.

### 2d. Beta enrollment

- **Who receives it:** anyone who signs up via `/beta?code=XXXX` and becomes a durable `beta_tester` account.
- **Suppression rules:** independent of Group A/B/C; a beta tester who also completed Life Gaps or joined the waitlist before beta should not receive a duplicate welcome, only the beta-specific one.
- **Timing:** confirmed built is a single post-signup welcome email. No further beta-specific nurture sequence is confirmed to exist beyond that.
- **Emotional / job-to-be-done:** the member just created a durable account with no purchase involved; the job is to explain, plainly, that this account persists and how to return to it, since the prior flow's silent auto-redirect was the exact gap this email was built to close.
- **Sequence purpose (proposed, copy needed):** a short arc guiding a beta tester through the app-inventory and guide-selection prompts at the correct, already-designed timing (after onboarding and some real Core usage, never immediately at signup, per the existing product decision), and a feedback-button reminder once real usage exists.
- **CTA destination in Krovos:** `/life-profile` if onboarding is incomplete; `/beta-guides` once onboarding and some Core usage exist.
- **What must not be assumed or overpromised:** never imply beta access is time-limited unless it actually is; never promise beta feedback changes a specific feature, since that decision isn't Krovos's to promise on the member's behalf.
- **Copy needed:** the welcome email's exact copy state is not confirmed in the reviewed material beyond its existence.

### 2e. Account/profile completion

- **Who receives it:** any member who completes the 8-step Life Profile flow and receives a generated Life View.
- **Suppression rules:** independent of the acquisition-side Groups A/B/C, since this trigger fires after the member is already a Krovos account holder, not a prospect.
- **Timing:** the canonical record states plainly that `/life-view` has no first-time welcome state as of July 2026. This is a confirmed gap, not a built sequence.
- **Emotional / job-to-be-done:** the member just received the recognition moment the whole product promises; the job is to make sure they know their Life Graph, Life Compass, and Life Updates are now active and where to find them, rather than leaving them to discover it unaided.
- **Sequence purpose (proposed, copy needed):** a single "your Life View is ready, here's what's next" email pointing to Life Compass and Life Graph, using the exact body copy already specified in `CLAUDE.md`'s own note for this gap: "Your Life Graph and Life Compass are now active. Start there to see your complete picture."
- **CTA destination in Krovos:** `/life-compass`.
- **What must not be assumed or overpromised:** never restate specific financial figures from the Life View in the email body, since that risks sending sensitive financial detail through email; never claim the plan is complete when Life Updates and ongoing check-ins are how it stays current.
- **Copy needed:** the one line already specified in `CLAUDE.md` can anchor a draft, but a full email has not been written or approved anywhere in the reviewed material.

### 2f. A newly relevant life phase

- **Who receives it:** an existing Krovos member whose Life Graph reflects a newly triggered life-phase signal, using the same signal taxonomy proposed in `product/tool-delivery-map-2026-07-24.md` (a relationship-status change, a new dependent added, a business flag newly set, a between-jobs status change, a windfall entered).
- **Suppression rules:** must never fire for a signal the member entered as a correction or a test; must not duplicate an in-app Life Compass callout that already surfaces the same signal, so the email should feel like a companion nudge, not a repeat of something the member already saw inside the app.
- **Timing (proposed):** near-immediate for the highest-urgency signals (a status change to Widowed, a between-jobs status change), since the Tool Delivery Map identifies these as the clearest cases where proactive surfacing matters most; a short delay (1 to 2 days) for lower-urgency signals to avoid feeling reactive to a data-entry moment.
- **Emotional / job-to-be-done:** life just changed in a way that reorganizes what matters; the job is to acknowledge the change with the same nonjudgmental warmth the Founder Book requires in-product, not to treat it as a marketing trigger.
- **Sequence purpose (proposed, copy needed):** a single email per signal, not a multi-email sequence, since this is meant to feel like Krovos noticed, not like a campaign. Each signal needs its own short, specific message; a generic "we noticed something changed" email would violate the Storytelling Bible's Rule 9 (never assume knowledge, teach inside the story) and Rule 32 (characters fail from reasonable assumptions, never ignorance).
- **CTA destination in Krovos:** the specific relevant tool or guide preview identified for that signal in the Tool Delivery Map (for example, `/widowhood-first-year-checklist` for a Widowed status change), never a generic dashboard link.
- **What must not be assumed or overpromised:** never infer the emotional weight of the change (a job loss is not always devastating; a new business is not always exciting); never promise Krovos has already solved the resulting complexity, only that it's ready to help work through it.
- **Copy needed in full.** No existing draft addresses any of these signals.

### 2g. Guide purchase or guide non-purchase

- **Who receives it:** a member who purchases a Life Phase Guide (purchase branch), or a member who viewed a guide preview without purchasing within a reasonable window (non-purchase branch).
- **Suppression rules:** the non-purchase branch must stop the moment the member purchases any qualifying guide (including via the OR-logic shared-tool ownership rule) or is granted household inheritance access to it; must never re-pitch a guide the member already owns.
- **Timing (proposed):** purchase confirmation immediate; a single non-purchase follow-up no sooner than 3 to 5 days after viewing the preview, to avoid reading as a checkout-abandonment nag.
- **Emotional / job-to-be-done (purchase):** the member just paid for something specific to their situation; the job is to confirm what they now have access to and point to the single next best action inside it, mirroring the guide-page structure defined in `product/guide-personalization-contract-2026-07-24.md`.
- **Emotional / job-to-be-done (non-purchase):** the member considered it and didn't buy, for reasons Krovos doesn't know; the job is to add one piece of specific, useful context relevant to why the guide might matter, not to apply pressure.
- **Sequence purpose (proposed, copy needed):** purchase branch: a single confirmation email pointing directly into the guide's own personalized plan. Non-purchase branch: a single, low-pressure follow-up naming one concrete thing the guide would already know about their situation (per the guide's "what Krovos already knows" block), never a discount offer or urgency framing, consistent with the Founder Book's ban on manufactured urgency.
- **CTA destination in Krovos:** the guide's own page for the purchase branch; the guide's preview page for the non-purchase branch.
- **What must not be assumed or overpromised:** never assume non-purchase means the member can't afford it or isn't interested, since the actual reason is unknown; never discount the guide in an email unless a real, approved discount mechanism exists; never claim the guide "will solve" a life event, only that it prepares for it.
- **Copy needed in full** for both branches.

### 2h. Key Core outcomes (retirement, debt, job transition, caregiving, family planning)

- **Who receives it:** an existing member whose recorded Life Graph or saved tool results show a meaningful outcome: a saved Retirement baseline model, a debt payoff milestone (per Milestone Tracker/"Progress and wins"), a job-transition life-phase change, a new caregiving flag, or a family-planning event (expecting, a new child).
- **Suppression rules:** must not duplicate an in-app "Progress and wins" callout the member already saw on Today; must respect the same emotional-weight caution as Section 2f, since caregiving and job-transition signals in particular can reflect difficult circumstances, not celebratory ones.
- **Timing (proposed):** shortly after the milestone is recorded (same day to next day), since these are meant to feel like acknowledgment, not a scheduled campaign touch.
- **Emotional / job-to-be-done:** the member did something real (saved a retirement plan, hit a debt-payoff milestone) or is navigating something real (a job transition, a new caregiving role); the job is to acknowledge it specifically and point to the one next useful thing, exactly the way `/life-compass`'s own governed current-focus card already does in-app.
- **Sequence purpose (proposed, copy needed):** single acknowledgment emails per milestone type, not a drip sequence, mirroring the "Progress and wins" pattern already built in-app rather than inventing a separate marketing cadence for the same underlying signal.
- **CTA destination in Krovos:** `/life-compass` (to see the milestone in context) or the specific hub involved (`/retirement`, `/life-goals`, `/life-labor` for caregiving-adjacent household load, the relevant guide for job transition or family planning).
- **What must not be assumed or overpromised:** never treat a debt payoff or retirement milestone as evidence the member no longer needs to think about that area; never send a celebratory-toned email for a job-loss or caregiving signal, since those are not achievements to congratulate.
- **Copy needed in full.** No existing draft addresses any Core-outcome milestone email.

---

## 3. Recommended Priority Order

### Pre-launch (must resolve before any new branch ships)

1. Resolve the five open founder decisions named in the July 20, 2026 audit: Day 0 vs. Day 1 timing, the founder-voice "I" vs. "we" exception, story provenance for Maya and Derrick, the Kit vs. Resend provider decision with one unified suppression authority, and approval of the full seven-email belief architecture before Emails 4 through 7 are drafted.
2. Fix the specific compliance gaps already identified: remove the banned filler word "genuinely" from Email 1, correct the incorrect no-unsubscribe assumption, add a valid physical postal address to the shared footer, and make the unsubscribe-link rendering fail closed rather than fail open when a subscriber record doesn't resolve.
3. Reconcile Emails 2 and 3's application state (currently `TODO` placeholders) with their approved copy status, so "approved" and "implemented" mean the same thing.
4. Do not build any new trigger branch from Section 2 until items 1 through 3 are resolved, since every new branch this document proposes sits on top of the same suppression, timing, and provider infrastructure.

### Beta

5. Close the confirmed Life View welcome-state gap (Section 2e) using the exact line `CLAUDE.md` already specifies. This is the lowest-effort, highest-alignment addition, since the copy is already written and the gap is already named.
6. Build the gift redemption welcome arc (Section 2c), since gift subscriptions are already shipped and currently have no lifecycle email beyond delivery.
7. Extend the beta-enrollment welcome into the already-designed app-inventory/guide-selection nudge timing (Section 2d), reusing the existing, already-decided sequencing rather than inventing a new one.

### Post-launch

8. Build the newly-relevant-life-phase branch (Section 2f), starting with the highest-urgency signals identified in the Tool Delivery Map (Widowhood, Divorce/Separation, between-jobs status change) before the lower-urgency ones.
9. Build the guide purchase and non-purchase branches (Section 2g).
10. Build the Core-outcome acknowledgment branch (Section 2h).
11. Complete Group A Emails 4 through 7 and build Group B/C story content, once the pre-launch founder decisions in item 1 are resolved. This is sequenced last, not because it's low value, but because building on unresolved timing, voice, and provider decisions would mean redoing the same work twice.
12. Remediate the win-back sequence per the audit's specific findings (banned language, reason-based routing, verified claims) before any live use, since it already exists in code and any live send today would carry its named risks forward unaddressed.

---

## 4. Social-to-Email Handoff and Lead-Magnet Follow-Up

No confirmed social-to-email capture mechanism beyond the existing waitlist and Life Gaps forms was found anywhere in the reviewed material. `strategic/go-to-market.mdx` names organic content channels (TikTok, Instagram, YouTube, a weekly newsletter) and a referral program, but does not document a specific technical handoff from a social post or bio link into the email architecture.

**Recommendation, not yet built:** every social CTA (bio link, post link, referral mention) should route to exactly one of the two existing entry points, `/life-gaps` or the waitlist, rather than creating a third capture path. This keeps every new social visitor inside the already-governed Group A/B/C deduplication logic (waitlist table checked first, then `life_gaps_submissions`, then path upgrade if applicable) instead of risking a repeat of the retired, now-replaced Path A/B/C confusion the current architecture was built to eliminate. Life Gaps should be the default social destination over the plain waitlist, since it is the actual recognition moment the whole acquisition strategy depends on, not just a signup form.

**Lead-magnet follow-up:** Life Gaps is Krovos's only confirmed lead magnet. Its follow-up architecture is already fully specified in Section 2a and the Group A/B/C system above; no separate lead-magnet-specific sequence exists or is proposed here beyond what Section 2a already covers. If a second lead magnet is created in the future, it should enter the same Group A/B/C dedup logic rather than spawning a parallel system, consistent with the standing instruction not to reintroduce the retired path structure.

---

## 5. A Closing Note on Voice

Every proposed branch above is written to be evaluated against the same standard already governing Group A: does it begin in motion, does it teach something the reader did not know before, does it avoid judging the member for their situation, and does it earn any invitation rather than assuming one. None of the "copy needed" branches should be drafted from a generic template. Each one exists because a specific, real Krovos signal fired, and the email should read that way.
