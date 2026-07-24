# Soap Opera Sequence Decision Memo

**Date:** July 24, 2026
**Purpose:** Resolve the five open conflicts identified in `content/marketing/soap-opera-sequence-audit-2026-07-20.md` (`krovos-os` repo, read only) into founder-ready recommendations, so Christine can approve or redirect each one directly rather than inheriting an unresolved audit. No approval is assumed anywhere in this memo. Every recommendation below is a proposal awaiting Christine's decision, not a record of a decision already made.

**Sources:** `content/marketing/soap-opera-sequence-audit-2026-07-20.md`, `content/emails/README.md` and the individual Group A email files (`krovos-os` repo, read only), `CLAUDE.md`'s Tech Stack and Email Architecture sections (`krovos` repo, read only, not edited), `brand/attractive-character.mdx`, `governing/founder-edition.mdx`, `conviction/founder-book.mdx`, `brand/storytelling-bible.mdx`, and `marketing/soap-opera-trigger-architecture-2026-07-24.md` (this repo, written July 24, 2026).

---

## 1. Day 0 / Day 1 Sequence Timing

**Conflicting evidence:** the tracked file `content/emails/group-a-email-01-founder.md` states "Day: 0 (sends immediately on signup)." The live, built email architecture documented in `CLAUDE.md` reserves Day 0 for the two transactional sends (T1 Waitlist Confirmation, T2 Life Gaps Results Delivery) and starts the Soap Opera sequence itself on Day 1, with Group A running on days 1, 3, 5, 8, 12, 16, 20. Emails 2 and 3's own files already treat Email 1 as a Day 1 event, since their "tomorrow" callbacks assume a 2-day gap consistent with a Day 1 to Day 3 cadence, not Day 0 to Day 3.

**Recommendation:** adopt Day 1 as the canonical timing for Email 1, and correct Email 1's file header to match. Do not change the live transactional architecture.

**Reasoning:** Krovos already deliberately separates functional confirmation (Category 1: "you're on the list," "here are your results") from the emotional, story-driven relationship (Category 2: the Soap Opera sequence). A subscriber who just signed up is still in "did that work?" mode, not story-reading mode. Sending a long, emotionally weighted personal narrative in the same moment as a functional confirmation would crowd the inbox and work against the Storytelling Bible's Rule 2 ("begin in motion"), since the reader hasn't finished the prior action yet. Two of the three already-approved files (Emails 2 and 3) implicitly assume Day 1 already, which makes Day 1 both the more coherent choice and the path of least rework.

**What changes if approved:** the metadata header in `content/emails/group-a-email-01-founder.md` is corrected from "Day: 0" to "Day: 1." No schedule code changes, since `lib/email-sequences.ts` and `email_schedule` already implement Day 1 per `CLAUDE.md`.

**Decision type:** founder decision (confirming which architecture is canonical), with a small follow-on documentation correction. Not a legal or compliance matter.

---

## 2. "I" Versus "We" Voice

**Conflicting evidence:** the Attractive Character states plainly, for the Origin Story: "Always told in 'we' voice. Never 'I.'" Email 1, the approved and implemented founder oil-light dashboard story, is written entirely in first-person "I" voice. It is also, per the Founder Book, a true account of Christine's own life ("User Zero was not a simulation. It was her own life"), not the abstracted collective origin narrative the AC's "we" framing describes elsewhere ("We built Krovos because we kept watching the same thing happen").

**Recommendation:** create one narrow, explicit exception in the AC: a first-person account of Christine's own specific, personal lived memory, when used as the founder's origin-story email, may use "I" voice. The general "we" framing remains the default rule for every other use of the origin story across VSLs, the website, ads, and any other channel.

**Reasoning:** the "we" rule exists to keep Krovos's origin story reading as a shared discovery rather than a single person's memoir when it's used broadly across many channels. Email 1 is a different rhetorical moment: it is a specific, singular memory (a car, a dashboard light, a specific age) narrated directly by the person who lived it, to a reader meeting Krovos for the first time. Forcing that memory into "we" voice would falsely collectivize an individual experience and strip the tiny, specific details that the Storytelling Bible's Rule 5 says create trust. The alternative the audit leaves open (rewrite Email 1 into "we" voice) is not recommended, since it would turn a specific personal story into a marketing statement, which is the opposite of what Email 1 is doing.

**What changes if approved:** the Attractive Character's Origin Story section gains one added sentence describing this narrow, single-asset exception. No other marketing copy is affected; the "we" rule stays fully intact everywhere else.

**Decision type:** founder decision. This is a brand-governance change to the AC document itself, which only Christine can approve. It is not an implementation task or a legal matter.

---

## 3. Story Provenance and What Can Be Presented as a Founder Story

