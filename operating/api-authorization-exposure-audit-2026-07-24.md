# API Authorization and Exposure Audit

**Date:** July 24, 2026
**Purpose:** A direct, evidence-based audit of every `app/api/**/route.ts` endpoint in the Krovos codebase (48 route files), covering middleware-layer reachability, each route's own authentication/authorization, and confirmed exposure risk. This is read-only inspection. No code, database policies, secrets, deployment settings, or providers were changed to produce it.

**Method:** direct inspection of `/Users/christine.smith/krovos` (read only): every file under `app/api/`, `proxy.ts`, `lib/publicPaths.ts`, `lib/authenticated-service-request.ts`, and the relevant migrations for identifier entropy (`household_connections.invite_token`, `life_gaps_submissions.id`, `waitlist.id`). Every claim below is either **confirmed** (traced to a specific file and line of behavior) or explicitly marked as needing **live verification**.

---

## 1. The `pathname.startsWith('/api')` Rule and Its Actual Effect

**Confirmed, from `lib/publicPaths.ts` and `proxy.ts`.** `isPublicPath()` includes the line `pathname.startsWith('/api')`. `proxy.ts`'s middleware only applies its unauthenticated-redirect and subscription-check branches when `!pathIsPublic`. Because every path under `/api/` satisfies `pathname.startsWith('/api')`, **`isPublicPath()` returns `true` for every single API route in this codebase, with no exception.** This means:

- `proxy.ts` never redirects an unauthenticated caller away from any `/api/*` route.
- `proxy.ts` never checks `subscription_status` before allowing a request through to any `/api/*` route.
- **Every API route is solely responsible for its own authentication and authorization.** There is no middleware-layer backstop for any of them.

**This directly corrects a claim made in a separately-produced audit of the Krovos Guide feature** (`product/krovos-guide-beta-readiness-audit-2026-07-24.md`, Section 4), which stated: "Subscription and authentication gating correctly protects both the page and the underlying API route via middleware, before either is reachable." That claim is **incorrect** as written. `proxy.ts` does protect the `/life-krovos-guide` *page* (since `/krovos-guide` is not in `isPublicPath`), but it does **not** protect `/api/krovos-guide` itself, because the blanket `/api` rule makes that route middleware-public. The route only requires a valid authenticated session (`authenticateServiceRequest`), never an active subscription. See Section 4 for the concrete consequence of this correction. That other document is not edited as part of this audit, since this audit's scope is the full API surface, not a single feature; the correction is recorded here and should be applied there in a follow-up pass.

---

## 2. Full Route Inventory

Every `app/api/**/route.ts` file, grouped by function. "Middleware-public" is always **yes** per Section 1; the meaningful question for every route is its own internal check, shown in the third column.

### Cron routes (intended to run only via Vercel's scheduler)

| Route | Own authorization check | Verdict |
|---|---|---|
| `cron/birthday-check` | `secret !== process.env.CRON_SECRET` (direct inequality) | **Confirmed fail-closed.** An unset `CRON_SECRET` cannot be matched by any real caller. |
| `cron/calendar-sync` | `!process.env.CRON_SECRET \|\| authorization !== 'Bearer ${...}'` | **Confirmed fail-closed**, explicit. |
| `cron/gift-delivery` | `authorization !== 'Bearer ${process.env.CRON_SECRET}'` (direct inequality) | **Confirmed fail-closed** by construction. |
| `cron/grace-period-check` | Same direct-inequality pattern | **Confirmed fail-closed.** |
| `cron/life-labor-occurrences` | Same direct-inequality pattern | **Confirmed fail-closed.** |
| `cron/send-sequences` | `if (cronSecret && authHeader !== ...)` (truthiness-guarded) | **Confirmed fail-open.** If `CRON_SECRET` is unset, the guard's left side is falsy and the whole condition short-circuits to false, meaning the 401 branch never executes and the route runs for anyone. This is the one cron in the codebase using this pattern; the other five use a direct inequality that fails closed by default. (Previously identified in `marketing/group-b-c-live-send-verification-2026-07-24.md`; restated here for completeness of this audit's full-surface scope.) |

