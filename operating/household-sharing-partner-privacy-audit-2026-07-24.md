# Household Sharing and Partner Privacy Audit

**Date:** July 24, 2026
**Purpose:** A direct, evidence-based audit of every cross-account path between a primary Krovos subscriber and a connected household partner. This is read-only inspection. No app code, migrations, Supabase, Vercel, Stripe, or production settings were changed to produce it.

**Method:** direct inspection of `/Users/christine.smith/krovos` (read only): `supabase/migrations/20260712_household_connections.sql`, `20260715_life_labor.sql`, `20260715b_life_labor_household_keying.sql`, `20260722203000_life_labor_task_source_rls.sql`, `20260722204000_shared_personal_calendar_task_visibility.sql`, `20260722205000_shared_personal_calendar_edit_access.sql`, `20260722210000_life_labor_task_occurrences.sql`, `20260723233000_calendar_external_integrations.sql`; every route under `app/api/household/`; `app/api/krovos-guide/route.ts`; `app/api/gifts/redeem/route.ts`; `app/api/adult-migration/claims/route.ts`; `app/settings/page.tsx`; and `lib/authenticated-service-request.ts`. Cross-referenced against `operating/api-authorization-exposure-audit-2026-07-24.md`, `operating/data-lifecycle-account-deletion-audit-2026-07-24.md`, and `product/life-calendar-beta-readiness-audit-2026-07-24.md` (all this repo). Every finding is labeled **confirmed** or **needs live verification**.

**A note on currency:** this audit found that `app/api/life-gaps/prefill/route.ts` and the `send-sequences` cron, both flagged as active gaps in `operating/api-authorization-exposure-audit-2026-07-24.md`, now match the fixes recommended in `operating/api-access-control-remediation-contract-2026-07-24.md`, and that a new `authenticateSubscribedRequest()` helper now exists and is in use on several routes, including `household/goals`. The codebase has moved since those two documents were written; this audit reflects what it found today, not what those earlier documents describe, and does not re-verify the rest of the API surface, since that is outside this audit's household-sharing scope.

---

## 1. What a Primary Member Can See/Edit From a Connected Partner

**Confirmed:** a primary has full administrative control over the connection itself (`hc_primary_all`, `for all using (auth.uid() = primary_user_id)`), including inviting, revoking, and reconnecting. A primary can read and set the sharing toggles for the connection (`app/settings/page.tsx` renders `calendar_shared`, `life_labor_shared`, `life_labor_edit_access`, and `financial_shared` as controls). **Confirmed, and this is the audit's first specific one-directional finding:** the RLS policies granting cross-account visibility into `calendar_events` and every `life_labor_*` table are written in exactly one direction, `connected_user_id = auth.uid() AND primary_user_id = <owner column>`, meaning **these specific policies never grant the primary read or write access into a connected partner's own personal calendar or Life Labor rows**, only the reverse. A primary does not, through these tables, see anything the connected partner personally owns.

**Confirmed exception, and this is bidirectional by design:** `app/api/household/partner-data/route.ts` computes the "partner" as whichever side of the connection the caller is *not*, `conn.primary_user_id === user.id ? conn.connected_user_id : conn.primary_user_id`, then reads that party's `life_graph` (`family`, `financial`, `household`, `goals`) gated on `financial_shared`. **This means a primary can read a connected partner's own Life Graph data through this specific route, provided `financial_shared` is on**, which is a materially different (and bidirectional) sharing model than the calendar/Life Labor tables use.

---

## 2. What a Connected Partner Can See/Edit From the Primary

**Confirmed, from the RLS policies directly:**

