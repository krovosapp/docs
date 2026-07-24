# Unified Life Calendar Beta Readiness Audit

**Date:** July 24, 2026
**Purpose:** A direct, evidence-based audit of what the Unified Life Calendar actually does today, against what the product promises. This is read-only inspection. No app code, database records, calendar-provider settings, OAuth configuration, or deployed services were changed to produce it.

**Method:** direct inspection of `/Users/christine.smith/krovos` (read only): `app/life-calendar/page.tsx`, `app/components/CalendarConnections.tsx`, `app/api/calendar/connections/route.ts`, `app/api/calendar/oauth/[provider]/start/route.ts`, `app/api/calendar/oauth/[provider]/callback/route.ts`, `app/api/calendar/webhooks/google/route.ts`, `app/api/calendar/webhooks/outlook/route.ts`, `app/api/calendar/import/apple/route.ts`, `app/api/cron/calendar-sync/route.ts`, `app/api/cron/life-labor-occurrences/route.ts`, `app/api/life-labor/occurrences/route.ts`, `lib/calendar-integrations.ts`, `lib/calendar-oauth.ts`, `lib/calendar-crypto.ts`, `lib/calendar-apple.ts`, `lib/calendar-server-user.ts`, `lib/life-labor-occurrences.ts`, `lib/publicPaths.ts`, and the migrations `20260722203000_life_labor_task_source_rls.sql`, `20260722210000_life_labor_task_occurrences.sql`, and `20260723233000_calendar_external_integrations.sql`. Every claim below is either **confirmed** (traced to a specific file and behavior) or explicitly marked as needing **live verification** where code alone cannot settle the question.

---

## 1. One-Time Tasks, Recurring Occurrence Generation, Lazy Top-Up, Archival, Completion/Skipping, History Retention

**Confirmed, from `lib/life-labor-occurrences.ts` and `supabase/migrations/20260722210000_life_labor_task_occurrences.sql`:**

- **One-time tasks** (`frequency = 'once'`) store their single date directly on `life_labor_tasks.occurrence_date` and are never written to `life_labor_task_occurrences`. Completion for these lives on the parent row's `completed_at` column, since a one-time task has no ambiguity about "which occurrence."
- **Recurring tasks** (`daily`, `weekly`, `biweekly`, `monthly`) always keep `occurrence_date` `NULL` on the parent row; every dated instance is a real row in `life_labor_task_occurrences`. `as-needed` tasks generate no occurrences at all, by design (irregular by definition).
- **Recurrence math** (`computeOccurrenceDates()`) uses an explicit, stored `recurrence_anchor` column (day-of-week for weekly/biweekly, day-of-month for monthly, clamped for short months), auto-populated once at insert time by a `BEFORE INSERT` trigger, then treated as independently editable data afterward rather than re-derived from `created_at` on every run. Biweekly recurrence is anchored to the task's creation week so parity doesn't drift as the query range moves. All date arithmetic is UTC-only.
- **Generation is idempotent**: `generateOccurrences()` upserts with `onConflict: 'task_id,occurrence_date', ignoreDuplicates: true`, so the daily cron re-running or a lazy top-up overlapping an already-generated range cannot create duplicate occurrence rows.
- **The daily cron** (`app/api/cron/life-labor-occurrences/route.ts`) maintains an 8-week rolling window (`WINDOW_WEEKS = 8`) for every non-archived recurring task, and is itself fail-closed on authorization: `if (authHeader !== 'Bearer ${process.env.CRON_SECRET}')`, which returns 401 for any mismatch, including an unset secret (an unset `CRON_SECRET` produces the literal string `"Bearer undefined"`, which a real caller cannot supply). **This is a stronger authorization pattern than the one found in the separately-audited email-sequences cron**, which is fail-open when its secret is unset; that finding is not repeated here, but is worth cross-referencing if this pattern is ever used as a template.
- **Lazy top-up is real and wired end to end**, not merely present as unused code: `app/life-calendar/page.tsx` calls `POST /api/life-labor/occurrences` with the currently viewed month's date range every time the calendar renders a month, before querying occurrences for that range. That endpoint discovers eligible tasks using the requesting member's own JWT against Supabase (so RLS, not application logic, decides which tasks a connected partner can even see), then calls the same shared `generateOccurrences()` function via a service-role client to write occurrence rows. This means a read-only connected partner can trigger materialization for a future month without gaining occurrence write permission, exactly matching the architecture described in the canonical current-state record. Requests are capped at 93 days to prevent abuse.
- **Archival is confirmed soft-delete, not row deletion**: `life_labor_tasks.archived_at` marks a task inactive without removing it, "so its occurrence history stays valid" (migration comment). The migration explicitly documents that archiving a task is expected to delete only its still-pending occurrence rows at the application level (not via cascade), leaving completed/skipped occurrences intact as real history. **This audit did not trace the specific application code that performs that targeted pending-row deletion on archive**; the schema and its documented intent are confirmed, but the exact archive action's implementation was not separately read line by line.
- **Completion/skipping**: each occurrence row has its own `status` (`pending`/`completed`/`skipped`) and `completed_at`, independent of sibling occurrences of the same task. `app/life-calendar/page.tsx` writes to this via a direct `life_labor_task_occurrences` update (confirmed at the line that sets `completed_at` conditionally on `status === 'completed'`).
- **History retention**: `life_labor_task_occurrences` has `ON DELETE CASCADE` from its parent task, which the table comment explicitly says is intentional only for a genuine hard purge, not the everyday archive path. Given archiving is soft-delete, occurrence history for an archived task is retained indefinitely under normal use.