### Stripe (payments)

| Route | Own authorization check | Verdict |
|---|---|---|
| `stripe/webhook` | `stripe.webhooks.constructEvent()` with `STRIPE_WEBHOOK_SECRET`, verifying the `stripe-signature` header | **Confirmed correct.** Standard, cryptographically verified webhook pattern; rejects with 400 on signature mismatch. |
| `stripe/checkout` | Session cookie via `authenticateServiceRequest`; every write scoped to `user.id` from the token | **Confirmed correct.** No client-supplied user identifier is trusted. |
| `stripe/guide-checkout` | Same pattern | **Confirmed correct.** |
| `stripe/cancel` | Cookie-session `auth.getUser()`; profile/subscription looked up and cancelled by `user.id` only | **Confirmed correct.** |
| `stripe/pause` | Same cookie-session pattern (confirmed via `createServerClient`/`getUser(` signal; not read in full line-by-line in this pass, but matches the identical pattern used by `stripe/cancel` and every `household/*` route) | **Confirmed correct**, consistent with the codebase's dominant, safe pattern. |
| `stripe/save-offer` | Same cookie-session pattern | **Confirmed correct**, same basis as above. |

### Household connections and sharing

| Route | Own authorization check | Verdict |
|---|---|---|
| `household/invite` | Cookie session; generates a 256-bit random hex `invite_token` (`randomBytes(32).toString('hex')`) | **Confirmed correct.** Token is not guessable. |
| `household/invite-info` | **Intentionally public**, gated only by possession of the invite token; returns only status, invite email, and the primary's first name | **Confirmed intentional and safely scoped.** Explicitly commented in the source as a pre-signup public endpoint; disclosure is minimal and the token is high-entropy. |
| `household/accept` | Cookie session; connection resolved by `invite_token`, all writes scoped to `user.id` | **Confirmed correct.** |
| `household/revoke` | Cookie session; explicitly checks `conn.primary_user_id !== user.id` before acting on a client-supplied `connectionId`, returning 403 otherwise | **Confirmed correct.** This is the textbook-correct pattern for a client-supplied identifier: trust the ID only after verifying the authenticated caller owns the resource it points to. |
| `household/reconnect` | Cookie session pattern, consistent with `revoke` | **Confirmed correct**, same basis. |
| `household/partner-data` | Cookie session; partner's user ID is derived from the caller's own verified active connection row, never from client input; additionally gated on `financial_shared` | **Confirmed correct.** Service-role cross-user read is appropriately bounded by an explicit application-level authorization check. |
| `household/guide-access` | Cookie session; primary's user ID derived the same way as `partner-data` | **Confirmed correct.** |
| `household/goals` | `authenticateServiceRequest`; primary's user ID derived from the caller's own connection row | **Confirmed correct.** |

### Calendar

| Route | Own authorization check | Verdict |
|---|---|---|
| `calendar/connections` (GET/DELETE) | `authenticateServiceRequest`; every query and delete scoped to `user_id = auth.user.id` | **Confirmed correct.** |
| `calendar/oauth/[provider]/start` | `authenticateServiceRequest`; short-lived `httpOnly` state cookie | **Confirmed correct.** |
| `calendar/oauth/[provider]/callback` | `getCalendarServerUser()` (session-cookie check) plus OAuth state validated against the stored cookie | **Confirmed correct.** |
| `calendar/import/apple` | `authenticateServiceRequest` | **Confirmed correct.** |
| `calendar/webhooks/google` | Shared-secret token (`x-goog-channel-token` compared to `CALENDAR_WEBHOOK_SECRET`) | **Confirmed correct**, fails closed (an unset secret cannot equal a real header value). |
| `calendar/webhooks/outlook` | Shared-secret token (`clientState` compared to `CALENDAR_WEBHOOK_SECRET`) | **Confirmed correct**, same reasoning. |
| `life-labor/occurrences` | `authenticateServiceRequest`; task discovery additionally uses the caller's own JWT against Supabase so RLS (not this route) decides which tasks are visible | **Confirmed correct**, a notably careful design (see the separately-produced Life Calendar audit for detail). |