| Data | Read | Write |
|---|---|---|
| Household-layer calendar events and Life Labor tasks/occurrences/seasons/snapshots/facts | Requires `life_labor_shared = true` | Additionally requires `life_labor_edit_access = true` |
| Personal-layer calendar events and their task occurrences | Requires `calendar_shared = true` | Same single toggle, `calendar_shared = true`, no separate edit tier exists |
| Work-layer and guide-linked calendar events | **Never**, under any toggle combination (a table-level `CHECK` constraint forces `read_only = true` on `work`, and no RLS policy branch includes either layer at all) | **Never** |
| Life Graph (`family`, `financial`, `household`, `goals`), via `household/partner-data` | Requires `financial_shared = true` | Not applicable, this route is read-only |
| Household-scoped Life Goals, via `household/goals` | N/A (goals travel with the Life Graph read above) | Requires **both** `financial_shared = true` **and** `financial_edit_access = true`; scoped strictly to goals with `scope === 'household'`, individual goals are untouched regardless of these toggles |
| Guide-purchase inheritance, via `household/guide-access` | Automatic for an active connection, no toggle gates it | Not applicable, purchases aren't editable |

**Confirmed, and directly relevant to the "shared household" expectation:** the connected partner's cross-account visibility into calendar and Life Labor data is the mirror image of Section 1's finding, granted only in the connected-party-reads-primary direction, correctly scoped per-toggle, and confirmed by explicit `EXISTS` subqueries joining `household_connections` and `household_sharing_settings` on every policy reviewed, not by a broader table-level grant.

---

## 3. Per-Data-Type Cross-Account Behavior