---

## 2. Google Calendar Connection, Import/Sync, Token Handling, Provider Failure States, Disconnect Behavior, Data Shown/Editable

**Confirmed, from `lib/calendar-integrations.ts`, `lib/calendar-crypto.ts`, and the OAuth start/callback routes:**

- **Two distinct connection modes** exist per provider: `personal_import` (one-time, `calendarWindow()` pulls -12 months to +18 months, then the connection's tokens are explicitly nulled out after the sync completes: `encrypted_access_token: ... 'personal_import' ? null : ...`) and `work_sync` (ongoing, -90 days to +12 months, tokens retained encrypted for continued refresh). This matches the product's stated one-time-import-versus-ongoing-read-only-feed design precisely, and confirms credentials are not retained after a personal import, not merely described that way in copy.
- **Token encryption**: `lib/calendar-crypto.ts` uses AES-256-GCM with a random 12-byte IV per encryption and a verified authentication tag on decrypt; the encryption key is derived via SHA-256 from `CALENDAR_TOKEN_ENCRYPTION_KEY`. Tokens are never selected or returned by any route in plaintext; `app/api/calendar/connections/route.ts`'s `GET` explicitly selects only `id, provider, connection_mode, account_email, provider_calendar_id, status, last_synced_at, last_error`, never the encrypted token columns, and `calendar_connections` grants `ALL` only to `service_role`, not `authenticated`, at the table level (confirmed in the migration).
- **OAuth flow**: `start` requires authentication, validates provider/mode against an allowlist (`google`/`outlook`, `personal_import`/`work_sync`), and sets a short-lived (10-minute), `httpOnly` state cookie. `callback` validates the returned state against that cookie before exchanging the code, redirecting unauthenticated visitors to sign-in and any error to `/life-calendar?calendar_connection=error`.
- **Token refresh and expiry**: `refreshCalendarAccessToken()` refreshes with a 5-minute buffer before expiry; on a failed refresh (e.g., a revoked or expired refresh token), it updates the connection to `status: 'reauthorization_required'` with a `last_error` message before throwing, rather than failing silently.
- **Provider failure states surfaced to the member**: `app/components/CalendarConnections.tsx` renders each `work_sync` connection's `status` (human-readable, underscores replaced with spaces) and `last_error` text directly in the UI. **A confirmed gap**: the only action offered for a connection in any state, including `reauthorization_required`, is "Disconnect." There is no distinct "Reconnect" or "Re-authorize" affordance; a member must disconnect and start a fresh "Connect Google/Outlook" flow, which will upsert onto the same connection row (unique on `user_id, provider, connection_mode, provider_calendar_id`), but this path is not labeled or explained as a repair action anywhere in the UI copy.
- **Disconnect behavior differs correctly by mode**: `DELETE /api/calendar/connections` deletes every `calendar_events` row tied to that `external_connection_id` **only when `connection_mode === 'work_sync'`**. A `personal_import` connection's already-imported events are correctly left in place on disconnect, since they became independent, editable Krovos data at import time, not an ongoing pointer to the provider.
- **Webhook renewal**: the `calendar-sync` cron renews a Google watch or Outlook subscription proactively when its `webhook_expires_at` is within 36 hours, and is itself fail-closed on its own authorization check (`if (!process.env.CRON_SECRET || authorization !== ...)`), which correctly refuses to run at all when the secret is unset, unlike the pattern found in the separately-audited email-sequences cron.
- **Sync failure handling**: the cron catches a per-connection sync error, truncates it to 500 characters, writes it to `last_error`, and continues processing the remaining connections rather than aborting the whole run.
- **What data is shown and editable**: imported events (`layer: 'personal'`, `read_only: false`) are fully editable Krovos events after import. Ongoing work-synced events (`layer: 'work'`, `read_only: true`, enforced by a table-level `CHECK` constraint) are display-only; Krovos "never creates, edits, or deletes anything in your work calendar," matching the code, which only ever reads from the provider for `work_sync` and never writes back to it.
- **Duplicated-event risk**: import-mode sync dedups by an identity key (`external_calendar_id | external_event_id | external_occurrence_id`) before inserting, and a unique index (`calendar_events_external_identity_idx`) enforces this at the database level for any row with a connection ID. Work-sync mode avoids duplicates by fully deleting and re-inserting that connection's events on every sync, rather than diffing. **Confirmed: the code structurally protects against duplicate events under normal operation** for both modes.

---

## 3. Household/Partner Calendar Sharing and Editing (Exact RLS Behavior)

**Confirmed, from `supabase/migrations/20260723233000_calendar_external_integrations.sql` and `20260722210000_life_labor_task_occurrences.sql`.** Both `calendar_events` and `life_labor_task_occurrences` implement the same pattern:

| Layer | Connected-partner read | Connected-partner write |
|---|---|---|
| `household` | Requires `household_sharing_settings.life_labor_shared = true` | Additionally requires `life_labor_edit_access = true` |
| `personal` | Requires `calendar_shared = true` | Same single toggle, `calendar_shared = true` (no separate edit-access tier exists for personal items, confirmed against the actual `household_sharing_settings` columns referenced in these policies) |
| `work` | **Never.** No policy branch includes `layer = 'work'` at all, and a table-level `CHECK` constraint forces `read_only = true` whenever `layer = 'work'`. | **Never.** Same reasoning. |
| `guide_linked` | **Never.** No policy branch includes this layer either. | **Never.** |

**A confirmed, specific directionality finding, not previously documented as such anywhere this audit reviewed:** every connected-access policy on both tables is written in one direction only, `hc.connected_user_id = auth.uid() AND hc.primary_user_id = calendar_events.user_id` (or the equivalent for `life_labor_tasks`/`life_labor_task_occurrences`). This grants the **connected party** visibility into the **primary's** shared household/personal rows. There is no symmetric policy granting the primary read or write access into the connected party's own personal calendar rows. This is consistent with the household task model being a single canonical list owned by the primary (which the canonical record already describes for Life Labor specifically: "resolves a canonical row, the primary user's user_id, when connected and shared"), so for `household`-layer rows this is very likely intentional, not a gap. For `personal`-layer calendar rows specifically, this means the "one family planning calendar" experience is, as implemented, **the primary's personal calendar made visible and editable by the connected partner**, not a true two-way merge where each partner's own personal items flow into a shared view the other person also sees. Whether this one-directional behavior is the intended final design for personal calendar sharing, as opposed to household sharing, was not confirmed by any product-decision document this audit had access to, and should be explicitly confirmed with a founder decision rather than assumed either way before beta.

---

## 4. Do Life Labor, Life Goals, and Family-Planning Calendar Items Reach the Calendar as Designed?

**Life Labor: confirmed yes, fully wired.** `app/life-calendar/page.tsx` queries `life_labor_tasks` and `life_labor_task_occurrences` directly (with an inner join back to the parent task for `task_source`), and renders them using dedicated event types (`task_household`, `task_personal`, `task_work`, `task_guide_linked`) with distinct colors and icons.

**Guide-linked items: confirmed displayable, but never populated.** The calendar's type system fully supports `task_guide_linked` and `calendar_guide_linked` as first-class categories, each with its own icon, color, and label ("Guide actions" / "Guide events"). A repository-wide search for the string `guide_linked` found it used **only inside `app/life-calendar/page.tsx` itself**; no guide, tool, or Guide-personalization code anywhere in the codebase ever creates a `life_labor_tasks` row with `task_source = 'guide_linked'` or a `calendar_events` row with `layer = 'guide_linked'`. **This is a confirmed, specific mismatch**: the calendar is fully ready to display guide-linked action items, and nothing in the product currently produces one.

**Life Goals: confirmed absent.** A repository-wide search for `calendar_events` or `life_labor_tasks` inside `app/life-goals` and `app/api/life-goals` found no matches at all. Life Goals never writes to either table. A Life Goal's target date (a home-purchase goal, a wedding fund goal, an education goal) does not appear on the Unified Life Calendar through any database-backed path. The calendar's own event-type union even declares a `'goal'` category with a color/icon mapping, but a search of `app/life-calendar/page.tsx` found no code path that ever assigns `type: 'goal'` to an actual event; the category is declared and styled, but unused.

**Family-planning items: confirmed partially reached, via a separate, non-persistent mechanism.** `buildEventsFromLifeGraph()` in `app/life-calendar/page.tsx` computes payday dates, mortgage and debt due-dates, children's birthdays, a kindergarten-start milestone (age 5), a college-age milestone (age 18), and three fixed annual reminders (tax deadline, year-end review, open enrollment window), entirely client-side, from the currently signed-in member's own `financial`/`family` Life Graph data, on every page load. **None of this is stored as `calendar_events` or any other row**; it is regenerated fresh each time, which keeps it current but also means it cannot be independently edited, dismissed, or shared as a discrete record, and it reflects the *viewing* member's own Life Graph, not necessarily a connected primary's, when a connected partner views the calendar. This is a real, working mechanism, but it is architecturally distinct from, and much narrower than, "family-planning calendar items reach the calendar as designed" would suggest if read as implying a persistent, shareable record; it currently covers birthdays and two fixed age-based milestones derived only from a child's date of birth, not any goal, plan, or milestone the member has actually set.

---

## 5. Empty, Connected, Disconnected, Expired-Auth, Sync-Failed, Duplicated-Event, and Mobile States

- **Empty state**: not separately traced line by line in this audit beyond confirming the page renders whatever `buildEventsFromLifeGraph()` and the database queries return; a member with no external connections, no Life Labor tasks, and a sparse Life Graph would see only the fixed annual reminders (tax deadline, year-end review, open enrollment) and any paydays inferred from income data. **Needs live verification** to confirm this reads as an intentional, well-labeled empty state rather than a sparse, unexplained one.
- **Connected state**: confirmed. `CalendarConnections.tsx` lists each `work_sync` connection with provider, account email, and status; a successful import or connection sets a visible confirmation message ("`{n}` events imported into Krovos" / "Your read-only work calendar is connected") and expands the panel.
- **Disconnected state**: confirmed. Disconnecting removes the connection row (and, for `work_sync`, its associated events) and the panel reflects the updated connection list on next load.
- **Expired-auth state**: confirmed surfaced as a `reauthorization_required` status string plus `last_error` text, but confirmed **not** paired with a labeled repair action (Section 2).
- **Sync-failed state**: confirmed surfaced via `last_error` on the connection, updated by the cron on any per-connection failure without aborting the rest of the run.
- **Duplicated-event state**: confirmed structurally prevented by identity-based deduplication (import mode) and full replace-on-sync (work mode); see Section 2.
- **Mobile state**: the canonical current-state record documents a prior fix ("closed Life Calendar's phone overflow") and this audit confirmed `CalendarConnections.tsx` uses a responsive grid (`grid gap-4 md:grid-cols-2`, single column below the `md` breakpoint). **This audit did not render the page in a browser and cannot confirm current mobile behavior beyond the presence of responsive classes; live verification at phone width is required before this can be marked confirmed-safe.**

---

## 6. Privacy Boundaries: What a Partner Can Read/Edit, What Never Crosses Accounts

**Confirmed, from the RLS policies in Section 3:**
- A connected partner can read and, if `life_labor_edit_access` is also on, write the primary's `household`-layer tasks and occurrences.
- A connected partner can read and write the primary's `personal`-layer calendar items and task occurrences, gated on the single `calendar_shared` toggle, matching the documented "collaborative, not read-only" correction for personal/family items.
- A connected partner can **never** see or touch a `work`-layer or `guide_linked`-layer row, under any combination of sharing settings, because no policy branch grants it and (for `work`) a table constraint forces `read_only = true` regardless.
- OAuth tokens, encrypted or not, are never exposed through any API route to any member, primary or connected; only `service_role` can read the encrypted columns directly, and every client-facing route selects an explicit, narrow column list.
- The Apple app-specific password is confirmed never persisted anywhere; it exists only as a request-body value passed directly into a live CalDAV request and is not written to any table, logged, or cached.

**What this audit could not verify**: whether the one-directional sharing pattern in Section 3 (only the connected party sees into the primary, never the reverse) is the deliberate final design for `personal`-layer sharing specifically, as opposed to an artifact of building `household`-layer sharing first and extending the same pattern. This is a founder-decision question, not a code-correctness one.

---

## 7. Mismatches Between Product Promises and Real Implementation

1. **Guide-linked calendar items are promised by the UI's own category system but never produced anywhere** (Section 4). This is the clearest, most specific mismatch found in this audit.
2. **Life Goals target dates do not reach the calendar at all**, despite a `'goal'` event category existing in the type system with no code path that ever uses it (Section 4).
3. **"One family planning calendar" reads, in the actual RLS, as one direction of sharing** (the primary's calendar made visible to the connected partner), not a symmetric two-way merge of both partners' personal calendars (Section 3). Whether this matches the intended promise depends on how that promise was meant to be read, which this audit cannot settle from code alone.
4. **Family-planning milestones on the calendar are narrower than the phrase implies**: only birthdays and two fixed age-based milestones (kindergarten, college) derived purely from a child's date of birth, computed fresh client-side rather than stored, editable, or shareable as discrete records (Section 4).
5. **No labeled "reconnect" action exists for an expired connection**; a member sees the problem (`last_error`, a "reauthorization required" status) but the only offered action is full disconnect, not a direct repair path (Section 2).

---

## 8. Prioritized Beta-Readiness Checklist

### Real gaps (code-confirmed, require a product or implementation decision)

1. **Decide and either implement or explicitly de-scope guide-linked calendar items before beta.** The category is fully built on the display side; either wire a real producer (at minimum, a guide action item with a due date) or remove the unused category so the calendar doesn't advertise a capability nothing uses.
2. **Decide whether Life Goals should reach the calendar**, and if so, build that path; today there is none, despite a declared, unused `'goal'` type.
3. **Confirm, as an explicit founder decision, whether one-directional personal-calendar sharing (connected party sees the primary, not the reverse) is the intended design**, before representing "one shared family calendar" as symmetric in any member-facing copy.
4. **Add a labeled reconnect/re-authorize action** for a connection in `reauthorization_required` status, rather than requiring disconnect-then-reconnect with no explanation that this is the repair path.
5. **Clarify or expand what "family-planning calendar items" means in product copy** to match what's actually built (birthdays and two fixed age-based milestones), or build the richer version if that's the intended promise.

### Confirmed-safe behavior (do not need further work to be beta-ready as-is)

6. Recurring-occurrence generation is idempotent, timezone-correct (UTC-only), and correctly shared between the daily cron and the lazy top-up path.
7. Both calendar-related crons (`calendar-sync`, `life-labor-occurrences`) are fail-closed on their own authorization checks, unlike the separately-audited email-sequences cron.
8. OAuth tokens are encrypted at rest with authenticated encryption, never returned to any client, and personal-import tokens are explicitly discarded after the one-time sync.
9. Work-calendar events are structurally read-only (table constraint plus RLS), and Krovos never writes back to a connected work calendar.
10. Disconnect behavior correctly distinguishes purging work-synced events from preserving already-imported personal events.
11. The Apple app-specific password is never persisted, matching its own disclosed copy exactly.
12. Duplicate-event protection is structural (a unique index plus delete-and-replace), not merely assumed.
13. `work` and `guide_linked` layers are RLS-hard-blocked from ever reaching a connected partner, regardless of any sharing toggle combination.

### Documentation-only gaps (no code change needed, only a written clarification or a follow-up verification task)

14. **Confirm live, at phone width, that the calendar's mobile layout matches the "phone overflow" fix already recorded**; this audit found responsive classes present but did not render the page to confirm current behavior.
15. **Trace the specific application code that deletes a task's pending occurrence rows on archive**, to move that claim from "documented schema intent, confirmed" to "confirmed executed code path," since this audit verified the schema and its comments but did not separately read that exact deletion logic.
16. **Note for a future cross-check, not corrected here**: this audit's method of checking `lib/publicPaths.ts` in full (rather than grepping only for a specific route name) surfaced that `pathname.startsWith('/api')` is a blanket public-path rule, meaning `proxy.ts`'s subscription middleware never runs on any API route, calendar-related or otherwise; each API route must and generally does enforce its own authentication internally (confirmed here for every calendar route). This is noted because a prior, separately-produced audit of a different Krovos feature stated that middleware protects an API route in a way this broader rule would contradict; that other document was not re-opened or corrected as part of this audit, since this audit's scope was the Life Calendar only, but it is worth a direct follow-up check before treating that other claim as settled.

**No item in this checklist was acted on by this audit.** Every finding reflects read-only inspection of the code as committed, not a change made during this review.
