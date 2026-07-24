# Pre-Launch Email Remediation Plan

**Date:** July 24, 2026
**Purpose:** One implementation-ready gate list that must be true before any Group A, B, or C Soap Opera marketing sequence is re-enabled. This document does not change app code, database records, providers, automations, or live sending. It defines what must change, who decides it, and what evidence proves each gate is actually closed, so a future implementation session has a checklist to execute against rather than a fresh audit to redo.

**Sources:** `marketing/soap-opera-sequence-decision-memo-2026-07-24.md`, `marketing/group-b-c-live-send-verification-2026-07-24.md` (both this repo, July 24, 2026), and direct, read-only inspection of `/Users/christine.smith/krovos`: `lib/email-sequences.ts`, `app/api/cron/send-sequences/route.ts`, `lib/email-templates/index.ts`, `components/emails/KrovosEmailBase.tsx`, `app/api/waitlist/route.ts`, `app/api/life-gaps/submit/route.ts`, `lib/transactional-emails.ts`, `vercel.json`, and the `email_schedule` migrations. No file was modified during this inspection.

---

## 0. Where This Starts From

The verification memo already established, from the code itself, that Group A Emails 2 through 7 and all of Groups B and C are `TODO` placeholders that the live cron would send exactly as written, with no gate that distinguishes a finished template from an unfinished one. The decision memo already resolved the five open governance conflicts (Day 0/Day 1 timing, "I" versus "we" voice, story provenance, Kit versus Resend, and the compliance gap bundle) into founder-ready recommendations, none of which are recorded as approved yet. This plan does not re-litigate either of those documents; it turns their findings into gates with evidence requirements.

---

## 1. Copy Approval Gates

- **Gate 1.1: Email 1 timing correction.** The metadata header in the Email 1 file is corrected from "Day 0" to "Day 1," matching the live architecture.
  *Evidence required:* the corrected file, diffed against its prior version, showing only the metadata change.
- **Gate 1.2: Voice exception decided.** Christine has explicitly approved (or rejected) the narrow "I" voice exception for Email 1 recommended in the decision memo.
  *Evidence required:* a dated, written founder decision, recorded in the Attractive Character document itself if approved, or a note that the alternative (rewrite to "we") was chosen instead.
- **Gate 1.3: Provenance classification recorded.** Maya and Derrick (and any other pre-launch parable-derived story) are explicitly classified as composite/illustrative in their governing files, not left unstated.
  *Evidence required:* a provenance field or note present in each story's governing file.
- **Gate 1.4: Banned language removed.** Email 1's copy no longer contains the banned filler word "genuinely" or any other Attractive Character violation found on review.
  *Evidence required:* a clean grep of the final copy against the banned-word list in `CLAUDE.md`'s Attractive Character section, attached to the approval record.
- **Gate 1.5: Seven-email belief architecture approved.** Christine has approved the belief architecture for Group A's remaining emails (or a revised one) before any of Emails 4 through 7 are drafted past the founder-source stage.
  *Evidence required:* a dated founder approval note referencing the specific belief-shift table it approves.
- **Gate 1.6: Group B and Group C belief architecture exists and is approved.** Neither group has any approved belief architecture today; this is a net-new founder decision, not a correction of an existing one.
  *Evidence required:* a written belief architecture for each group (narrative job, belief shift, invitation, per email) carrying an explicit founder approval date, in the same shape as the Group A table already recommended in the July 20 audit.
- **Gate 1.7: Every scheduled template has real, approved copy.** No template key currently in `TODO_TEMPLATE_KEYS` may remain there once its group is re-enabled.
  *Evidence required:* for each template key, a governing file with a stated "Approved" status and a copy body that is not a `TODO` placeholder.

## 2. Technical Safeguard Gates

- **Gate 2.1: Hard rule: an incomplete template cannot be scheduled or sent.** This is the single most important technical gate. Today, `renderSequenceEmail()` in `lib/email-templates/index.ts` renders any key in `TODO_TEMPLATE_KEYS` and returns a normal-looking subject/HTML pair; the cron in `app/api/cron/send-sequences/route.ts` has no check that distinguishes that output from a finished template before calling Resend. The fix must make it structurally impossible to send a `TODO` template, not merely unlikely: for example, a template registry that only contains entries for approved templates, where an unregistered or explicitly-flagged-incomplete key throws or is refused at the scheduling step (in `scheduleSequence()`), the cron step (before calling Resend), or both, rather than silently rendering placeholder text and sending it.
  *Evidence required:* a passing automated test (or an equivalent build-time check, consistent with the existing pattern of `audit:tool-inputs` and `audit:app-shell` style checks already used elsewhere in this codebase) that attempts to schedule and send a deliberately incomplete template and confirms the send is blocked, not merely logged.
