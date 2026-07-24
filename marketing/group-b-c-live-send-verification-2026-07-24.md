# Group B / Group C Live-Send Verification Memo

**Date:** July 24, 2026
**Purpose:** Determine, from the actual committed Krovos code and configuration, whether Group B or Group C of the Soap Opera Sequence is currently capable of sending email to real people, and whether that email could be placeholder or non-compliant content. This memo is read-only inspection. No email, automation, provider setting, database record, or application code was activated, scheduled, sent, or edited to produce it.

**Method:** direct inspection of `/Users/christine.smith/krovos` source files (read only, no changes made): `lib/email-sequences.ts`, `app/api/cron/send-sequences/route.ts`, `lib/email-templates/index.ts`, `components/emails/KrovosEmailBase.tsx`, `app/api/waitlist/route.ts`, `app/api/life-gaps/submit/route.ts`, `vercel.json`, and `supabase/migrations/20260704_email_schedule.sql` and `supabase/migrations/20260712_winback_sequence.sql`. Cross-referenced against `marketing/soap-opera-trigger-architecture-2026-07-24.md` and `marketing/soap-opera-sequence-decision-memo-2026-07-24.md` (this repo) and the July 20, 2026 `krovos-os` audit those two memos already summarized.

**Headline finding: yes.** Based on the code as committed, both Group B and Group C are fully wired end to end, from a real member action through to a real Resend send, and every one of their scheduled emails is a `TODO` placeholder with no approved copy. A live subscriber who joins the waitlist or completes Life Gaps with the "results only" option today would receive literal placeholder text unless something outside this code (a paused cron, a missing API key, a dashboard-level override) is currently preventing it. This memo cannot confirm or rule out such an external override, since that would require inspecting the live Vercel dashboard and Supabase project directly, which is out of scope here (see the verification gap noted in Section 3).

---

## 1. Every Route, Cron, Provider Configuration, and Database State That Could Trigger Group B or Group C

### Entry points (what starts a Group B or Group C schedule)

- **`app/api/waitlist/route.ts`**: on any new waitlist signup where the email has no existing `life_gaps_submissions` row, the route calls `scheduleSequence(normalizedEmail, '', 'C')` unconditionally, immediately after sending the T1 confirmation. There is no feature flag, environment check, or kill switch gating this call. If the email already has a Life Gaps submission, the route correctly treats the subscriber as already Group A and skips scheduling C.
- **`app/api/life-gaps/submit/route.ts`**: when a Life Gaps submission's `path === 'results_only'`, the route calls `scheduleSequence(email, name || '', 'B', rowId)` unconditionally, after sending T2. There is no flag or kill switch gating this call either. The `path === 'waitlist'` branch correctly schedules Group A instead.

### Scheduling logic

- **`lib/email-sequences.ts`** defines `GROUP_B_SEQUENCE` (5 steps: days 1, 3, 6, 9, 13, template keys `group-b-01-anchor` through `group-b-05-close`) and `GROUP_C_SEQUENCE` (4 steps: days 1, 3, 5, 8, template keys `group-c-01-marcus-jade` through `group-c-04-final`). `scheduleSequence()` writes one row per email into `email_schedule` with a `scheduled_for` date computed from UTC day offsets, and does not send anything itself.

### Sending logic

- **`app/api/cron/send-sequences/route.ts`**: a daily cron that selects every `email_schedule` row due today (`scheduled_for = today`, not yet sent, skipped, or unsubscribed), regardless of `group_id`. For each row it checks a global unsubscribe flag (waitlist or Life Gaps), a Group-C-specific "already completed Life Gaps" exit condition, renders the template via `renderSequenceEmail()`, and sends via Resend if a template renders successfully. There is no check anywhere in this route that distinguishes an approved template from a placeholder template before sending. A `TODO` template is sent exactly the same way a real one is.
- **`vercel.json`** registers `/api/cron/send-sequences` on the schedule `0 9 * * *` (9:00 UTC daily) as one of six active production crons. As committed, this is a live, scheduled, production cron entry, not a disabled or commented-out one.

### Template rendering, the actual content that would be sent

