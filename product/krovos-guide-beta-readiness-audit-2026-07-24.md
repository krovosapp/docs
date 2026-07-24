# Krovos Guide Beta Readiness Audit

**Date:** July 24, 2026
**Purpose:** A direct, evidence-based audit of what Krovos Guide actually does today, against what the product promises. This is read-only inspection. No app code, prompts, database records, API/provider settings, or deployed services were changed to produce it.

**Method:** direct inspection of `/Users/christine.smith/krovos` (read only): `app/api/krovos-guide/route.ts`, `app/krovos-guide/page.tsx`, `lib/krovos-guide-tool-context.ts`, `lib/krovos-guide-starters.ts`, `lib/authenticated-service-request.ts`, `proxy.ts`, `lib/publicPaths.ts`, `app/privacy/page.tsx`, and the migrations `supabase/migrations/20260722171000_krovos_guide_memory.sql` and `supabase/migrations/20260722192000_core_tool_results.sql`. Every claim below is either **confirmed** (traced to a specific file and line of behavior) or explicitly marked as an **assumption** where the code alone cannot settle the question.

---

## 1. What Krovos Guide Sends to Anthropic

**Confirmed, from `app/api/krovos-guide/route.ts`.** Every request to Anthropic's Messages API (`model: claude-sonnet-4-6`) is built from exactly these pieces:

1. **A fixed system prompt** (`SYSTEM_PROMPT`) describing Krovos Guide's role, principles, and tone. Static text, no member data.
2. **A curated Life Graph summary** (`buildLifeGraphSummary()`), built by extracting specific fields from the member's `life_graph` row and formatting them as plain text. Confirmed included: the member's age (computed from date of birth, not the date of birth itself), whether a partner is present and their age, the number of children and each child's age (not names or birthdates), housing type, state (not full address), annual net take-home income and pay frequency for the member and partner, combined household income, mortgage balance/rate/monthly payment, each debt's type/balance/rate/payment and the total, a specific list of investment account balances (401k, Roth IRA, brokerage, partner Roth IRA, partner 457b, total), retirement goal fields (target ages, target monthly income, lifestyle, life expectancy), and specific monthly spending categories (total, childcare, groceries, a computed subscriptions total, streaming, software).
3. **The member's preferred display name** (`profile.preferred_name`, falling back to the first word of `full_name` if no preferred name is set).
4. **An allowlisted summary of saved Core tool outcomes** (`buildRelevantToolContext()` in `lib/krovos-guide-tool-context.ts`), drawn from up to 10 recently updated `core_tool_results` rows. Only three tools have any defined field allowlist today (Emergency Fund Calculator, Debt Payoff Planner, Insurance Gap Checker); for those, only specific named numeric fields from the `outcome` JSON are surfaced (for example `currentMonths`, `payoffOrder`, `totalNeed`), never the tool's raw `inputs`. The `core_tool_results` query itself only selects `tool_route, tool_name, outcome, decision`, never `inputs`, so raw tool inputs are structurally unreachable from this code path regardless of the allowlist.
5. **Recent conversation context**: the last 8 messages (`CONTEXT_MESSAGE_LIMIT`, configurable via environment variable) of the *current* conversation only, by role and raw content. Other conversations the member has had with Krovos Guide are not included.
6. **The member's newly typed message**, sent verbatim, with no server-side redaction or scrubbing of its contents.

**What is confirmed excluded:** raw uploaded documents, government ID numbers, account credentials, authentication tokens, full names beyond the display name, full addresses, and any `financial` fields not explicitly extracted by `buildLifeGraphSummary()`. This last point matters: although the API route fetches the *entire* `life_graph` and `profiles` rows (`select('*')`) into server memory, only the specific fields listed above are ever serialized into what's sent to Anthropic. Business data, pension data, immigration/visa data, insurance and benefits data, HSA/FSA data, equity/RSU/commission data, and any aging-parent or caregiving flag are all present in the fetched row but are never included in the text sent to the model. See Section 7 for why this matters beyond a simple data-minimization win.

**A specific, code-confirmed gap in the allowlist:** while `outcome` fields are correctly restricted per tool by `RULES` in `lib/krovos-guide-tool-context.ts`, the `decision` field (a free-text column on `core_tool_results`) is passed through **unfiltered for every tool**, including tools with no defined allowlist rule at all. This is a narrower, but real, exception to the "allowlisted summary only" design the code comments claim.

**What must not be assumed:** the member's raw typed message is not filtered for sensitive content before being sent (Section 5 covers this directly). The privacy page's own language already discloses this correctly (see Section 2); it is not a silent gap.

---

## 2. Privacy and Security Language vs. Actual Implementation