**Conflicting evidence:** the AC's Parables section requires every parable to be a "named, specific story with real stakes," and states that "additional parables are added as real user situations emerge post-launch. Real stories, with permission, told with the same structure and the same standard." Maya and Derrick already appear as illustrative parables inside the AC document itself, written before launch, with no real user situations yet to draw from by the document's own logic. The Group A email files for Maya and Derrick, however, carry no documented provenance at all: the July 20 audit found no record of whether either is a real, consented story, a documented anonymized case, or a composite, and flagged this explicitly as unresolved.

**Recommendation:** classify Maya and Derrick, and any other AC-parable-derived email story used before real post-launch user situations exist, explicitly as composites: illustrative stories written to the same structure and standard the AC requires (named character, real and specific stakes, no judgment), but not claimed as verified, individually-consented case studies. This classification should be recorded in each story's governing file (not necessarily restated in the email body copy itself), and any language in the email body that asserts documentary fact about a specific real subscriber should be reviewed and softened if present.

**Reasoning:** Krovos has not launched yet, so by the AC's own stated logic, there are no real subscriber stories to draw from before launch, which means the pre-launch parables were always functioning as illustrative device first. The Founder Book's own honesty principle, "the truth when the truth is difficult," applies to Krovos's claims about itself, not only to the guidance Krovos gives members. Being clear that these are composites, while keeping every other narrative standard intact, satisfies that principle without requiring any rewrite of the story content itself.

**What must not happen:** presenting a composite as a verified, real, individually-consented story. That is a factual-representation risk, not a tone preference, and is the specific thing this recommendation is designed to prevent.

**What changes if approved:** each governing email file's provenance field is corrected from unstated to "composite, illustrative, pre-launch." No rewrite of the Maya or Derrick narrative content is required unless a review of the current body copy finds a documentary-fact claim that needs softening.

**Decision type:** primarily a founder decision (confirming the provenance classification), with a secondary legal/compliance touchpoint: if any future revision of this copy frames Maya or Derrick as a customer testimonial rather than an illustrative parable, that specific framing should get a compliance check against FTC rules on depicting fictional endorsers as if real, before it ships that way.

---

## 4. Kit Versus Resend Operational Ownership

**Conflicting evidence:** `CLAUDE.md`'s Tech Stack table names Kit (formerly ConvertKit) as "Marketing email" and Resend as "Email (transactional)." The live, built Soap Opera Sequence code (`lib/email-sequences.ts`, the `/api/cron/send-sequences` cron, the `email_schedule` table) sends every scheduled Group A/B/C marketing message through Resend, not Kit. This is a direct contradiction between the documented system of record and what actually runs.