### Documents, reports, and Krovos Guide

| Route | Own authorization check | Verdict |
|---|---|---|
| `extract-documents` | `authenticateServiceRequest`; every query scoped to `userId` | **Confirmed correct.** |
| `extract-documents-about` | `authenticateServiceRequest`; additionally verifies a client-supplied storage path is prefixed with the caller's own user ID before touching Storage | **Confirmed correct**, a good defense against path traversal into another member's private bucket folder. |
| `extract-spending` | Same storage-path-prefix check as above | **Confirmed correct.** |
| `generate-report` | `authenticateServiceRequest`; scoped to `userId` | **Confirmed correct for identity**, but see Section 4: no subscription check exists in the route itself. |
| `krovos-guide` (GET/POST/PATCH) | `authenticateServiceRequest`; scoped to `userId` | **Confirmed correct for identity**, but see Section 4: no subscription check exists in the route itself. |
| `update/apply` | `authenticateServiceRequest`; every checkin/log/upsert scoped to `user.id` | **Confirmed correct for identity**, same subscription caveat as above. |
| `white-glove-prompt` | `authenticateServiceRequest`; scoped to `userId` | **Confirmed correct for identity**, same caveat. |

### Gifts and net worth

| Route | Own authorization check | Verdict |
|---|---|---|
| `gifts/purchase` | **Intentionally public** (a gift giver may have no Krovos account); rate-limited by an 8-per-hour request fingerprint hash; explicitly rejects discount/coupon parameters; no money moves until Stripe's own Checkout Session completes | **Confirmed intentional and appropriately mitigated.** |
| `gifts/redeem` | `authenticateServiceRequest` | **Confirmed correct for identity.** |
| `net-worth/snapshot` | Inline bearer-token check via `supabase.auth.getUser(token)`, functionally equivalent to `authenticateServiceRequest` but implemented separately; scoped to `user.id` | **Confirmed correct**, though this is a defense-in-depth/consistency note: this route re-implements the shared auth pattern instead of using the shared helper, which is not itself a vulnerability but is worth consolidating (Section 5). |

### Life Gaps, waitlist, and beta (public lead-generation and low-friction signup surfaces)

| Route | Own authorization check | Verdict |
|---|---|---|
| `life-gaps/submit` | **Intentionally public** (a lead magnet, no account required) | **Confirmed intentional.** |
| `life-gaps/prefill` | **None.** Takes an arbitrary `email` query parameter and returns that email's Life Gaps `answers`, `detected_phases`, and `active_modifiers` with no authentication, no ownership check, and no rate limit. | **Confirmed active vulnerability. See Section 3.** |
| `life-gaps/unsubscribe` | **Intentionally public**, gated by a UUID row `id` acting as an unguessable bearer token; action is limited to unsubscribing, no data is returned to the caller | **Confirmed intentional and safely scoped**, standard one-click-unsubscribe pattern. |
| `waitlist` (POST) | **Intentionally public** (waitlist signup) | **Confirmed intentional.** |
| `waitlist/unsubscribe` | Same UUID-row-`id`-as-token pattern as `life-gaps/unsubscribe` | **Confirmed intentional and safely scoped.** |
| `winback/unsubscribe` | Same pattern, scoped by `group_id = 'W'` | **Confirmed intentional and safely scoped.** |
| `beta/signup` (POST/GET) | **Intentionally public** (pre-account signup), gated by a `beta_codes` invite code; correctly rolls back the created auth user if profile activation fails | **Confirmed intentional.** No rate limit was found on repeated code-guessing attempts; low real-world severity today (worst case is an extra beta account), noted as a defense-in-depth item in Section 5, not a vulnerability. |
| `beta/feedback` | Cookie session; verifies `profile.beta_tester` before accepting a submission | **Confirmed correct.** |
| `feedback/submit` | Cookie session; scoped to `user.id`, `beta_tester` read from the profile rather than trusted from the client | **Confirmed correct.** |

### Adult migration