- **`lib/email-templates/index.ts`** defines a `TODO_TEMPLATE_KEYS` array containing all 5 Group B template keys and all 4 Group C template keys (plus 6 of Group A's own 7 keys: everything except `group-a-01-founder`). Any template key in that array is rendered by `renderTodoTemplate()`, which produces a subject literally reading `[TODO: {template-key} subject]` and a body reading `TODO: {template-key} content`, wrapped in the shared `KrovosEmailBase` layout. `renderSequenceEmail()` has no separate code path that blocks a `TODO`-classified template from being sent; it renders and returns the placeholder exactly as it would a finished one, and the calling cron sends whatever comes back.
- **`components/emails/KrovosEmailBase.tsx`** is the shared footer for every sequence email, `TODO` or not. It renders an unsubscribe link only when both `showUnsubscribe` is true and a resolved `unsubscribeUrl` string is present (`{showUnsubscribe && unsubscribeUrl && (...)}`). If `unsubscribeUrl` is `undefined`, the link silently does not render, with no fallback text and no block on sending. The footer contains no physical postal address anywhere, for any template, approved or placeholder.

### Database state

- **`supabase/migrations/20260704_email_schedule.sql`** creates `email_schedule` with a `group_id` check constraint allowing `'A'`, `'B'`, `'C'` (later extended to include `'W'` for win-back in `20260712_winback_sequence.sql`). There is no `enabled`, `paused`, or similar flag column anywhere in this schema. Nothing in the database schema itself can pause Group B or C independently of the application code; the only real controls are the entry-point routes (Section 1, above) and whatever the cron itself checks at send time.

### Authentication note on the cron itself

- The cron's own authorization check is `if (cronSecret && authHeader !== 'Bearer ${cronSecret}') return 401`. If `CRON_SECRET` is unset or empty in the deployed environment, this condition never triggers, and the route would respond to any request with no authorization at all. This memo cannot confirm the current live value of `CRON_SECRET` in Vercel (that is a deployed-service check outside this memo's scope), but the canonical current-state record documents that `CRON_SECRET` was rotated and verified on July 21, 2026, which implies a value is set. The code pattern itself is fail-open by design (silently permissive when the secret is absent) rather than fail-closed (refusing to run without a configured secret), and is flagged in Section 4 regardless of the secret's current live value.

---

## 2. Live, Disabled, Placeholder-Only, or Blocked: Status of Each Sequence

| Sequence | Entry point status | Schedule status | Template/copy status | Overall status |
|---|---|---|---|---|
| **Group A, Email 1** | Live (life-gaps submit, `path = waitlist`) | Live (Day 1) | Approved copy, actually implemented (`GroupA01Founder.tsx`) | **Live and functioning as intended.** |
| **Group A, Emails 2-7** | Same entry point as Email 1, since the whole sequence is scheduled at once | Live (Days 3, 5, 8, 12, 16, 20) | All 6 remaining keys are in `TODO_TEMPLATE_KEYS`, including Emails 2 and 3, whose content is marked "approved" in `content/emails/` but was never wired into the dispatcher | **Live schedule, placeholder content.** A real Group A subscriber who reaches Day 3 today would receive `[TODO: group-a-02-maya subject]`, not the approved Maya story. |
| **Group B (all 5 emails)** | Live (life-gaps submit, `path = results_only`) | Live (Days 1, 3, 6, 9, 13) | All 5 keys are `TODO` placeholders; no approved copy exists anywhere in the reviewed material | **Fully live trigger, fully placeholder content.** |
| **Group C (all 4 emails)** | Live (waitlist signup with no prior Life Gaps) | Live (Days 1, 3, 5, 8), with a correct exit condition if Life Gaps is completed mid-sequence | All 4 keys are `TODO` placeholders; no approved copy exists anywhere in the reviewed material | **Fully live trigger, fully placeholder content.** |
| **Group W (win-back)** | Live (Stripe webhook, on a qualifying cancellation event) | Live (Days 14, 19, 24, 28 post-cancellation) | All 4 templates are implemented (not `TODO`), but the `krovos-os` audit lists specific unresolved content risks (banned filler/"most people" language, an assumed rather than recorded cancellation reason, unverified pricing claims) | **Live trigger, implemented content, documented content risk, not a placeholder problem.** |

---

## 3. Whether a Real Subscriber Could Receive Placeholder or Non-Compliant Copy Today

**Placeholder copy:** yes, as the code is written and as `vercel.json` configures it, a real member joining the waitlist or submitting Life Gaps with the "results only" path today would have Group C or Group B rows written to `email_schedule`, and the daily 9:00 UTC cron would send whatever renders for those rows, including a literal `[TODO: ...]` subject and `TODO: ... content` body, with no gate anywhere in the send path that checks whether a template is finished before sending it.

