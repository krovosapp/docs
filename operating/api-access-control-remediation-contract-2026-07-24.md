# API Access Control Remediation Contract

**Date:** July 24, 2026
**Purpose:** A required-by-category authorization contract for every Krovos API route, plus a migration plan for removing the blanket `pathname.startsWith('/api')` public-path rule without breaking legitimate public routes, and specific acceptance criteria for the three highest-priority fixes named in `operating/api-authorization-exposure-audit-2026-07-24.md`. This document defines requirements; it does not implement them. No code, database policies, secrets, deployment settings, or providers were changed to produce it.

**Sources:** `operating/api-authorization-exposure-audit-2026-07-24.md` (this repo, July 24, 2026), and direct, read-only inspection of `/Users/christine.smith/krovos`: `proxy.ts`, `lib/publicPaths.ts`, `lib/authenticated-service-request.ts`, and the route files named throughout.

---

## 0. How to Read This Document

Every route audited in `operating/api-authorization-exposure-audit-2026-07-24.md` belongs to exactly one of the seven categories below. The category determines the *contract*, the minimum verification that route must perform, the response it must return on failure, and whether rate limiting is required, regardless of which specific mechanism (session cookie, bearer token, shared secret, signature) implements it. A route that meets its category's contract is compliant even if implemented differently from a sibling route in the same category; a route that falls short of its category's contract is a gap, whether or not this document already knows its name.

---

## 1. Public Lead Capture and Signed-Link Callbacks

**Definition:** routes reachable before a Krovos account exists, or reachable by a caller who will never have one (a gift recipient before redemption, a partner accepting a household invite). No session is possible or required by design.

**Routes in this category today:** `life-gaps/submit`, `life-gaps/unsubscribe`, `waitlist` (POST), `waitlist/unsubscribe`, `winback/unsubscribe`, `gifts/purchase`, `beta/signup` (POST/GET), `adult-migration/validate`, `household/invite-info`.