- **Gate 2.2: Cron authorization fails closed.** The verification memo found that `send-sequences`'s own authorization check (`if (cronSecret && authHeader !== ...)`) is silently permissive whenever `CRON_SECRET` is unset, rather than refusing to run. This should be changed so the route refuses to execute at all when `CRON_SECRET` is not configured, regardless of the request's own header.
  *Evidence required:* a test confirming the route returns an error (not a 200) when `CRON_SECRET` is absent from the environment.
- **Gate 2.3: Entry-point pause capability exists.** A real, tested way to stop new subscribers from entering Group B or Group C (at `app/api/waitlist/route.ts` and `app/api/life-gaps/submit/route.ts`) independently of pausing Group A, so a future incident doesn't require an all-or-nothing rollback.
  *Evidence required:* a documented, tested flag or equivalent mechanism, plus a record of it being exercised at least once in a non-production environment.
- **Gate 2.4: Group A Emails 2 through 7 are wired into the dispatcher.** Email 2 and Email 3's approved copy exists in `content/emails/` but was never connected to `lib/email-templates/index.ts`'s dispatch logic; this is an implementation gap independent of any further copy work.
  *Evidence required:* `renderSequenceEmail()` returning each approved email's real subject/body, confirmed by a direct call in a non-production environment, not by code review alone.

## 3. Consent / Unsubscribe / Footer Compliance Gates

- **Gate 3.1: Unsubscribe rendering fails closed.** `components/emails/KrovosEmailBase.tsx` currently omits the unsubscribe link silently whenever `unsubscribeUrl` is `undefined`, rather than blocking the send. This must change so a send is blocked entirely when no working unsubscribe link can be resolved, for every group.
  *Evidence required:* a test that simulates an unresolvable subscriber record and confirms no email is sent, rather than confirming only that the footer looks different.
- **Gate 3.2: Every subscriber state resolves a working unsubscribe link.** `buildUnsubscribeUrl()` in the cron must correctly resolve a link for Group B (via `life_gaps_submissions`), Group C (via `waitlist`), and any state where an A/B row is missing a `submission_id`.
  *Evidence required:* a real, followed unsubscribe link for at least one test subscriber in each group, confirmed to actually suppress further sends for that subscriber.
- **Gate 3.3: Physical postal address added.** `KrovosEmailBase`'s shared footer contains no postal address today, for any template.
  *Evidence required:* the address visible in a rendered test email, reviewed against CAN-SPAM's requirement for a valid physical postal address.
- **Gate 3.4: Consent language matches actual sequence content.** The consent shown to a member at waitlist signup or Life Gaps submission should accurately describe the kind of ongoing content they will receive, once real copy exists for Groups B and C.
  *Evidence required:* the current signup-flow consent copy, reviewed side by side with the approved sequence content, with any mismatch resolved before launch.
- **Gate 3.5: Legal review completed.** The decision memo already states this should not be finalized on a brand-team decision alone.
  *Evidence required:* a dated sign-off from counsel covering the footer, unsubscribe mechanism, and consent language specifically, not a general product review.

## 4. List Hygiene / Suppression Gates

- **Gate 4.1: One suppression source governs every list.** Today, suppression is checked across the `waitlist` and `life_gaps_submissions` tables inside `isUnsubscribedAnywhere()`; if Kit or any other tool ever holds its own subscriber list, that list must be included in the same check, or explicitly and permanently excluded from ever sending marketing content, so there is no path where an unsubscribe in one place fails to suppress a send from another.
  *Evidence required:* a documented list of every system capable of sending to a Krovos subscriber, with a stated suppression relationship for each one.
- **Gate 4.2: Group A/Group C mutual exclusivity holds.** The July 20 audit requires that a subscriber never run Group A and Group C simultaneously; the current code appears to handle this correctly at the entry points (a waitlist signup with an existing Life Gaps row skips Group C scheduling), but this should be verified directly rather than assumed from a single code read.
  *Evidence required:* a test confirming that a subscriber who completes Life Gaps first, then joins the waitlist, or the reverse order, ends up in exactly one active sequence, never both.
- **Gate 4.3: Group C exit-on-completion actually stops remaining sends.** The cron's `hasCompletedLifeGaps()` check exists and appears correct on read, but should be confirmed against a real scheduled row, not code inspection alone.
  *Evidence required:* a test subscriber who completes Life Gaps mid-Group-C-sequence, confirmed to have remaining Group C rows marked skipped rather than sent.
- **Gate 4.4: Win-back sequence content risks are separately resolved.** The verification memo notes Group W's own templates are implemented but carry the specific unresolved risks the July 20 audit already named (banned filler language, an assumed rather than recorded cancellation reason, unverified pricing claims). These are content and logic fixes, not new infrastructure.
  *Evidence required:* a corrected Group W copy set and a routing check confirming messages reference the subscriber's actual recorded cancellation reason, not a generic assumption.

## 5. Measurement Gates

- **Gate 5.1: A written measurement contract exists before launch.** Per the July 20 audit's own recommendation: delivered rate, bounce rate, unsubscribe rate, and spam-complaint rate, each with a defined guardrail.
  *Evidence required:* the written contract itself, dated and attached to the launch decision.