**Confirmed, from `app/privacy/page.tsx`, compared line by line against the code in Section 1.** The privacy page's AI/Guide-specific paragraph states: "Krovos Guide sends the messages you enter, a limited structured selection of relevant Life Graph facts, recent conversation context, and saved Krovos outcomes to Anthropic so it can provide personalized guidance." This is accurate against the code: it names exactly the same categories confirmed in Section 1, in the same shape (message text, a *limited* selection of Life Graph facts, recent conversation context, saved outcomes), and does not overstate what's sent.

It further states: "Krovos does not automatically add raw documents, government identification numbers, account credentials, or authentication information to Guide context." This is confirmed accurate; none of those categories appear anywhere in `buildLifeGraphSummary()` or the tool-context allowlist.

It adds the caveat: "Do not enter those details in Guide messages because message text is processed as submitted." This is an honest, correctly-worded disclosure of a real limitation (Section 1: the member's typed message is not scrubbed), not a false reassurance.

On retention: "Anthropic states that standard commercial API inputs and outputs are deleted from its backend within 30 days, subject to its usage-policy, legal, and contractual exceptions. Krovos does not currently represent that it has a separate zero-data-retention agreement." This is an appropriately hedged claim about a third party's stated policy, correctly sourced with a link, and does not claim more certainty than the code or a business relationship can support.

On account deletion: "Upon account deletion, your personal data and uploaded documents will be permanently removed within 30 days." Section 6 covers whether this matches the Guide-specific schema.

**Overall finding: the privacy page's language matches the actual implementation closely and accurately, on every claim this audit could verify against code.** This is the strongest confirmed-safe finding in this audit. The one caveat is structural, not a copy problem: the privacy page's promises about account deletion depend on the account-deletion flow itself correctly removing the `auth.users` row, which this audit did not separately verify (see Section 6).

---

## 3. Message Limits, Reset Behavior, Tier Status, and Surprise Avoidance

**Confirmed limits, from `app/api/krovos-guide/route.ts`:** a monthly turn limit (`MONTHLY_TURN_LIMIT`, default 100), a daily turn limit (`DAILY_TURN_LIMIT`, default 20), and a separate monthly cost cap (`MONTHLY_COST_LIMIT_CENTS`, default $3.00 estimated cost, computed from token counts and per-million pricing constants). These are three independent gates: a request can be blocked by the cost cap before the turn-limit check ever runs, or blocked by the turn-limit check (via the `claim_guide_turn` database function) even if the cost cap hasn't been reached.

**Confirmed reset behavior:** both `guide_usage_monthly`'s row-per-month structure and the `claim_guide_turn()` function key on `date_trunc('month', ...)`, so monthly counters reset automatically at the start of each calendar month (UTC). The daily counter resets when `daily_date` no longer matches the current UTC date, inside the same function.

**Confirmed tier status:** there is no tier-differentiated limit anywhere in this code. All limits are flat environment-variable constants applied identically to every member. A search of the codebase found no "Krovos+" implementation of any kind, consistent with `CLAUDE.md`'s pricing table listing Krovos+ as "TBD, after Core is fully built." **Confirmed: no higher-tier Guide access exists today; every subscriber has the same limits.**

**A confirmed surprise-avoidance gap:** `app/krovos-guide/page.tsx` fetches and stores `dailyTurns`/`dailyLimit` from the API response (the GET route returns both), but the page's rendered UI never displays them. The member-facing usage line only ever shows "`{turns}` of `{monthlyLimit}` Guide responses used this month," with warning text appearing at 80% and 90% of the *monthly* limit. There is no equivalent daily-limit indicator anywhere in the UI. A member could exhaust the daily limit (default 20) with no warning at any point, then receive a generic `429` response reading "Guide limit reached. Your access resets automatically," which does not state whether the daily or monthly limit was the cause, nor when access actually resets. This is distinct from the separate cost-cap message, "Krovos Guide has reached its monthly service allowance. Contact support if you need help with an active decision," which uses different wording for what could look, to a member, like the same kind of stop. **This is a real, code-confirmed gap in "can a member understand remaining access without surprise," not a documentation issue.**

---

## 4. Empty, Unavailable, Rate-Limited, Failed-Generation, and Privacy-Boundary States

- **Empty state (confirmed):** a member with no conversation history sees "Hello, `{displayName}`." followed by "Ask me anything. I already know your situation. You never have to re-explain." This exact copy renders identically whether the member has a rich Life Graph or none at all; the underlying `buildLifeGraphSummary()` falls back to the literal string `"Life Graph not yet built."` when no `life_graph` row exists, but the UI's greeting does not vary to reflect that. **This is a real overpromise in the empty state**: a brand-new member with zero Life Graph data still sees "I already know your situation," which is not yet true for them.
- **Unavailable/gated state (confirmed):** `/krovos-guide` and `/api/krovos-guide` are not listed in `lib/publicPaths.ts`, so `proxy.ts`'s middleware requires both authentication and an active subscription state (`active`, `trialing`, `founder`, `connected_user`, or unexpired gift access) before either the page or the API route is reachable; an unauthenticated visitor is redirected to sign-in, and an authenticated member without qualifying access is redirected to `/subscribe`. This middleware check runs before the API route handler executes, so a lapsed subscriber cannot reach the underlying endpoint directly, only through the same gated path as the page. **Confirmed safe.**
- **Rate-limited state (confirmed, with the gap noted above):** a `429` is returned with a plain-language message in both the cost-cap and turn-limit cases. The retry-affordance in the UI (Section below) does not apply to a rate-limit response the same way it does to a genuine failure, since the message correctly tells the member to wait rather than offering an immediate retry button for this specific case (the UI's generic error banner and "Try again" button do render for any non-OK response, including a 429, which means a member could tap "Try again" immediately after being told to wait, only to be blocked again; this is a minor UX inconsistency, not a data-safety issue).
- **Failed-generation state (confirmed built correctly):** on a network failure or non-OK response, `sendMessage()` in `app/krovos-guide/page.tsx` restores the message list to its prior state (removing the optimistically-added user message from view), sets a visible error banner with the actual error text, and offers a "Try again" button that resends the exact failed content. The conversation is never left in a state that could be mistaken for a received reply. **Confirmed matches the product's own stated design principle** (from the canonical current-state record) that a failed request must never render as if it succeeded.
- **Privacy-boundary state (confirmed):** the API route loads `life_graph` filtered strictly to `.eq('user_id', userId)`, the calling member's own row only. There is no join, merge, or fetch of a connected household partner's Life Graph anywhere in this route, even when `financial_shared` is enabled elsewhere in the product. **Confirmed: Krovos Guide does not combine a connected partner's data into any member's Guide context.**

---

## 5. Can Krovos Guide Personalize Without Exposing Raw Documents, Credentials, Government IDs, Payment Data, or Partner-Private Information?

**Confirmed yes, for everything the application controls automatically:**
- Raw uploaded documents are never read or referenced by `buildLifeGraphSummary()` or the tool-context allowlist.
- Government ID numbers, account credentials, and authentication tokens are not present anywhere in the `life_graph` fields this code touches, and are not part of the Anthropic request.
- Payment/billing data (Stripe customer IDs, card details, `gift_access_end`, `discount_category`, and similar `profiles` fields) is fetched into server memory (`select('*')` on `profiles`) but never serialized into the Anthropic request; only `preferred_name`/`full_name` are read from that object.
- Partner-private data is not exposed across household connections, per Section 4's privacy-boundary finding.

**One confirmed exception, already disclosed correctly:** the member's own typed message text is sent to Anthropic exactly as submitted, with no automatic redaction. If a member types a Social Security number, an account number, or a raw document excerpt directly into the chat box, that text is not filtered before being sent. The privacy page discloses this plainly ("message text is processed as submitted") rather than claiming a protection that doesn't exist. **This is an accurate disclosure of a real, structural limitation, not a hidden gap**, but it does mean the honest answer to "can the Guide guarantee this never happens" is no, only that Krovos does not *automatically* introduce that data itself.

---

## 6. Does Conversation Storage and Retention Match the Stated Product Promise?

**Confirmed, from `supabase/migrations/20260722171000_krovos_guide_memory.sql`:** `guide_conversations` and `guide_messages` both have `on delete cascade` foreign keys to `auth.users`. This means deleting a member's Supabase auth user row would structurally cascade-delete every conversation and message they have with Krovos Guide, which architecturally supports the privacy page's account-deletion promise. **This audit did not separately trace the actual account-deletion API route to confirm it deletes the `auth.users` row itself** (that would require reading a different part of the codebase not in scope for this Guide-specific audit); the cascade relationship is confirmed, but whether it is actually triggered by account deletion today is an assumption, not a verified fact, and should be checked directly before this is represented as fully confirmed.

**A confirmed granularity gap, not a contradiction:** the product exposes an "Archive" action for a conversation, which only sets `archived_at` (removing it from the default conversation list) and does not delete the conversation or its messages. There is no member-facing "delete this one conversation" action anywhere in the reviewed code. The privacy page's retention promise is written at the account level ("your personal data and uploaded documents will be permanently removed within 30 days" upon account deletion), so this is not a broken promise, but a member who wants to remove one specific sensitive conversation without deleting their entire account has no way to do so today.

**Storage duration otherwise:** conversations and messages persist indefinitely (no TTL, no automatic expiry) as long as the account exists, which matches the product's own "sustained relationship, not a one-time report" framing, and matches the privacy page's implicit promise that Guide history remains available to the member across sessions.

---

## 7. Mismatch Between the "Krovos Already Knows Your Life" Model and the Actual Context the Guide Receives

**This is the most significant finding in this audit, and it is fully code-confirmed, not an assumption.**

`lib/krovos-guide-starters.ts` detects a wide range of life-phase signals directly from the member's full Life Graph (fetched client-side via `useLifeGraph`), including `business_data`, `immigration_costs`/`partner_visa_type`/`visa_type`, `aging_parent`, `pension_data`, `remittance`, `life_insurance`, `partner_rsus`/`partner_commission`, and more. Based on these signals, the page surfaces specific, highly personalized starter questions, for example: *"My partner is on a visa and cannot work yet, how do we budget on one income?"*, *"My parent may need memory care soon, how do I start planning for that cost?"*, and *"How does my pension affect my Social Security benefit and what is WEP?"*

**None of these signals, immigration/visa status, aging-parent/caregiving context, pension data, business ownership, life insurance, or RSU/commission income, are included in `buildLifeGraphSummary()`**, the function that actually builds what gets sent to Anthropic (Section 1). A member who clicks the visa-budgeting starter question sends that exact text to Claude, but the model's system prompt contains no visa, immigration, or partner-work-authorization data at all; it only ever receives household composition, income, housing, debt, investments, retirement goals, and spending. The same is true for the pension, elder-care, and business-owner starter questions.

**This directly contradicts the Guide's own first stated principle in its system prompt**: *"You already know this person. Never ask them to re-explain their situation."* For any member whose relevant context is immigration, caregiving, a pension, a business, or insurance/RSU income, that principle is not actually true today, because the specific data that would make it true is never sent. The model would either have to answer generically, guess, or ask the member to explain, all three of which the product's own design explicitly says should never happen.

**This is a real product gap, not a documentation gap.** The privacy page's description of what's sent (Section 2) is accurate about what *is* sent; the mismatch is between the starter-question feature's apparent personalization and the system prompt's actual, much narrower content, not between the privacy page and the code.

---

## 8. Prioritized Beta-Readiness Checklist

### Real gaps (code-confirmed, require an implementation decision, not just a documentation fix)

1. **The starter-questions-to-system-prompt mismatch (Section 7).** Highest priority: this is the one finding that could actively make Krovos Guide feel like it "doesn't know" a member, for exactly the personas (immigration, caregiving, business owner, pension) the product's own life-phase framework is built to serve well. Fixing this means extending `buildLifeGraphSummary()` to include these fields, not merely relabeling the starter questions.
2. **No daily-limit visibility in the UI (Section 3).** A member can hit the daily cap with zero warning, then receive a generically-worded 429 that doesn't distinguish daily-limit, monthly-limit, or cost-cap causes.
3. **The `decision` field bypasses the tool-outcome allowlist (Section 1).** A narrower gap than the headline one, but a real exception to the "allowlisted summary only" design as currently commented in the code.
4. **The empty-state greeting overpromises for a member with no Life Graph (Section 4).** "I already know your situation" renders identically regardless of whether that's true yet.
5. **No per-conversation deletion (Section 6).** Not a broken promise, since the privacy page speaks at the account level, but a real capability gap worth a founder decision before beta, given how personal Guide conversations can be.
6. **The rate-limit "Try again" affordance doesn't distinguish a 429 from a genuine failure (Section 3).** Low severity, easy to fix alongside item 2.

### Confirmed-safe behavior (do not need further work to be beta-ready as-is)

7. Raw documents, government IDs, credentials, and payment/billing data are structurally excluded from what's sent to Anthropic.
8. A connected household partner's Life Graph is never combined into another member's Guide context.
9. The privacy page's AI/Guide-specific language accurately matches the actual code behavior, on every claim checked.
10. Subscription and authentication gating correctly protects both the page and the underlying API route via middleware, before either is reachable.
11. Failed-generation handling correctly preserves the prior conversation state and never renders a failure as a successful reply.
12. Conversation and message deletion is structurally supported via cascade-delete from the auth user row (pending the one assumption noted in Section 6).

### Documentation-only gaps (no code change needed, only a written clarification)

13. **Confirm, in a follow-up check outside this audit's scope, that the account-deletion flow actually deletes the `auth.users` row**, so the cascade-delete architecture in Section 6 can move from "structurally supported" to "fully confirmed." This is a verification task, not a code change, unless the check reveals the deletion flow doesn't actually remove that row.
14. **Consider adding one sentence to member-facing Guide copy** (not the privacy page, which is already accurate) clarifying that Guide responses depend on what's in a member's Life Graph today, so a member with a sparse Life Graph understands why answers may feel more general at first, until item 1 above is addressed or as an interim mitigation if it is not addressed before beta.

**No item in this checklist was acted on by this audit.** Every finding reflects read-only inspection of the code as committed, not a change made during this review.