**Recommendation:** formally designate Resend as the system of record for all lifecycle and marketing email, including Groups A/B/C, win-back, and any future sequence proposed in `marketing/soap-opera-trigger-architecture-2026-07-24.md`. Do not attempt a migration back onto Kit for functionality that already runs on Resend. If Kit currently serves a distinct purpose Christine wants to keep separate (a newsletter, a different audience, a specific feature Resend doesn't offer), name that purpose explicitly and write down which subscriber data lives in which tool and how a suppression event in one propagates to the other.

**Reasoning:** the entire governed suppression architecture, the deduplication logic across the waitlist table and `life_gaps_submissions`, and every currently confirmed-built sequence already runs on Resend. Migrating to Kit at this stage would mean rebuilding already-working, already-tested infrastructure for no functional gain, and would introduce exactly the risk the audit already warns against: "a single global suppression source must govern Resend, Kit, or any future provider." Two active systems with two suppression lists is a real compliance and trust risk (a subscriber who unsubscribes in one system could still receive mail from the other) unless a single authority is named.

**What changes if approved:** `CLAUDE.md`'s Tech Stack entry for "Marketing email" is corrected to Resend, or to a two-line entry naming Resend as primary and Kit's specific remaining purpose if one exists. This is a documentation correction in the `krovos` repo, out of scope for this docs-only session, and should be flagged for a future `krovos`-repo session rather than acted on here.

**Decision type:** founder decision (which system is canonical), with a documentation-correction implementation task once decided. Not a legal or compliance matter on its own, unless Kit is still actively billed with no remaining use, which would be a business-operations follow-up outside this memo's scope.

---

## 5. Compliance / Suppression / Consent Gaps

**Conflicting evidence, bundled from the July 20 audit:**
- Email 1's note incorrectly assumes no unsubscribe link is required because it is a "welcome" email.
- The shared email template only renders an unsubscribe link when a subscriber record resolves successfully; a missing or broken subscriber record could therefore produce a commercial email with no opt-out at all, a fail-open condition rather than fail-closed.
- The shared footer does not confirm a valid physical postal address, which the FTC's CAN-SPAM business compliance guide requires for commercial email.
- The current cron runs at 9:00 UTC, which is 5:00 a.m. Eastern during daylight saving time; send-time policy and recipient localization are unresolved.
- No documented per-email measurement contract exists yet (delivered rate, bounce rate, unsubscribe/complaint guardrails, a stop rule).

**Recommendation:** treat every Soap Opera Sequence email as a commercial message that requires a working unsubscribe link and a valid physical postal address by default, with no "this one doesn't need it" exception unless counsel actually determines otherwise in writing. Change the unsubscribe-link rendering to fail closed: if a subscriber record cannot resolve, block the send entirely rather than deliver a commercial email without the required footer. Remove the "no unsubscribe required" note from Email 1's file and add the standard footer. Decide and document a send-time policy rather than leaving 9:00 UTC as an unexamined default. Define the measurement contract before any new sequence goes live.

**Reasoning:** this is a legal-exposure and member-trust question, not a brand-voice question. The FTC's CAN-SPAM guidance requires accurate routing information, a valid physical postal address, a clear and working opt-out method, and opt-out processing within 10 business days for commercial email (`https://www.ftc.gov/business-guidance/resources/can-spam-act-compliance-guide-business`). Treating a fail-open unsubscribe gap or a missing postal address as a low-priority cleanup item contradicts the Founder Book's own honesty pillar applied to the company's own conduct, not only to the guidance it gives members.

**What changes if approved:** a valid physical postal address is added to the shared email footer template; the footer/unsubscribe rendering logic changes to fail closed; the "no unsubscribe required" note is removed from Email 1's file and the standard footer is added to it; a send-time policy is decided and documented; the measurement contract (delivered/bounce/unsubscribe/complaint rates, a defined stop rule) is written down before any sequence in this category launches live.

**Decision type:** primarily a legal/compliance review. The postal address and unsubscribe requirements are legal minimums, not stylistic preferences, and should not be finalized on a brand-team decision alone. The send-time policy and measurement contract are founder/operational decisions that can proceed in parallel. **Do not launch any live send in this category without an actual legal review of the footer and unsubscribe mechanism, regardless of any other approval in this memo.**

---

## 6. Recommended Minimum Viable Pre-Launch Sequence

The smallest sequence that can be safely finalized before beta, built entirely from material that already exists and is already approved, once the recommendations above are actually decided (not assumed):

| Send | Content | Status once recommendations are approved |
|---|---|---|
| Day 0 | T1 (Waitlist Confirmation) and T2 (Life Gaps Results Delivery) | Already built, already transactional, unaffected by any of the five conflicts above. |
| Day 1 | Email 1, the founder story | Ready once: the Day field is corrected to Day 1 (Section 1), the narrow "I" voice exception is granted (Section 2), and the compliance footer/unsubscribe fixes are applied (Section 5). |
| Day 3 | Email 2, Maya | Ready once: implemented in the dispatcher (currently a `TODO` placeholder, an implementation task, not a founder-decision blocker), the provenance classification is recorded as composite/illustrative (Section 3), and the compliance footer fix is applied (Section 5). |
| Day 5 | Email 3, Derrick | Ready under the same conditions as Email 2. |

This gives Group A a real, accurately-labeled, compliant three-email opening arc for beta, using only material that is already written and already approved in substance. It deliberately does not attempt Emails 4 through 7, or Groups B and C, since those depend on decisions and content that do not yet exist (see Section 7).

**One item requires immediate verification, not scheduled resolution:** if Group B and Group C's schedules are already live in production per `CLAUDE.md`'s documented architecture, and the audit found no authoritative copy for either, then real subscribers may currently be receiving placeholder or `TODO` content on those tracks today. This should be checked directly and, if confirmed, treated as an active issue to pause, not a backlog item to sequence.

---

## 7. Blocked List

Items that must wait for a decision, content, or review before they can be finalized, none of which this memo resolves on its own:

- **Group A Emails 4 through 7**, blocked on Christine's approval of the full seven-email belief architecture recommended in the July 20 audit, plus durable repository import of the founder-supplied source for Emails 4 through 6, plus a founder review decision on Email 7's draft.
- **All of Group B**, blocked on the fact that no authoritative copy exists anywhere in the reviewed material.
- **All of Group C**, blocked on the same absence of copy.
- **The win-back sequence (Group W)**, blocked on its own separately documented remediation list: removing banned filler and "most people" language, routing by actual recorded cancellation reason instead of an assumed one, verifying current product inventory and named capabilities, reconciling the stated data-retention window with the real deletion process, and verifying pricing/discount claims against the current checkout.
- **Kit's exact remaining purpose, if any**, blocked on Christine naming it explicitly (Section 4).
- **The legal review of the footer, postal address, and unsubscribe mechanism**, blocked on actual counsel engagement, not a docs-repo decision (Section 5).
- **Every new trigger branch proposed in `marketing/soap-opera-trigger-architecture-2026-07-24.md`** (gift redemption, beta enrollment extension, Life View welcome, newly relevant life-phase branches, guide purchase/non-purchase, Core-outcome milestones), all explicitly marked copy needed there and out of scope for this memo's minimum viable sequence.