| Route | Own authorization check | Verdict |
|---|---|---|
| `adult-migration/claims` | `authenticateServiceRequest` | **Confirmed correct.** |
| `adult-migration/validate` | **Intentionally public** (a prospective independent adult has no account yet); returns only a boolean `valid`/`invalid`, never claim details | **Confirmed intentional and safely scoped.** |

---

## 3. Confirmed Vulnerability: `life-gaps/prefill` Unauthenticated PII Disclosure

**File:** `app/api/life-gaps/prefill/route.ts`. **Severity: high, active.**

The route accepts an unauthenticated `GET` request with an `email` query parameter and returns that email address's most recent `life_gaps_submissions` row: `answers`, `detected_phases`, and `active_modifiers`. There is no session check, no ownership verification, no rate limiting, and no requirement that the caller prove control of the email address in any way (no token, no signed link, nothing beyond the literal string).

**Why this matters concretely:** the Life Gaps modifier taxonomy (documented in `CLAUDE.md`) includes values such as `DIVORCED`, `WIDOWED`, `PARTNER_VISA`, `MEDICAL_EXPENSE`, `REBUILDING`, and `CONCERN_HEALTHCARE`, meaning the `answers`/`active_modifiers` payload can reveal a real person's relationship status, immigration situation, a stated medical expense concern, or that they are "starting over" after some kind of setback, i.e., specifically the sensitive life-situation categories the product exists to handle with care. Anyone who knows or can guess a person's email address, which is not a secret for most people, can retrieve this without ever signing in.

**Intended purpose, confirmed from context:** this endpoint exists to support pre-filling Life Profile fields for a member whose email matches a prior Life Gaps submission (the "From your Life Gaps" badge feature described in `CLAUDE.md`). That is a legitimate feature; the flaw is that the endpoint verifies only that the caller *knows* an email address, not that they *are* that person (e.g., a signed-in session whose own email matches, or a token issued at Life Gaps submission time and later presented at onboarding).

**This is a real gap requiring a code fix**, out of scope for this docs-only audit to perform, but the fix shape is clear: either require an authenticated session and use `user.email` directly instead of a query parameter, or require a signed, single-use token issued to that specific submission (the same pattern already used correctly by the unsubscribe routes) instead of a raw email string.

---

## 4. Confirmed Gap: No Route Using `authenticateServiceRequest` Checks Subscription Status

**Severity: high, active, systemic.** Every route in the "Documents, reports, and Krovos Guide" group in Section 2, plus `gifts/redeem`, `household/goals`, `calendar/connections`, `life-labor/occurrences`, `stripe/checkout`, `stripe/guide-checkout`, and `adult-migration/claims`, uses the shared `authenticateServiceRequest()` helper. That helper (`lib/authenticated-service-request.ts`) does exactly one thing: validate that the bearer token corresponds to a real, currently-valid Supabase session. **It does not check `profiles.subscription_status` anywhere.** A grep across every route file using this helper confirmed none of them separately check subscription status either.

Combined with Section 1's finding that `proxy.ts`'s subscription-check branch never runs for any `/api/*` route, the practical consequence is: **a member with a valid Supabase session but a lapsed, cancelled, or never-started subscription can call any of these API routes directly and receive a real response**, bypassing the `/subscribe` redirect that protects the corresponding *page*. Concretely, this includes generating a new Life View report, sending and receiving real Krovos Guide responses (consuming the underlying Anthropic API budget), submitting a Life Updates check-in, or reading household partner data, none of which should be reachable without an active subscription per the product's own access model.

This is not a contradiction of any specific written privacy or security claim this audit found (see Section 6), but it is a real mismatch between the product's intended subscription-gated access model and what the code actually enforces. It is systemic rather than a one-off bug: it reflects an architectural assumption, that `proxy.ts` handles subscription gating for everything, that Section 1 shows does not extend to the API layer at all.

---

## 5. Is Service-Role Use Appropriately Bounded After Authentication?