**Required verification:**
- No session or credential is required to *reach* the route.
- Any action beyond a pure, side-effect-free read (an unsubscribe, a signup, a purchase) must be gated by a high-entropy, unguessable identifier the caller could only have obtained from the original communication (a UUID row `id`, a 256-bit random token), never a value the caller could plausibly know or guess about someone else, such as a bare email address, a sequential ID, or a short human-chosen code with no separate rate limit.
- The response must disclose the minimum necessary to complete the route's stated purpose. `household/invite-info`'s current shape (status, invite email, primary's first name only) is the model to match; a route in this category must never return another kind of sensitive record (financial detail, quiz answers, full profile data) keyed only by a guessable value.

**Expected failure response:** a 400 for a missing required parameter, a 404 for an identifier that doesn't resolve (never distinguishing "wrong token" from "expired token" from "never existed" in a way that helps an attacker enumerate valid values), and no response body beyond what's needed for the caller's own next step.

**Rate limiting: required** for any route that creates a record, spends money, or claims a limited resource (`gifts/purchase`, `beta/signup`), keyed to a request fingerprint, an email, or an IP, whichever fits the route's own abuse model. **Not required** for a pure unsubscribe-by-token action, since the identifier's own entropy is the control and a rate limit adds no meaningful protection there.

---

## 2. Authenticated Free/Account-Management Actions

**Definition:** routes that require a real Krovos account and a valid session, but not an active paid subscription, because the action either manages the subscription itself, manages account-level state that should remain reachable regardless of billing status, or is part of a free/beta tier by design.

**Routes in this category today:** `household/invite`, `household/accept`, `household/revoke`, `household/reconnect`, `beta/feedback`, `feedback/submit`, `stripe/cancel`, `stripe/pause`, `stripe/save-offer`, `stripe/checkout`, `adult-migration/claims`, `calendar/oauth/[provider]/start`.

**A specific classification note:** `stripe/checkout` belongs here, not in Section 3, even though it is a paid-product route, because its entire purpose is establishing a subscription for someone who does not yet have an active one. Requiring an active subscription to reach the route that creates a subscription would be circular. The same reasoning applies to `stripe/cancel`, `stripe/pause`, and `stripe/save-offer`: a member managing their own subscription's lifecycle must be able to reach these regardless of whether that subscription is currently active, paused, or already scheduled to cancel.

**Required verification:**
- A valid, current Krovos session (cookie-based `getUser()` or an equivalent bearer-token check).
- Every identifier the route acts on (a connection ID, a subscription ID, a claim ID) must be either derived from the authenticated session directly, or explicitly checked for ownership against the authenticated caller before use, exactly as `household/revoke` already does (`conn.primary_user_id !== user.id` → 403) and `household/partner-data` already does (deriving the partner's ID from the caller's own connection row rather than accepting one as input).
- No subscription-status check is required or appropriate here.

**Expected failure response:** 401 for no/invalid session; 403 for a valid session that does not own the referenced resource; 404 for a resource that does not exist (not distinguished from 403 in the response body, to avoid confirming existence to a caller who shouldn't see it).

**Rate limiting: recommended, not strictly required**, for actions with real-world cost or abuse potential (repeated invite sends, repeated cancel/pause toggling); not required for simple state reads.

---

## 3. Active-Subscription-Only Member Actions

**Definition:** routes that deliver the paid product itself, an active Krovos subscription (or unexpired gift access, or connected-user inherited access) is required, matching exactly the access rule already defined once in `proxy.ts`: `subscription_status` in (`active`, `trialing`, `founder`, `connected_user`), or an unexpired `gift_access_end`.

**Routes in this category today:** `generate-report`, `krovos-guide` (GET/POST/PATCH), `update/apply`, `white-glove-prompt`, `extract-documents`, `extract-documents-about`, `extract-spending`, `gifts/redeem`, `household/goals`, `life-labor/occurrences`, `calendar/connections`, `calendar/import/apple`, `net-worth/snapshot`, `stripe/guide-checkout` (per the product's own pricing rule that guide purchase access requires an active Core subscription).

**Required verification:**
- Everything required in Section 2, plus: after confirming a valid session, load `profiles.subscription_status` and `profiles.gift_access_end` for that same user and confirm the identical access rule `proxy.ts` already encodes. **This check must live in one shared place**, not be re-derived per route, so the API-layer rule and the page-layer rule in `proxy.ts` can never silently drift apart from each other. See Section 9(B) for the specific implementation shape.
- The subscription check happens *after* session validation and *before* any data is read or written, so an unsubscribed caller never causes a partial side effect.

**Expected failure response:** 403 with a clear, member-facing-safe reason (`"An active subscription is required."`), not a bare 401 (which would incorrectly suggest the problem is the session itself) and not a 402 unless Krovos later adopts payment-required semantics deliberately and consistently across the product; pick one status code and apply it uniformly across every route in this category.

**Rate limiting: required** for any route that triggers a paid, metered, or rate-limited external service call (`krovos-guide`'s existing per-user turn/cost limits are the model; `generate-report` and the `extract-*` document routes should have an equivalent, explicit limit if they don't already).

---

## 4. Household-Sharing Actions

**Definition:** routes that deliberately cross from one member's data into another's, by design, because a household connection and an explicit sharing toggle permit it. This is a narrower, stricter version of Sections 2/3's ownership check: the resource being read or written does not belong to the caller at all, and the authorization question is "does an active, explicitly-configured sharing relationship permit this specific cross-account read or write," not merely "does the caller own this row."

**Routes in this category today:** `household/partner-data`, `household/guide-access`, `household/goals` (when writing to `connection.primary_user_id`'s `life_graph` on a connected user's behalf).

**Required verification:**
- A valid session (Section 2's requirement), for the *connected* party, not the primary, since these routes exist specifically to serve the connected user's view of the primary's data.
- An active household connection row (`status = 'active'`) linking the caller to the resource owner, looked up from the caller's own session, never accepted as a client-supplied primary-user identifier.
- The specific sharing toggle the action requires (`financial_shared` for financial reads, `life_labor_shared` plus `life_labor_edit_access` for household-task writes, `calendar_shared` for personal-calendar reads/writes), checked as a separate, explicit condition from the connection's mere existence. A connection existing does not imply every toggle is on.
- Every existing route in this category already implements this correctly per the exposure audit; the requirement here is to hold this same shape for any future household-sharing route, not to fix an existing one.

**Expected failure response:** an empty/null result (`{ partner: null }`, `{ hasAccess: false }`) rather than an error, when the connection exists but the specific toggle is off, matching the existing routes' own behavior; this avoids leaking the existence of a connection or a toggle state to a caller who shouldn't be able to distinguish "no connection" from "connection exists but not shared."

**Rate limiting: not required** beyond what Section 2/3 already specifies for the underlying action.

---

## 5. Stripe Webhooks

**Definition:** exactly one route, `stripe/webhook`, receiving server-to-server callbacks from Stripe with no user session involved at all.

**Required verification:**
- Cryptographic signature verification via `stripe.webhooks.constructEvent()` against `STRIPE_WEBHOOK_SECRET`, checking the `stripe-signature` header on every request, with no exception. This is already correctly implemented.
- No other authentication mechanism is appropriate or necessary here; the signature check is the entire contract.

**Expected failure response:** 400 for a signature that fails to verify (already correctly implemented). Never 401/403, since those imply an identity check that doesn't apply to this route's actual security model.

**Rate limiting: not required.** Stripe's own retry behavior and the signature requirement are the relevant controls; an additional rate limit could cause legitimate retried webhook deliveries to be dropped.

---

## 6. Cron Jobs

**Definition:** routes invoked only by Vercel's own scheduler, never by a browser or a member-facing client.

**Routes in this category today:** `cron/birthday-check`, `cron/calendar-sync`, `cron/gift-delivery`, `cron/grace-period-check`, `cron/life-labor-occurrences`, `cron/send-sequences`.

**Required verification:**
- A direct, unconditional comparison of the request's `Authorization` header against `Bearer ${process.env.CRON_SECRET}`, structured so that **an unset or empty `CRON_SECRET` causes every request to fail**, never causes the check to be skipped. The required shape is:

  ```
  if (!process.env.CRON_SECRET || authHeader !== `Bearer ${process.env.CRON_SECRET}`) {
    return 401
  }
  ```

  Any variant that short-circuits past the check when the secret is falsy (`if (cronSecret && authHeader !== ...)`) is non-compliant, regardless of whether it currently causes a live incident, because a future misconfiguration (a secret accidentally removed from an environment) would silently reopen the route to the entire internet instead of safely refusing to run.
- Five of the six routes in this category already meet this contract as written; `cron/send-sequences` does not (see Section 9(C) for its specific acceptance criteria).

**Expected failure response:** 401, with no response body beyond a minimal error message; the route should perform no database read or write before this check passes.

**Rate limiting: not required.** The shared secret is the entire control; a cron route has no legitimate high-volume caller to rate-limit against, and adding one would only risk the scheduler's own legitimate invocation being throttled.

---

## 7. OAuth Callbacks

**Definition:** routes a third-party provider (Google, Microsoft) redirects the member's browser to, or calls directly (a push-notification webhook tied to an existing OAuth-authorized subscription).

**Routes in this category today:** `calendar/oauth/[provider]/callback` (browser-redirect code exchange), `calendar/webhooks/google` and `calendar/webhooks/outlook` (provider-initiated push notifications).

**Required verification, browser-redirect callbacks (`calendar/oauth/[provider]/callback`):**
- A valid Krovos session, since the browser making this request is the same one that started the OAuth flow while signed in.
- The OAuth `state` parameter returned by the provider must match a value Krovos itself generated and stored in a short-lived, `httpOnly` cookie at the start of the flow, rejecting any mismatch before exchanging the authorization code. This is already correctly implemented.
- The exchanged code must be redeemed for a token immediately server-side; the code itself must never be exposed to any client-side script.

**Required verification, provider-initiated webhooks (`calendar/webhooks/google`, `calendar/webhooks/outlook`):**
- A shared secret the provider is configured to echo back on every notification (`x-goog-channel-token` for Google, `clientState` for Outlook), compared against `CALENDAR_WEBHOOK_SECRET`, rejecting any mismatch, including when the header is absent. This is already correctly implemented and, unlike Section 6's cron routes, is naturally fail-closed as written today (an unset secret cannot equal any real header value a provider would send), but should still be written as an explicit `!process.env.CALENDAR_WEBHOOK_SECRET` check for the same defensive reason given in Section 6, rather than relying on the coincidence that `undefined !== "some string"` happens to be true.
- The connection the notification refers to must be looked up by the provider's own channel/subscription identifier and must belong to an `active`, `work_sync` connection before any sync is triggered; a webhook for an unknown or inactive channel should be a silent no-op (204/202), not an error, since a provider will continue sending notifications for a channel Krovos has already disconnected until that channel's TTL expires.

**Expected failure response:** a redirect to a member-visible error state (`/life-calendar?calendar_connection=error`) for the browser-redirect callback, since a JSON error response would be shown raw to the member's browser; a bare 401 with no body for the provider-initiated webhooks, since no human ever sees that response.

**Rate limiting: not required** for either; both are gated by a secret or a session, and a provider's own retry/backoff behavior governs webhook volume.

---

## 8. Migration Plan: Removing the Blanket `/api` Public-Path Rule

**The problem being fixed:** `lib/publicPaths.ts`'s `isPublicPath()` includes `pathname.startsWith('/api')`, making every API route middleware-public regardless of its actual category. This must not be fixed by simply deleting that line, because `proxy.ts`'s current non-public branch behavior, an HTML redirect to `/auth/sign-in` or `/subscribe`, is correct for a browser navigating to a page and would break every API route's actual contract, since a `fetch()` call expects a JSON response (or a defined error status), not a 307 redirect to an HTML page.

**Step 1: Split the single `isPublicPath()` into two explicit functions.** `isPublicPagePath()` keeps today's exact behavior, unchanged, for all non-API routes. A new `isPublicApiPath()` contains an explicit allowlist built directly from Section 1 of this contract: `life-gaps/submit`, `life-gaps/unsubscribe`, `waitlist`, `waitlist/unsubscribe`, `winback/unsubscribe`, `gifts/purchase`, `beta/signup`, `adult-migration/validate`, `household/invite-info`, plus `stripe/webhook` (Section 5) and the six cron routes (Section 6) and the two calendar provider webhooks (Section 7), since none of these should ever redirect to sign-in even after this migration, they should simply continue reaching their own internal secret/signature/token check exactly as today. `life-gaps/prefill` is deliberately **not** included in this allowlist; see Section 9(A).

**Step 2: Change `proxy.ts`'s non-public branch to respond differently for API paths than for page paths.** For any request where `pathname.startsWith('/api')` and `!isPublicApiPath(pathname)`: if there is no authenticated user, return a JSON `401` response directly from the middleware (`NextResponse.json({ error: 'Unauthorized' }, { status: 401 })`), never a redirect. Do not attempt to also enforce subscription status at this layer; leave that entirely to Section 3's shared-helper requirement (Section 9(B)), so the middleware's job stays narrowly scoped to "is there a valid session at all," the same question `authenticateServiceRequest` already answers, rather than duplicating category-specific business logic that belongs with each route.

**Step 3: Verify no currently-working legitimate route breaks.** Before this ships, run the full route inventory from `operating/api-authorization-exposure-audit-2026-07-24.md` against the new logic and confirm: every route already listed as "intentionally public" in Sections 1, 5, 6, and 7 above remains reachable without a session; every route in Sections 2, 3, and 4 above, which already performs its own session check internally, sees no behavior change for an authenticated caller, since the new middleware-level 401 for an unauthenticated caller is now simply reached one layer earlier than before, not a new kind of rejection.

**Step 4: Ship this as a monitored, reversible change.** Deploy with the new middleware logic active but logging (not silently swallowing) any request that would newly receive a 401 at the middleware layer, for one full review cycle, before treating the migration as complete. If any legitimate route is discovered mid-flight to need public access that wasn't anticipated in Step 1's allowlist, add it explicitly to `isPublicApiPath()` with a comment stating why, rather than reverting to the blanket rule.

**Step 5: Treat `isPublicApiPath()`'s allowlist as a standing, reviewed artifact.** Any new API route added to the codebase going forward must be explicitly classified into one of this contract's seven categories before it ships; a route that needs to be public must be added to the allowlist deliberately, not left public by the absence of a check, which is the exact condition this migration exists to eliminate.

---

## 9. Specific Acceptance Criteria

### (A) Fixing `life-gaps/prefill`

- The route no longer accepts a bare, unauthenticated `email` query parameter as sufficient to return data.
- The route requires a valid Krovos session (Section 2's contract) and uses that session's own verified email address to look up `life_gaps_submissions`, ignoring or explicitly rejecting any client-supplied `email` value that differs from the session's own.
- **Acceptance test 1:** an unauthenticated request (no session) to this route returns 401 with no data, for any `email` value, including one known to have a real submission.
- **Acceptance test 2:** an authenticated request where the session's own email does not match any `life_gaps_submissions` row returns an empty/null result, not an error that would confirm or deny whether some *other* email has a submission.
- **Acceptance test 3:** an authenticated request where the session's own email does match a submission returns that submission's `answers`, `detected_phases`, and `active_modifiers`, i.e., the legitimate pre-fill use case continues to work end to end, just gated correctly.
- **Acceptance test 4:** a request attempting to pass a different member's email while authenticated as someone else returns the same empty/null result as test 2, never that other member's data.
- Rate limiting is added regardless of the above, since this remains a database lookup reachable by any signed-in member; a per-user or per-session limit is sufficient, since the identity check already removes the anonymous-enumeration risk.

### (B) Enforcing Subscription Status on Paid APIs

- A single, shared authorization function (extending `lib/authenticated-service-request.ts` or added alongside it, e.g. `authenticateSubscribedRequest()`) performs, in order: (1) the existing session validation `authenticateServiceRequest()` already does, (2) a lookup of that same user's `profiles.subscription_status` and `profiles.gift_access_end`, (3) the identical access rule already encoded once in `proxy.ts` (`active`, `trialing`, `founder`, `connected_user`, or an unexpired `gift_access_end`), returning a 403 with a clear message if none apply.
- Every route named in Section 3 above is updated to call this shared function instead of the plain session-only one. `stripe/guide-checkout` is explicitly included, per its own product rule that guide purchase requires an active Core subscription; `stripe/checkout` is explicitly **not** included, per Section 2's reasoning.
- **Acceptance test 1:** a request to `krovos-guide` (or any other Section 3 route) with a valid session but `subscription_status = 'canceled'` and no unexpired `gift_access_end` returns 403, not 200, and performs no database write before returning.
- **Acceptance test 2:** the same request with `subscription_status = 'active'` succeeds exactly as it does today, confirming no regression for a real, current subscriber.
- **Acceptance test 3:** the same is separately confirmed for `trialing`, `founder`, `connected_user`, and unexpired-`gift_access_end` states, since all five are valid access states today and the new check must not narrow access relative to what `proxy.ts` already grants at the page layer.
- **Acceptance test 4:** the shared function's access rule and `proxy.ts`'s own `hasAccess` logic are confirmed identical (ideally by having one import the other's rule, or a shared constant, rather than two independently-maintained copies of the same five-state list), so a future change to one cannot silently diverge from the other.

### (C) Making Cron Auth Fail Closed

- `app/api/cron/send-sequences/route.ts`'s authorization check is changed from `if (cronSecret && authHeader !== 'Bearer ${cronSecret}')` to the same direct, unconditional form already used by the other five cron routes: `if (!process.env.CRON_SECRET || authHeader !== 'Bearer ${process.env.CRON_SECRET}') return 401`.
- **Acceptance test 1:** with `CRON_SECRET` unset in a non-production environment, a request to this route with no `Authorization` header returns 401.
- **Acceptance test 2:** with `CRON_SECRET` unset, a request with an arbitrary, incorrect `Authorization` header also returns 401 (confirming the fix doesn't merely handle the no-header case).
- **Acceptance test 3:** with `CRON_SECRET` correctly set, a request bearing the correct value still succeeds exactly as before, confirming no regression to the legitimate Vercel-scheduled invocation.
- As a follow-on, not a blocking requirement of this specific fix: consider the build-time guard already recommended in the exposure audit's remediation plan, checking that every file under `app/api/cron/` matches this required pattern, so this exact class of regression cannot be reintroduced silently by a future cron route.

---

**No item in this contract has been implemented, tested, or acted on by this document.** Every requirement above is a specification for a future implementation session to build against and verify, not a record of a change already made.
