# Account Deletion Remediation Contract

**Date:** July 24, 2026
**Purpose:** A safe, testable implementation design for the gaps confirmed in `operating/data-lifecycle-account-deletion-audit-2026-07-24.md`. This document specifies what to build; it does not build it. No app code, migrations, production systems, Supabase, Stripe, or Vercel configuration were changed to produce it.

**How to read this document:** every statement is labeled either **Confirmed fact** (traced to the July 24 audit, itself traced to specific code and schema) or **Proposed design** (this document's own recommendation, not yet approved or built). Where a proposal requires a decision only Christine can make, that is called out explicitly, not silently assumed.

---

## 1. The Post-Cancellation Path: `cancelled_grace_period` → 30-Day Retention → Deletion/Anonymization

**Confirmed fact:** the chain from Stripe's `customer.subscription.deleted` webhook through `cancelled_grace_period`, a `grace_period_ends_at` exactly 30 days out, and the daily `grace-period-check` cron flipping the status to `eligible_for_deletion`, already exists and is correctly timed. Nothing reads `eligible_for_deletion` today.

**Proposed design:** add one new stage after `eligible_for_deletion`, not by extending the existing detection cron to also perform deletion in the same pass, but as a **separate, deliberately delayed execution step**, for two reasons: it keeps detection (a cheap, safe, idempotent status flip) and execution (an irreversible, higher-risk action) as two distinct code paths with two distinct failure modes, and it creates a natural window for a human review or member-initiated abort before anything irreversible happens.

- **New status:** `deletion_scheduled`, set by a second daily cron (`account-deletion-check`, proposed name) that finds every `eligible_for_deletion` profile and, only if a configurable **additional** waiting period has also passed (**proposed default: 3 days**, giving a final buffer beyond the 30-day figure already promised, purely as an internal safety margin, not a change to the member-facing promise), moves it to `deletion_scheduled` and records the exact timestamp the deletion job is allowed to run.
- **A third daily process** (the actual deletion job, Section 7) processes rows in `deletion_scheduled` whose scheduled time has passed, running the full ordered procedure in Section 3.
- **Founder decision required:** whether the first production run of this pipeline should execute automatically end to end, or whether, for at least the first several real deletions, the `deletion_scheduled` stage should instead generate a founder-visible notification requiring an explicit one-click confirmation before the deletion job runs. **Proposed default: require explicit confirmation for the first cohort of real deletions** (a small, fixed number or a fixed time window, whichever Christine prefers), then graduate to fully automatic once the job's behavior is trusted in production, given how much of Section 3's dependent-record handling is new and unverified in a live environment.

---

## 2. Founder Decision Framework: Hard Deletion vs. Limited, Legally Necessary Accounting Records

**Confirmed fact:** `gift_entitlements.recipient_user_id` uses `ON DELETE RESTRICT`, which will block any deletion attempt outright for a member who has redeemed a gift, unless that constraint's underlying row is handled before the `auth.users` deletion is issued.

**Proposed default recommendation: hybrid, not pure hard-delete and not pure retention.**

- **Hard-delete, in full:** Life Graph, uploaded documents (both the Storage object and any DB reference), Guide conversations and messages, all Core tool results, calendar OAuth tokens and imported/synced events, Life Labor data, household connections, and feedback content beyond what's already anonymized by the existing `ON DELETE SET NULL` behavior on `in_app_feedback.user_id`.
- **Retain, narrowly and anonymized:** the minimum financial-ledger fields needed for revenue recognition and accounting, specifically `gift_purchases` and `gift_codes` (which already do not reference the recipient's account at all, only the purchaser's email and the code itself) and `gift_entitlements` with its `recipient_user_id` **nulled**, not deleted, mirroring the pattern `gift_codes.redeemed_user_id` already uses correctly today.

**Reasoning for this default:** a purely anonymized financial record (an amount, a duration, purchase and redemption timestamps, with no live pointer back to a specific member) is the narrowest retention that still satisfies an accounting/audit need, and is a materially different thing from retaining a member's actual personal data. This mirrors the shape of an already-existing, working pattern in the same schema (`gift_codes.redeemed_user_id`), rather than inventing a new retention mechanism.

**This is not this document's decision to finalize.** Whether "anonymized financial ledger row, no personal identifier attached" is an acceptable form of "your data is deleted" is a product and, likely, legal question, not a purely technical one. **Founder decision required**, ideally with a legal review given this touches a specific retention exception to an otherwise stated deletion promise (see Section 9).

---

## 3. Required Deletion Order for Every Identified Dependent Record

**Confirmed fact, restated from the audit:** most tables added since the tracked-migration convention began cascade correctly from `auth.users` already and need no manual handling. The tables listed below either lack a cascade, need special handling per Section 2/4, or are outside the account-record boundary entirely. The order below is **proposed design**, sequenced leaves-first, so that nothing is ever deleted before every table that might reference it has been cleared.

**Phase 0, before anything else touches the database:** enumerate and delete every Storage object associated with the member (Section 5). This happens first specifically so that, if it fails, the account and its database rows remain fully intact and the deletion can be safely retried, rather than deleting database rows first and losing the very information (file paths) needed to find and remove the corresponding Storage objects.

**Phase 1, manual pre-clearance of tables with no cascade or with cross-account meaning (in this order):**
1. `life_labor_task_occurrences` (owned by this user; a connected partner's own occurrences, if any, are untouched since they are keyed to their own `user_id`)
2. `life_labor_tasks`
3. `life_labor_seasons`
4. `life_labor_snapshots`
5. `life_labor_facts`
6. `life_labor_settings`
7. `household_sharing_settings` rows tied to any `household_connections` row where this user is `primary_user_id` or `connected_user_id`
8. `household_connections` rows where this user is `primary_user_id` or `connected_user_id`
9. `gift_entitlements.recipient_user_id` set to `NULL` (not deleted) for any row belonging to this user, per Section 2/4

**Phase 2, tables this audit confirmed already cascade correctly, requiring no manual action, listed here only so the deletion job's own verification step (Section 7) has an explicit checklist to confirm against:** `core_tool_results`, `guide_conversations`, `guide_messages`, `guide_usage_monthly`, `guide_plans`, `calendar_connections`, `calendar_events`, `retirement_plans`, `caregiver_ledgers`, `child_future_contributions`, `net_worth_snapshots`, `payday_checkins`, `user_guide_purchases`, `adult_migration_claims` (parent-side cascade; target-side already nullifies).

**Phase 3, unverified legacy tables:** `life_graph`, `profiles`, `reports`, `documents`, `waitlist`, `conversations`. **Confirmed fact:** these predate the tracked-migration convention and this audit could not read their foreign-key definitions. **Proposed requirement, not optional:** before this entire procedure is built, each of these six tables' actual live foreign-key behavior must be confirmed directly against the Supabase project (Section 9's live-verification plan covers `life_graph`, `profiles`, and `documents` specifically). If any of them turns out not to cascade, it is added to Phase 1's manual-clearance list before the job is considered complete.

**Phase 4, the account record itself:** issue the actual deletion (or, if Section 2's decision lands on anonymize-rather-than-hard-delete for the base account, the equivalent anonymization update) only after Phases 0 through 3 are confirmed complete for this specific user.

**Explicitly out of scope for this procedure, by design, not by oversight:** `life_gaps_submissions` and `waitlist` rows matching the member's email. **Confirmed fact:** these are not linked to a Krovos account via `user_id` and represent a separate consent basis (a lead-magnet quiz or a waitlist signup, not an account). **Proposed, requires a decision:** whether an account-deletion request should, as a matter of product policy, also sweep matching Life Gaps/waitlist rows by email as a courtesy, or whether those remain a truly separate record a member must separately request removal of. See Section 9.

---

## 4. Handling a Redeemed Gift Entitlement Without Permanently Blocking Deletion or Enabling Reuse

**Confirmed fact:** today, `gift_entitlements.recipient_user_id ... on delete restrict` makes deletion impossible for anyone who has redeemed a gift, full stop, with no workaround at the application level, since `RESTRICT` is enforced by Postgres itself before any application code runs.

**Proposed design:**
- Change the underlying constraint (in a future migration, not part of this docs-only session) from `ON DELETE RESTRICT` to `ON DELETE SET NULL` for `gift_entitlements.recipient_user_id`, the same behavior already correctly implemented for `gift_codes.redeemed_user_id` in the same schema.
- **Why this does not corrupt financial records:** every dollar-amount, duration, purchase-linkage, and timestamp field on `gift_entitlements` is untouched by this change; only the pointer to *which specific member* redeemed it is cleared. Revenue recognition depends on `gift_purchases` and `gift_codes`, neither of which references the recipient's account at all today, so this change affects nothing already load-bearing for accounting.
- **Why this does not enable gift-code reuse:** a gift code's "already used" state lives on `gift_codes.used_at` (and, separately, its digest-based identity), not on `gift_entitlements.recipient_user_id`. Nulling the entitlement's recipient reference does not touch `used_at`. A redemption attempt against an already-used code is rejected on that basis regardless of whether the original recipient's account still exists.
- **Sequencing relative to the constraint change:** until that migration ships, Section 3's Phase 1 must include an explicit application-level `UPDATE gift_entitlements SET recipient_user_id = NULL WHERE recipient_user_id = :user_id` step performed by the deletion job itself (a service-role write, not a schema change), so the deletion procedure does not have to wait on a migration to be buildable and testable. Once the constraint itself is changed to `SET NULL`, this explicit step becomes redundant but harmless (idempotent no-op) rather than something that must be removed.

---

## 5. Storage File Removal: Order, Retries, Idempotency, and a Failure Ledger

**Confirmed fact:** no code anywhere in the repository currently removes a Storage object. This section proposes the mechanism from nothing, not a fix to an existing one.

**Proposed sequence:**
1. **Enumerate.** Before any database row is touched, collect every Storage path associated with the member: document paths referenced from `life_graph`/`documents` (exact source columns to be confirmed live, per Section 3 Phase 3's caveat about these unverified legacy tables), and feedback-screenshot paths on `in_app_feedback` (exact column name to be confirmed live; this audit's source material named the bucket, `feedback-screenshots`, but did not capture the specific column). Write this full list into a new **deletion manifest**, one row per path, before deleting anything.
2. **Remove.** For each manifest path, call the Storage removal operation. Record success or failure per path in the manifest, not in aggregate, so a partial failure is identifiable at the individual-file level, not just "something failed."
3. **Retry.** Transient failures (network errors, rate limits) retry automatically with exponential backoff, up to a bounded number of attempts (**proposed: 5**). A path still failing after that many attempts is marked `failed_permanent` in the manifest, not silently dropped.
4. **Idempotency.** Re-running the job against the same deletion request must skip any path already marked `removed` in the manifest and retry only paths still `pending` or `failed_permanent`, so re-invoking the job after a partial run never re-processes work already done and never errors on a path that's already gone (a removal attempt against an already-absent object is treated as success, not failure, for idempotency purposes).
5. **Gate on completion.** The database-deletion phases in Section 3 do not begin until every manifest row is either `removed` or has been explicitly, manually overridden (see below). This is a hard gate, not a best-effort one: **if any Storage path is still `failed_permanent` after retries, the entire deletion request halts before touching any database row**, and an alert fires (Section 7). This ordering exists specifically so the worst-case outcome is "the account is untouched and the request needs attention," never "the database says deleted but a document still sits in Storage."
6. **The failure ledger is the manifest table itself**, not a separate structure: `account_deletion_storage_manifest` (proposed name), storing `deletion_request_id`, `storage_path` (or a hash of it, if even the path itself is considered sensitive enough to avoid retaining, a decision worth making explicitly, see Section 7's audit-trail requirement), `status`, `attempt_count`, `last_attempted_at`, and `last_error` (an error class/code, not a raw provider error string that might embed request content).

---

## 6. Migration Plan for Missing Foreign-Key Cascades

**Confirmed fact:** the Life Labor cluster (six tables) and `household_connections` have no `ON DELETE` clause at all (default `NO ACTION`), meaning a deletion attempt fails outright rather than completing partially. `gift_entitlements` is `RESTRICT` (covered in Section 4).

**Proposed classification, table by table, per the instruction to identify where a constraint change is safe versus where a deliberate service-role procedure is safer:**

- **Safe to add a straightforward `ON DELETE CASCADE`:** `life_labor_settings`, `life_labor_tasks`, `life_labor_seasons`, `life_labor_snapshots`, `life_labor_facts`, `life_labor_task_occurrences`, and `household_connections` (`primary_user_id` and `connected_user_id`). None of these rows have a meaning that outlives the account they belong to in the way a financial ledger entry does; a straight cascade is the correct long-term schema fix for all seven.
- **Why a deliberate service-role procedure must still run before that migration is trusted in production, not instead of it:** several of these tables have **cross-account meaning**, specifically the connected-partner relationship in `household_connections`, `life_labor_tasks`/`life_labor_seasons`/`life_labor_snapshots`/`life_labor_facts` when `household_connection_id` is set and `life_labor_shared`/`calendar_shared` is on. A silent database-level cascade deleting the primary's rows the instant their `auth.users` row is removed would also silently remove data a connected partner has been actively viewing and editing, with no notice to that partner. **Proposed requirement:** the deletion job (Section 7), not the database constraint alone, must check for an active household connection before proceeding, and either (a) surface a notice to the connected partner as part of the deletion flow, or (b) simply accept and document that shared household data disappears for both parties when the primary deletes their account, whichever Christine decides is the correct member experience. **This is the actual founder decision this section exists to flag**: the cascade itself is a safe, uncontroversial schema fix; what a connected partner experiences when it fires is a product decision, not a technical one.
- **Why `gift_entitlements` is handled differently (`SET NULL`, not `CASCADE`):** covered fully in Section 4; a cascade would delete the financial record itself, which is the one thing this whole section is designed to avoid losing.

**Sequencing:** the CASCADE migrations for the seven tables above can ship independently of the deletion job itself, as a pure defense-in-depth improvement (an account deletion attempted through any future path, not only this one, would no longer be blocked), but should not be treated as sufficient on their own without the household-connection notice/handling logic in the deletion job, per the reasoning above.

---

## 7. Deletion Job Design

**Authorization.** **Proposed:** exactly two paths may trigger a deletion request, and no others:
1. **Member-initiated:** an authenticated session, re-confirmed at the moment of request (a re-entered password, or a confirmation link emailed to the account's own address and clicked within a short window), never triggered from a bare authenticated API call alone, given the action's irreversibility.
2. **Admin-initiated:** for the existing manual `privacy@krovos.app` request path, an authenticated founder/admin session performing the action explicitly, logged as `initiated_by: admin` rather than `initiated_by: member` in the audit trail below.

No other trigger (a bare cron, a webhook, an unauthenticated call) should ever be able to start this job.

**Idempotency.** Every request gets one `deletion_request_id` (UUID) at creation. The job is safe to re-run against the same `deletion_request_id` at any point, resuming from the manifest and phase-completion state already recorded, rather than restarting from the beginning or double-processing already-completed phases.

**Audit trail containing no personal content.** **Proposed:** a new table, `account_deletion_log`, storing only: `deletion_request_id`, `initiated_at`, `initiated_by` (`member`/`admin`), `completed_at`, `status` (`pending`/`in_progress`/`completed`/`failed`/`aborted`), and a per-phase row count (integers: how many Life Labor rows removed, how many Storage paths removed, whether the household-connection check found an active connection, as a boolean, not the partner's identity). **Never** the member's email, name, Life Graph content, document content, or any Storage path string in this specific log table (paths belong only in the manifest from Section 5, which is itself proposed to be purged or hashed once a request reaches `completed`, a detail worth finalizing during implementation, not this document).

**Retry behavior.** Transient failures at any phase retry automatically with backoff, matching Section 5's Storage-specific behavior; a genuine, non-transient error (an unexpected foreign-key violation indicating a dependent table this contract didn't anticipate) does not retry blindly, it moves the request to `failed` and alerts.

**Alerts for partial failure.** **Proposed:** any transition to `failed` or `aborted` triggers an explicit, founder-visible alert (at minimum, an internal email; a dedicated admin view is a reasonable future enhancement but not required for a first, safe implementation). A deletion request that reports `completed` must be trustworthy; the entire design in Sections 3 through 6 exists so that "looks done but isn't" is structurally difficult to reach, but the alert on `failed`/`aborted` is the backstop for whatever this contract didn't anticipate.

---

## 8. Live Verification Plan Using a Disposable Test User

**Setup, proposed:** create one fresh, an actually disposable test account (not one of the named personas in `CLAUDE.md`'s Test Accounts table, to avoid disturbing those), and a second disposable account to serve as its household partner. Populate the primary account with: Core data (a completed Life Profile, at least one saved `core_tool_results` row, a `retirement_plans` row, a `net_worth_snapshots` row), Guide history (at least one real exchange in `guide_conversations`/`guide_messages`), calendar tokens/events (a real test-mode Google connection with at least one imported event), Life Labor data (at least one task, one season, one snapshot, one fact, and confirmed-generated `life_labor_task_occurrences` rows), a document (a real Storage upload with its database reference), a screenshot (an `in_app_feedback` submission with an attached screenshot), a household connection to the second disposable account with sharing enabled, a redeemed test-mode gift code, and a full walk of the cancellation path (cancel, reach `cancelled_grace_period`, and either wait for or time-travel `grace_period_ends_at` in a non-production environment to reach `eligible_for_deletion`).

**Pass/fail criteria, per category:**

| Category | Pass criterion | Fail criterion |
|---|---|---|
| Core data | `life_graph`, `core_tool_results`, `retirement_plans`, `net_worth_snapshots`, `payday_checkins` return zero rows for this `user_id` after the job reports `completed` | Any row remains, or the job reports `completed` while any row remains (the specific "false success" failure mode this contract most needs to catch) |
| Guide history | `guide_conversations`, `guide_messages`, `guide_usage_monthly`, `guide_plans` return zero rows | Any row remains |
| Calendar tokens/events | `calendar_connections` and `calendar_events` return zero rows | Any row remains, or an encrypted token is confirmed still capable of authenticating against the provider after the deletion job completes |
| Life Labor | All six tables return zero rows for this user's own data; the connected partner's own Life Labor rows (owned under their own `user_id`) are fully intact and unaffected | Any of this user's rows remain, or any of the partner's rows are unexpectedly affected |
| Documents | The database reference is gone, and a direct Storage check on the known path confirms the object itself is absent, not merely that the reference row is gone | The Storage object still exists under its known path, regardless of database state |
| Screenshots | The Storage object is confirmed absent; the `in_app_feedback` row's `user_id` is null, consistent with its existing `SET NULL` design (unless Section 2's decision changes this specifically, in which case verify against whatever was decided) | The Storage object still exists, or the row's `user_id` still points to the deleted user |
| Household connection | The connection row (and its sharing-settings row) reflects whatever Section 6 decided (removed, or handled with partner notice); the connected partner's own account and data are fully intact when checked independently after the primary's deletion | The partner's own data is affected in any way, or the connection row is left in an inconsistent state (e.g., referencing a `primary_user_id` that no longer exists) |
| Redeemed gift | `gift_entitlements` row survives with `recipient_user_id` null; `gift_codes.used_at` remains set; a fresh redemption attempt against the same code is rejected | The entitlement row is gone (data loss), or the code becomes redeemable again |
| Cancellation/grace state and overall Storage | The account record itself reflects Section 2's chosen end state (deleted or anonymized, whichever was decided); a full scan of every known Storage path prefix for this user returns nothing | Any Storage object remains anywhere under this user's known prefixes, regardless of database state |

**Also required: a deliberate abort-path test.** Inject a forced, temporary failure into the Storage-removal phase (Section 5) for one test run, and confirm: the job halts before any database phase begins, the request is marked `failed`, an alert fires, the account and all its data remain fully intact and reachable exactly as before the attempt, and re-running the same `deletion_request_id` after the injected failure is removed successfully completes the full deletion from where it left off.

---

## 9. Reconciling This Design Against the Current Privacy Policy's 30-Day Promise

**Confirmed fact, exact current wording** (`app/privacy/page.tsx`): "You have the right to access, correct, or delete your personal information at any time. You may export your data or request account deletion by contacting us at privacy@krovos.app. Upon account deletion, your personal data and uploaded documents will be permanently removed within 30 days."

**What this design confirms matches already:** the 30-day timing itself is correct and this contract's proposed additional internal safety margin (Section 1's proposed 3-day buffer before execution) is an internal scheduling detail, not a change to the member-facing 30-day figure; nothing in this contract proposes extending the promised window.

**What this design flags for a decision, rather than silently resolving:**

1. **The gift-entitlement anonymization exception (Section 2/4).** The current promise reads as unconditional ("your personal data... will be permanently removed"). This design's default recommendation retains an anonymized financial ledger row indefinitely. A defensible reading is that a row with no personal identifier attached is no longer "your personal data," but the current privacy-page wording does not say this explicitly. **Decision needed:** either confirm that reading is acceptable as-is, or add an explicit carve-out sentence (e.g., noting that anonymized transaction records may be retained for accounting purposes), which would be a legal/product wording decision for a future session in the `krovos` repo, not something this docs-only session should draft or apply.
2. **Life Gaps and waitlist records tied to the same email but not to the account (Section 3).** A member requesting deletion might reasonably assume this covers "everything tied to my email," but the current design does not touch these separate, pre-account records. **Decision needed:** whether to extend the deletion action to also sweep matching `life_gaps_submissions`/`waitlist` rows as a matter of policy, or to leave them as a truly separate consent basis a member must address separately, and if the latter, whether the privacy page's wording should say so explicitly.
3. **Stripe-held data.** This audit could not verify (and this docs-only contract cannot test, given the no-Stripe-changes constraint) whether any process deletes or anonymizes the Stripe customer object itself. The privacy page already has a working precedent for exactly this kind of third-party-retention caveat, in its existing language about Anthropic's own data-retention policy for API inputs and outputs. **Decision needed:** confirm whether Stripe's own retained data is covered by this deletion process, and if not, consider mirroring that same disclosure pattern for Stripe specifically, rather than leaving the promise silent on it.

**This document does not change the privacy page's wording.** Doing so is out of scope for a docs-only contract about the `krovos` app's implementation, and, per the instruction this contract was built against, any wording change belongs to a future legal/product decision, not to an automatic edit made alongside this design.

---

**Founder decisions this contract requires, collected in one place:**
- Section 1: whether the first cohort of real deletions runs fully automatically or requires explicit one-click confirmation before execution.
- Section 2: whether the hybrid hard-delete-plus-anonymized-financial-record default is acceptable, ideally with legal review, given it is a direct exception to the current unconditional-sounding privacy promise.
- Section 6: what a connected household partner should experience when the primary deletes their account (silent shared-data removal, or an explicit notice/handling step first).
- Section 9, item 1: whether the privacy page's wording needs an explicit carve-out for the anonymized gift-entitlement exception.
- Section 9, item 2: whether account deletion should also sweep matching Life Gaps/waitlist records by email, and whether the privacy page should say so.
- Section 9, item 3: whether Stripe-held data is covered by this process, and whether the privacy page needs a Stripe-specific retention disclosure mirroring its existing Anthropic-specific one.

**No item in this contract has been implemented, tested, or acted on.** Every requirement above is a specification for a future implementation session to build against and verify, not a record of a change already made.