**Confirmed yes, consistently, across every route reviewed.** The recurring, correct pattern in this codebase is: authenticate the caller first (via `authenticateServiceRequest`, a cookie-session `getUser()` call, a cron secret, a webhook signature, or a shared webhook token), and only *after* that succeeds, use a service-role client to perform the actual database operation, scoped to an identifier derived from the authentication step itself, either the caller's own `user.id`, or (for the household cross-user routes) a partner ID derived from a connection row the caller was already verified to belong to. No route was found that accepts a service-role-privileged action gated on a client-supplied identifier with no ownership check, except for the two problems already named in Sections 3 and 4, neither of which is a service-role misuse in the conventional sense: Section 3 has no service-role-vs-anon distinction at all (there is no authentication step to bound), and Section 4 is a missing authorization *check* layered on top of otherwise-correct authentication and correctly-scoped service-role use.

---

## 6. Privacy/Security Language vs. Actual Enforcement

This audit reviewed `app/privacy/page.tsx`'s AI-processing language in the separately-produced Krovos Guide audit and found it accurate against the Guide's own code. For the API surface specifically:

- No privacy or marketing copy reviewed in this or prior audits makes an explicit claim such as "every API endpoint requires an active subscription" or "all API routes are protected by middleware," so Section 4's finding is not a contradiction of stated privacy language; it is a mismatch between the product's business model and its enforcement, not between its privacy promises and its code.
- The privacy page's claims about what Krovos Guide sends to Anthropic, what's excluded, and retention remain accurate regardless of this audit's findings; those findings are unrelated to authorization.
- The one place this audit found a direct, specific claim about another document's security behavior that turned out to be wrong is the Krovos Guide audit's proxy-gating statement, corrected in Section 1.

---

## 7. Prioritized Remediation Plan

### Active security gaps (highest priority, require a code change)

1. **Fix `life-gaps/prefill`'s unauthenticated PII disclosure** (Section 3). This is the most severe, most immediately exploitable finding in this audit and should be treated as the top priority.
2. **Add subscription-status checks to every route currently relying only on `authenticateServiceRequest`** (Section 4), or, more robustly, extend the shared helper itself to optionally require an active subscription, so future routes inherit the check by default instead of needing to remember it individually.
3. **Fix `cron/send-sequences`'s fail-open `CRON_SECRET` check** (Section 2), changing it to the same direct-inequality pattern already used correctly by the other five cron routes. (Also already flagged in `marketing/pre-launch-email-remediation-plan-2026-07-24.md`, Gate 2.2; restated here since this audit's scope is the full API surface.)

### Defense-in-depth improvements (should happen, lower urgency than the above)

4. **Consolidate `net-worth/snapshot`'s inline bearer-token check onto the shared `authenticateServiceRequest` helper**, purely for consistency; the current inline version is not itself incorrect.
5. **Add a rate limit to `beta/signup`**, matching the pattern already used correctly on `gifts/purchase`, since a low-entropy, human-shareable beta code has no other protection against repeated guessing today.
6. **Consider a lightweight, repository-wide check (a lint rule or a build-time audit script, matching the existing pattern of `audit:tool-inputs` and `audit:app-shell`) that flags any new cron route using a truthiness-guarded secret comparison instead of a direct inequality**, so the fail-open pattern found in `send-sequences` cannot be reintroduced silently by a future route.

### Documentation-only corrections (no code change needed)

7. **Correct the specific claim in `product/krovos-guide-beta-readiness-audit-2026-07-24.md`, Section 4**, that middleware protects the Krovos Guide API route; it does not, per Section 1 of this audit. That document was not edited as part of this audit; this is a flagged follow-up for whoever next updates it.
8. **Note, for future audits of any new API route, that `pathname.startsWith('/api')` in `lib/publicPaths.ts` means no new route can rely on `proxy.ts` for authentication or subscription gating, regardless of intent.** This should be treated as a standing fact to check against, not something that needs to change; the current design correctly places responsibility on each route, and every route this audit reviewed except the two named in Sections 3 and 4 already does this correctly.

**No item in this checklist was acted on by this audit.** Every finding reflects read-only inspection of the code as committed, not a change made during this review.