| Data type | Cross-account path exists? | Toggle(s) | Directionality | Confirmed/Needs verification |
|---|---|---|---|---|
| **Calendar events (personal layer)** | Yes | `calendar_shared` | One-directional (connected reads/writes primary's) | Confirmed |
| **Calendar events (household layer)** | Yes | `life_labor_shared` (+ `life_labor_edit_access` for write) | One-directional | Confirmed |
| **Calendar events (work/guide-linked layers)** | No, structurally blocked | N/A | N/A | Confirmed |
| **Life Labor tasks, seasons, snapshots, facts, occurrences** | Yes | `life_labor_shared` (+ `life_labor_edit_access` for write) | One-directional | Confirmed |
| **Goals (household-scoped)** | Yes | `financial_shared` + `financial_edit_access` for write | Read: bidirectional via `partner-data`; write: connected-party-to-primary only, via `household/goals` | Confirmed |
| **Finances (Life Graph `financial`/`household`/`family`)** | Yes, read-only | `financial_shared` | Bidirectional, via `partner-data` | Confirmed |
| **Life Graph as a whole (beyond the `partner-data` route's specific field selection)** | No broader path found | N/A | N/A | Confirmed no other route or policy grants a fuller cross-account Life Graph read |
| **Krovos Guide context sent to Anthropic** | **No.** The Guide's own route queries `life_graph` filtered strictly to `.eq('user_id', userId)`, the caller's own row; no join, merge, or fetch of a connected partner's data exists anywhere in that route, regardless of any sharing toggle | N/A | N/A | Confirmed, see Section 7 |
| **Uploaded documents** | No cross-account path found anywhere in the codebase | N/A | N/A | Confirmed by absence |
| **In-app feedback** | No. RLS on `in_app_feedback` is strictly `auth.uid() = user_id` for both insert and select; no household-connection-aware policy exists on this table | N/A | N/A | Confirmed |
| **Gifts (`gift_purchases`, `gift_codes`, `gift_entitlements`)** | No household-connection-based sharing mechanism touches these tables at all; a redeemed gift belongs to the individual who redeemed it, never automatically extended to a connected partner | N/A | N/A | Confirmed |
| **Adult-migration claims** | A separate parent-to-dependent relationship model entirely, keyed on `source_parent_user_id`/`target_user_id`, with no `household_connection_id` column and no interaction with the household-connections system found | N/A | N/A | Confirmed, the two systems do not intersect |
| **Deletion effects on a connected partner** | Indirect. Per `operating/data-lifecycle-account-deletion-audit-2026-07-24.md`, `household_connections` has no cascade from `auth.users`, so a primary's deletion attempt fails outright today rather than silently affecting the partner. If that cascade is added (as that audit's companion contract recommends), a primary's deletion would remove the connection row, which would then structurally cut off the partner's RLS-granted visibility into what were the primary's shared items, but nothing in the codebase updates the connected partner's own `subscription_status` (which depends on `connected_user` remaining valid) as part of that chain, the way `household/revoke` explicitly does today | N/A | Needs a decision, see Section 8 |

---

## 4. Is All Sharing Opt-In, Correctly Scoped, and Revocable?

**Confirmed: sharing is not uniformly opt-in.** `supabase/migrations/20260712_household_connections.sql` defines `household_sharing_settings` with `calendar_shared boolean not null default true` and `fair_play_shared boolean not null default true` (later renamed to `life_labor_shared` in `20260715_life_labor.sql`, retaining its default), while `financial_shared` and `financial_edit_access` both default `false`. `app/api/household/accept/route.ts` **explicitly inserts** these same values at the moment a connection becomes active (`calendar_shared: true, life_labor_shared: true, life_labor_edit_access: true, financial_shared: false`), confirming this is a deliberate application-level choice, not an unnoticed schema default. `20260715_life_labor.sql` separately adds `life_labor_edit_access boolean not null default true`.

**What this means concretely:** the instant a household connection is accepted, the connected partner has full read **and write** access to the primary's personal calendar and household Life Labor data, with no additional action required from the primary beyond having sent and had the invite accepted. Financial data sharing is correctly opt-in and requires the primary to separately enable it afterward in Settings. **This is a real, confirmed asymmetry**, not a hypothetical one: two of the three sharing categories are opt-out, one is opt-in.

**Is this indefensible?** Not necessarily. A reasonable product argument exists for treating an accepted household invitation as implied consent for the collaborative surfaces (a shared calendar, shared chores) while still requiring an explicit extra step for financial specifics, which is a materially more sensitive category. **This audit does not resolve which framing is correct; it confirms the current behavior precisely so that whichever framing is intended can be a deliberate decision, not an accidental one.** See Section 8 for the specific decision this contract needs.

**Scoping: confirmed correct**, on every policy and route reviewed. No cross-account read or write grants more than its specific named toggle claims to grant; the `work`/`guide_linked` layers and individual (non-household-scoped) goals are correctly excluded from every sharing path found.

**Revocability: confirmed correct and immediate.** `app/api/household/revoke/route.ts` sets the connection to `revoked`, which every RLS policy's `hc.status = 'active'` condition immediately excludes from matching, cutting off all cross-account access in the same instant, and separately downgrades the connected party's own `subscription_status`. `household/reconnect` correctly re-derives identity from the caller's own session rather than a client-supplied target.

---

## 5. One-Directional Behaviors That Could Conflict With the "Shared Household" Expectation

**Confirmed, restated with full precision from Sections 1-3:** the calendar and Life Labor sharing model is asymmetric by construction. Only a **connected** party can see into a **primary's** shared rows; there is no policy anywhere granting the reverse. Concretely: if both members of a household are meant to experience "one shared family calendar," what's actually built is "the primary's calendar, made visible and, for personal items, editable by the connected partner," not a merge of both people's own personal calendars into a single shared view each can see. A connected partner's own personal calendar entries (if they create any under their own account) are not shared back to the primary through any mechanism this audit found.

**This is the single most important product-experience question this audit surfaces**, because it is easy to build correctly at the RLS level (which this codebase has done) while still not matching what "shared household calendar" means to an actual couple, where either person might reasonably expect to add something and have their partner see it. **This needs a founder decision, not a code fix**, because the correct fix depends entirely on which experience is intended (see Section 8).

---

## 6. Client-Supplied Identity vs. Authenticated-Derived Identity

**Confirmed clean across every household-sharing route reviewed.** `household/revoke` accepts a client-supplied `connectionId` but explicitly checks `conn.primary_user_id !== user.id` before acting, returning 403 otherwise. `household/partner-data` and `household/guide-access` derive the partner's ID from the caller's own verified, active connection row, never from a client-supplied identifier. `household/goals` derives `connection.primary_user_id` the same way, and additionally re-verifies both required toggles before permitting a write, using `authenticateSubscribedRequest()` (the subscription-aware helper) rather than the plain session-only one. `household/reconnect` derives `user.id` from the session and uses it directly as `primary_user_id` on the new connection row, never accepting one as input. **No route in this household-sharing surface trusts a client-supplied identity for any authorization decision.** This is a genuinely strong, confirmed-safe finding.

---

## 7. Partner Privacy: Anthropic Context and Account Deletion

**Anthropic context: confirmed airtight.** `app/api/krovos-guide/route.ts` fetches `life_graph` with `.eq('user_id', userId).single()`, `userId` being the authenticated caller's own ID, with no join, union, or secondary fetch of a connected partner's data anywhere in that route. This holds **regardless of `financial_shared` or any other sharing toggle**, since the Guide route never consults `household_connections` or `household_sharing_settings` at all. A connected partner's financial details, even when fully shared for in-app purposes via `partner-data`, are never included in what either party's Krovos Guide sends to Anthropic. This is one of the strongest confirmed-safe findings in this audit.

**Account deletion: confirmed incomplete, compounding a gap already identified elsewhere.** Per `operating/data-lifecycle-account-deletion-audit-2026-07-24.md`, `household_connections` currently has no cascade from `auth.users`, which today means a deletion attempt for either party fails outright rather than silently affecting the other, an accidental but real protection against a silent partner-privacy surprise. If that table is given a cascade (as the companion remediation contract recommends, for the correct and separate reason of making deletion possible at all), the deletion job must explicitly handle what happens to the connected partner's access and awareness, not rely on the cascade alone, since a bare cascade would silently and immediately end the partner's access to shared calendar/Life Labor data with no notice, the same open question already flagged in that contract's Section 6, now confirmed relevant from the partner-privacy angle specifically as well as the deletion-mechanics angle.

---

## 8. Categorized Findings

### Confirmed-safe behavior
- No route trusts a client-supplied identity for any cross-account authorization decision (Section 6).
- Krovos Guide context is fully isolated per user, never including a connected partner's data under any sharing configuration (Section 7).
- Uploaded documents and in-app feedback have no cross-account access path at all.
- Gifts and adult-migration claims are entirely separate systems from household sharing, with no unintended interaction found.
- Revocation is immediate and correctly cuts off every RLS-gated cross-account path in the same instant, and correctly downgrades the connected party's own access.
- Sharing scope is correctly bounded everywhere it exists: `work`/`guide_linked` calendar layers and individual (non-household) goals are never reachable across accounts, regardless of toggle state.
- `household/goals` is a well-built, correctly-scoped, audited (`life_graph_change_log`) cross-account write path with concurrency protection.

### Confirmed gaps
- **Calendar and Life Labor sharing default to on (opt-out), not opt-in**, the moment a household connection is accepted, while financial sharing correctly defaults off (Section 4). This is a confirmed behavior, not a hypothesis; whether it is also a *problem* depends on the founder decision below.
- **Cross-account visibility is one-directional for calendar and Life Labor data** (connected-party-reads-primary only), which may not match a "shared household calendar" expectation for either partner to add something and have the other see it (Section 5).
- **A connected partner's own `subscription_status` is not updated if the primary's account is ever deleted**, unlike the explicit downgrade `household/revoke` performs today; this is a real gap once account deletion itself is built, not yet exploitable since deletion doesn't currently work at all (Section 7).

### Decisions needing founder approval
1. **Should calendar and Life Labor sharing remain opt-out by default at connection acceptance, or should the primary be shown and asked to explicitly confirm these toggles (or a summary of them) before or immediately after inviting a partner?** The current default is defensible as implied consent for collaborative surfaces, but it is a decision this audit found no evidence was made deliberately at the "what does the primary see and confirm" level, as opposed to the underlying database default, which clearly was deliberate.
2. **Should personal-calendar sharing become bidirectional** (both partners' own personal items visible to each other when `calendar_shared` is on), **matching the "one shared family calendar" framing**, or is the current "primary's calendar, partner reads and edits it" model the intended design? This is the single highest-leverage product decision in this audit, since it determines whether Section 5's finding is a bug or a feature.
3. **What should happen to a connected partner's access and notification when the primary's account is deleted?** A silent, immediate loss of access (matching a plain cascade) versus an explicit notice, and whether the partner's `subscription_status` should be programmatically downgraded the same way `household/revoke` already does, need a decision before the account-deletion work in the companion remediation contract is built.

### Live-verification-only items
- Whether the Settings page's sharing-toggle display (Section 4) is shown to the primary proactively at invite time, or only discoverable by later visiting Settings; this audit confirmed the toggles are readable and settable there, but did not trace whether the invite flow itself surfaces them before or immediately after sending an invite.
- Whether a connected partner who creates their own personal calendar events (under their own `user_id`) currently has any UI affordance suggesting those are "shared" when they structurally are not, which would be a documentation/UI-clarity gap distinct from the RLS finding itself.

---

## 9. Implementation-Ready Remediation Backlog

Ranked by dependency and product impact, not raw severity, since item 1 is a decision every other item in this backlog depends on.

1. **Resolve the bidirectional-vs-one-directional calendar/personal-sharing decision (Section 8, decision 2).** *Blocking, decision required first.* **Acceptance criteria:** a written decision exists stating whether personal-calendar sharing becomes bidirectional; if yes, proceed to item 2; if no, proceed to item 3 instead.
2. **If bidirectional is chosen: add the mirrored RLS policies** (a `primary_user_id = auth.uid() AND connected_user_id = <owner>` branch alongside the existing direction) for `calendar_events` where `layer = 'personal'` and its task-occurrence equivalent, gated on the same `calendar_shared` toggle. **Acceptance criteria:** a connected partner's own personal calendar events become visible to and editable by the primary under the identical toggle rule already governing the reverse direction; `work` and `guide_linked` layers remain excluded from both directions, unchanged; a live test with two disposable accounts confirms both directions work identically.
3. **If one-directional-by-design is confirmed: update any member-facing copy that implies a fully mutual "shared family calendar"** to accurately describe the primary-calendar-plus-partner-access model instead, so expectations match what's built. **Acceptance criteria:** no in-product copy claims mutual visibility beyond what Section 1/2 confirm exists.
4. **Resolve the opt-in-vs-opt-out default decision (Section 8, decision 1).** **Acceptance criteria:** a written decision exists; if the defaults should change, `app/api/household/accept/route.ts`'s explicit insert values and, separately, the underlying column defaults in a future migration are updated together, so schema and application code cannot drift apart the way this audit found other cascade-related columns had.
5. **Add a pre-connection or immediately-post-connection summary of sharing defaults, shown to the primary**, regardless of the outcome of item 4, since even a deliberately opt-out default benefits from being shown, not just settable later in Settings. **Acceptance criteria:** the invite flow or the accept confirmation displays the exact toggle states that will apply (or already applied), with a direct link to change them, rather than requiring the primary to separately discover Settings.
6. **Define the connected-partner deletion-impact behavior (Section 8, decision 3)** as part of, not separate from, the account-deletion work already specified in `operating/account-deletion-remediation-contract-2026-07-24.md`. **Acceptance criteria:** when a primary's account deletion completes, the connected partner's `subscription_status` is explicitly and correctly updated (matching the existing `household/revoke` behavior), and, if decided, the partner receives a notice; a live test with two disposable accounts confirms the partner's own account and data remain fully intact and their access state is correct afterward, not merely "not crashed."
7. **Live-verify the two items in Section 8's live-verification-only list**, since both are cheap to check directly and would sharpen items 4 and 5 above with real evidence about the current invite-flow experience rather than this audit's inference from the Settings page alone.

**No item in this backlog was acted on by this audit.** Every finding reflects read-only inspection of the code and schema as committed, not a change made during this review.