**Non-compliant copy:** yes, independent of the placeholder question. Every sequence email, including the one fully approved and implemented Email 1, shares the same `KrovosEmailBase` footer, which has no physical postal address at all, and which silently omits the unsubscribe link whenever `unsubscribeUrl` fails to resolve rather than blocking the send. This is a live condition in the current code, not a documentation gap; it matches exactly what the July 20, 2026 audit flagged, and this inspection confirms the flagged behavior is actually present in the source, not merely asserted.

**What this memo cannot confirm:** whether the live Vercel deployment currently matches this committed code exactly, whether the `/api/cron/send-sequences` cron is actually enabled in the live Vercel cron dashboard (as opposed to merely registered in `vercel.json`), and whether `RESEND_API_KEY` is currently a valid, active key in the production environment. Confirming any of those requires direct inspection of the live Vercel and Resend dashboards, which are deployed services outside this memo's read-only, docs-only scope. **Recommend Christine confirm these three items directly and immediately**, since the code-level analysis above shows no application-level safeguard that would prevent a send if all three are true, which the canonical current-state record's own repeated references to a working, verified Resend integration suggest is the more likely case, not the less likely one.

---

## 4. Fail-Closed Remediation Checklist Before Any Group B/C Launch

Every item below must be true before Group B or Group C sends real emails to real subscribers. This is a checklist, not an implementation; nothing in this memo performs any of these changes.

### Consent
- [ ] Confirm that waitlist signup and Life Gaps "results only" submission actually constitute valid consent for the specific marketing content that will be sent, not only for a single transactional confirmation.
- [ ] Confirm consent language shown to the member at signup matches what the sequence will actually contain, once real copy exists.

### Unsubscribe
- [ ] Change `KrovosEmailBase`'s unsubscribe rendering from silently omitting the link when `unsubscribeUrl` is unresolved to blocking the send entirely in that case (fail closed, not fail open).
- [ ] Verify `buildUnsubscribeUrl()` in the cron correctly resolves a working link for every real subscriber state (Group B via `life_gaps_submissions`, Group C via `waitlist`), not only the common case.
- [ ] Confirm the one-click unsubscribe (`List-Unsubscribe-Post: List-Unsubscribe=One-Click`) header actually works end to end against a real Resend send.

### Suppression
- [ ] Confirm a single suppression source governs every list a subscriber could be on (waitlist, `life_gaps_submissions`, any future Kit list per the open Kit/Resend decision in the decision memo), so an unsubscribe in one place stops every sequence, not just the one it was raised in.
- [ ] Add an explicit, tested guard so a `TODO`-classified template can never be sent by the cron, regardless of schedule state, until it is replaced with approved copy. Today nothing in `renderSequenceEmail()` or the cron prevents this.

### Postal address
- [ ] Add a valid physical postal address to `KrovosEmailBase`'s shared footer. Today there is none, for any sequence email.

### Timing
- [ ] Decide and document a deliberate send-time policy; today the cron fires at 9:00 UTC (5:00 a.m. Eastern during daylight saving) with no stated rationale or recipient localization.
- [ ] Reconcile the Email 1 file's stated "Day 0" against the live Day 1 architecture (see the decision memo's Section 1) so schedule documentation and code agree.

### Provider ownership
- [ ] Resolve the Kit-versus-Resend ownership question from the decision memo before scaling Group B/C content, so a future provider change doesn't require rebuilding the suppression and scheduling logic that already exists on Resend.

### Measurement
- [ ] Define, before launch, the measurement contract already recommended in the July 20, 2026 audit: delivered rate, bounce rate, unsubscribe and spam-complaint guardrails, and an explicit stop rule if any of them cross a threshold.
- [ ] Add basic monitoring so a `TODO` send, if one ever occurs again, is caught automatically rather than discovered by a subscriber reply.

### Founder approval
- [ ] Do not write or approve Group B or Group C copy until Christine approves the underlying belief architecture for each sequence, consistent with the July 20 audit's own instruction not to draft the remaining Group A emails without that approval first, which applies equally to Groups B and C, since neither has any approved architecture today.
- [ ] Confirm Christine has reviewed this memo's headline finding and made an explicit decision on whether Group B and Group C should be paused at the entry-point routes (`app/api/waitlist/route.ts`, `app/api/life-gaps/submit/route.ts`) until the remaining items in this checklist are complete, since pausing those two call sites is the smallest possible change that would stop new subscribers from entering an unfinished sequence while leaving everything else untouched.

**No item on this checklist has been completed, approved, or acted on by this memo.** Every checkbox reflects a gap found during read-only inspection, not a task performed.