- **Gate 5.2: An explicit stop rule exists.** A defined threshold (for example, a spam-complaint or unsubscribe rate crossing a stated percentage) that automatically pauses the sequence in question, not merely flags it for review.
  *Evidence required:* the stated threshold and a description of who is notified and what pauses when it's crossed.
- **Gate 5.3: Monitoring can detect a repeat of the placeholder-send failure.** If a `TODO` or otherwise unfinished template is ever sent again, it should be caught automatically, not discovered from a subscriber reply, which is how a failure like this most often surfaces in practice.
  *Evidence required:* a monitoring check or alert (even a simple one) that specifically flags any outgoing sequence email whose subject or body matches the `TODO` pattern, confirmed to fire in a test.

## 6. Founder Decision Gates

These are decisions only Christine can make; no implementation or legal review substitutes for them.

- **Gate 6.1: The five conflicts in the decision memo are actually decided**, not merely recommended: Day 0/Day 1 timing, the "I"/"we" voice exception, story provenance classification, Kit versus Resend ownership, and the compliance remediation approach.
- **Gate 6.2: The Group A seven-email belief architecture is approved**, or a revised one is substituted, before Emails 4 through 7 move past founder-source drafts.
- **Gate 6.3: Group B and Group C belief architectures are approved** as net-new decisions, since neither has ever had one.
- **Gate 6.4: An explicit go/no-go decision on immediately pausing the two entry-point routes** (`app/api/waitlist/route.ts`, `app/api/life-gaps/submit/route.ts`) is made and recorded, independent of how quickly the rest of this plan is executed, since that pause is the smallest change that stops new subscribers from entering an unfinished sequence today.
  *Evidence required for all founder gates:* a dated, written decision for each item, referencing this document or the two source memos by name.

---

## 7. Template-Level Readiness Checklist

Applied individually to every template key before its group is re-enabled. A template is not ready unless every row below is checked.

| Requirement | What "done" looks like |
|---|---|
| Approved copy exists | A governing file states "Approved," not "draft" or unstated |
| Wired into the dispatcher | `renderSequenceEmail()` returns the real subject/body for this key, confirmed by direct invocation, not code review |
| Banned language checked | A clean grep against the Attractive Character's banned-word list |
| Provenance recorded | Composite/illustrative or real-with-permission, explicitly stated |
| Unsubscribe link resolves | Confirmed for a real test subscriber in this specific group |
| Postal address present | Visible in the rendered output |
| Cannot be sent while incomplete | The template is absent from any "incomplete" registry/list by the time it's marked ready, and was structurally blocked from sending before that point (Gate 2.1) |
| Subject/preheader reviewed | Matches or supersedes the existing subject/preheader recommendation set, with any change noted |

**Hard rule, restated plainly: a template with any unchecked row above must be structurally incapable of being scheduled or sent, not merely undocumented as ready.** This is the single fix that would have prevented the condition the verification memo found. Everything else in this plan matters, but this rule is the one that makes every other gap survivable if it slips through review.

---

## 8. Safe to Keep Running Now vs. Paused

### Safe to keep running now (transactional, not marketing)

These are implemented in `lib/transactional-emails.ts`, `lib/email.ts`, and `lib/cancellation-confirmation.ts`, entirely separate from `lib/email-templates/index.ts`'s sequence dispatcher and the `email_schedule` table. Pausing Group A/B/C does not require touching any of these:

- T1 Waitlist Confirmation (`sendWaitlistConfirmation`)
- T2 Life Gaps Results Delivery (`sendLifeGapsResults`)
- Cancellation confirmation (`sendCancellationConfirmationOnce` / `sendCancellationConfirmationEmail`)
- Gift-subscription delivery (`sendGiftEmail`)
- Birthday recognition (the `birthday-check` cron)
- In-app feedback alert emails

### Should be paused pending this plan's gates

- **Group A, Emails 2 through 7.** Even though Emails 2 and 3 have approved copy, that copy is not wired into the dispatcher, so the live behavior today is identical to Groups B and C: `TODO` content would send on schedule.
- **Group B, entire sequence.** No approved copy exists; the entry point (`life-gaps/submit`, `results_only` path) is fully live today.
- **Group C, entire sequence.** No approved copy exists; the entry point (`waitlist` signup) is fully live today.
- **Group W (win-back).** Not a placeholder problem, but carries documented, unresolved content risks (banned language, assumed cancellation reason, unverified pricing) that should be resolved before continued live use, per Gate 4.4.

### What "paused" should mean, precisely

Per Gate 2.3, pausing should stop new subscribers from entering an affected sequence at its entry point, and should stop the cron from sending any already-scheduled row for an affected group, without needing to delete existing `email_schedule` rows or otherwise mutate subscriber data. This plan does not implement that pause; it only specifies what evidence would prove it works once built.

**This document changes nothing by itself.** Every gate above remains open until a future implementation session executes it and records the evidence this plan specifies.
