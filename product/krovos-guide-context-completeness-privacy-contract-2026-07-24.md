# Krovos Guide Context Completeness and Privacy Contract

**Superseded, in part, July 24, 2026:** app commit `9d7e6bf` ("Complete Krovos Guide life context") shipped four of the five flags this document's Section 3 recommended as a follow-up batch: remittance, expecting a child, life-insurance/estate, and staged retirement. Section 1's table rows for those four signals, and Section 3's "five-flag" framing, no longer reflect current code and should not be read as current. The definitive, reconciled matrix is `product/krovos-guide-starter-coverage-audit-2026-07-24.md`, which also investigates the expecting-child accuracy concern this document flagged (confirmed still present) and the dead starter-question paths. This document's original content is left in place below as the historical record of what was recommended and why; only `benefits_open_enrollment` remains an open candidate from its original five-item list.

**Date:** July 24, 2026
**Purpose:** For every starter-question trigger in `lib/krovos-guide-starters.ts`, confirm whether the underlying signal actually reaches what Krovos Guide sends to Anthropic, and define the minimum safe context flag for any that don't. This is read-only inspection plus a design contract; it does not implement anything. No app code, prompts, database, API settings, or production services were changed to produce it.

**Method:** direct inspection of `app/api/krovos-guide/route.ts` (current `main`, including the "RELEVANT LIFE CONTEXT" block added in commit `89a635e`), `lib/krovos-guide-starters.ts` in full, `app/privacy/page.tsx`, `product/krovos-guide-beta-readiness-audit-2026-07-24.md`, `product/guide-personalization-contract-2026-07-24.md`, and `/Users/christine.smith/mcp/csuite/context/3_current_state.md`.

**What's already confirmed built, restated briefly:** commit `89a635e` added a `RELEVANT LIFE CONTEXT` block to the system prompt covering business ownership, FIRE planning, wedding planning, immigration/work-authorization, aging-parent caregiving, college planning, pension planning, variable/RSU household income, and self-employed/gig income. This closed the audit's headline mismatch. The table below covers every trigger in the starters file, not only what's new, so this document stands on its own as the complete reference going forward.

---

## 1. Every Starter-Question Trigger, Reach, and Minimum Flag

| Life Graph signal | Starter phase(s) | Reaches Guide context today? | Minimum flag needed | Approval status | Representable without raw docs/IDs/medical detail/credentials/account numbers? |
|---|---|---|---|---|---|
| `financial.business_data` | `business_owner` | **Yes** ("business ownership planning is active") | Already implemented | **Already approved** (shipped, commit `89a635e`) | Yes, confirmed, a boolean-derived phrase, no business financials beyond what's already in the DEBT/INVESTMENTS sections |
| `financial.fire_planning` | `fire` | **Yes** ("financial-independence planning is active") | Already implemented | **Already approved** (shipped) | Yes |
| `financial.wedding_planning` | `wedding` | **Yes** ("wedding planning is active") | Already implemented | **Already approved** (shipped) | Yes |
| `financial.immigration_costs` / `family.partner_visa_type` / `family.visa_type` | `immigration` | **Yes** ("immigration or work-authorization planning is active") | Already implemented | **Already approved** (shipped) | Yes, no visa number, country, or document reference is sent, only the fact that this planning is active |
| `family.aging_parent` | `elder_care` | **Yes** ("caregiving for an aging parent is active") | Already implemented | **Already approved** (shipped) | Yes, no medical condition, diagnosis, or care-facility detail is sent, only the caregiving-context flag |
| `financial.college_planning` | `college` | **Yes** ("college planning is active") | Already implemented | **Already approved** (shipped) | Yes |
| `financial.pension_data` | `pension` | **Yes** ("pension planning is active") | Already implemented | **Already approved** (shipped) | Yes, pension existence is flagged; no plan administrator, account number, or benefit-statement detail is sent |
| `financial.partner_rsus` / `financial.partner_commission` | `variable_income` | **Yes** ("variable household income is present") | Already implemented | **Already approved** (shipped) | Yes |
| `financial.my_income_types` includes `freelance_1099`/`self_employed`/`gig` | `gig_income` (and overlaps `business_owner`) | **Yes** ("self-employed or gig income is present") | Already implemented | **Already approved** (shipped) | Yes |
| `financial.benefits_open_enrollment` | `open_enrollment` | **No.** Not present anywhere in `buildLifeGraphSummary()` or the new context block. | `"benefits open enrollment is active"` | **Needs founder approval** | Yes, a boolean-derived phrase; no plan name, premium amount, or carrier detail required |
| `family.children[].expecting` | `expecting` | **No, and a related accuracy issue exists.** The children-mapping code (`children = (family.children \|\| []).map(c => ({ age: c.dob ? calculateAge(c.dob) : null }))`) has no branch for `expecting`, so an expecting entry (which typically has no `dob`) renders as `"child N (age unknown)"` in the HOUSEHOLD section, misrepresenting an unborn child as an existing child of unknown age. | `"expecting a child"` as its own flag, separate from the children list, plus a corrected children-count that excludes expecting entries (matching the pattern `hasKids` already uses correctly in `detectLifePhase()`) | **Needs founder approval** (a genuinely sensitive life category, deserving explicit sign-off even though it matches the sensitivity level of already-approved items like `aging_parent`) | Yes, a boolean flag with no due date, no medical detail, no pregnancy-specific information beyond the fact itself |
| `financial.remittance` | *(detected but currently unused, see Section 2)* | **No.** Not present in the context block. | `"the household sends recurring financial support to family abroad"` | **Needs founder approval** | Yes, no country, recipient name, or amount is required, only the pattern's existence; CLAUDE.md's own language guidance ("replace 'remittance' with plain language") should govern the actual wording chosen |
| `financial.life_insurance` (part of the `estate` trigger) | `estate` | **No.** Neither `life_insurance` nor a general "estate planning is relevant" flag is in the context block, even though `hasKids` (part of the same trigger condition) is already visible indirectly via the children list, and `business_data` (also part of the same condition) is already sent. | `"estate planning is relevant (dependents and/or existing life insurance on file)"` | **Needs founder approval** | Yes, a boolean-derived phrase; no policy number, carrier, or coverage amount is required |
| `financial.three_phase_retirement` | `multi_phase_retirement` | **No.** Not present in the context block. | `"a staggered, multi-phase retirement plan is active"` | **Needs founder approval** | Yes |
| `financial.debts.length >= 2` / `>= 4` | `debt_focus` / `debt_crisis` | **Effectively yes, via underlying data, not a phase label.** The DEBT section already lists every debt's type, balance, rate, and payment, so Claude can already infer debt volume and severity directly from the figures themselves. | None needed; the raw debt data already carries more signal than a phase label would add | **No action needed** | N/A |
| Age bands (`early_career`/`building`/`optimization`/`pre_retirement`) | Same names | **Effectively yes, via underlying data.** `myAge` is already sent in the HOUSEHOLD section ("User age: X"), which is sufficient for Claude to infer the same life-phase banding `detectLifePhase()` computes. | None needed | **No action needed** | N/A |
| `financial.debts.length === 0` (`debt_free`) | *(detected but unused, see Section 2)* | **Effectively yes, via underlying data.** An empty DEBT section already communicates this. | None needed | **No action needed** | N/A |

**No signal in this table falls into "must never be sent."** Every gap identified is a high-level, boolean-derived planning-context flag, structurally identical in shape and sensitivity to the six categories already shipped and approved in commit `89a635e`. None require a raw document, an identifier, a medical detail, a credential, or an account number to implement.

---

## 2. Two Additional Findings, Not Directly Asked For But Directly Relevant

1. **Three detected phases produce no starter questions at all, a dead code path, not a privacy issue.** `detectLifePhase()` can push `'remittance'`, `'pre_retirement'`, and `'debt_free'` onto its returned array, but none of the three exists as a key in `startersByPhase`, and none is listed in `getStarterQuestions()`'s `priorityOrder`. A member who triggers only these signals (for example, someone with `financial.remittance` set but none of the other 15 real phase keys) would compute a phase list that contributes zero starter questions, silently. This should be fixed alongside the context-flag batch below, since `remittance` is one of the flags being added to context in this same pass, it would be an odd inconsistency to teach the Guide about remittance in its context while still surfacing no starter question for it.
2. **The `im2` starter question ("We send money to family abroad every month...") already exists under the `immigration` phase and is tagged `category: 'remittance'`, but is only ever shown to a member who also triggers the `immigration` phase specifically**, not to a member who has `financial.remittance` set without any immigration signal (for example, a household supporting family abroad with no immigration-status complexity of their own). This is a real, narrow discoverability gap the remittance flag fix below should also resolve.

---

## 3. Recommended Smallest Safe Follow-Up Batch

All five items below extend the exact same array-and-pattern already shipped in `app/api/krovos-guide/route.ts`'s `lifeContexts` block, adding entries in the same shape (`condition ? 'plain-language phrase' : null`), not a new mechanism:

1. Add `financial?.benefits_open_enrollment ? 'benefits open enrollment is active' : null`.
2. Add `family?.children?.some((c: any) => c.expecting) ? 'expecting a child' : null`, and correct the children-mapping to exclude expecting entries from the age-based children list (matching `detectLifePhase()`'s existing `hasKids` pattern), so an unborn child is never rendered as "child N (age unknown)."
3. Add `financial?.remittance ? '[the household sends recurring financial support to family abroad, exact wording pending Christine's review of CLAUDE.md's plain-language guidance]' : null`.
4. Add `(financial?.life_insurance || (family?.children || []).some((c: any) => !c.expecting)) ? 'estate planning is relevant' : null` (note: `business_data` is already covered by the existing business-ownership flag, so it does not need to be repeated in this new condition).
5. Add `financial?.three_phase_retirement ? 'a staggered, multi-phase retirement plan is active' : null`.

**Paired, small fix, same batch:** add `startersByPhase.remittance` and `startersByPhase.pre_retirement` and `startersByPhase.debt_free` entries (or, for `pre_retirement`/`debt_free` specifically, confirm deliberately that no dedicated starter set is wanted and remove the dead phase-push instead, whichever Christine prefers), and add `remittance` to `priorityOrder`, so the newly-context-aware remittance signal also produces a visible starter question for a member who has it without also triggering `immigration`.

This is a genuinely small, low-risk batch: five new boolean-derived phrases in an already-proven pattern, plus a handful of lines restoring three currently-dead phase branches. No new Life Graph fields are required, no new data collection, no new API surface.

---

## 4. Privacy Wording Implications

**Confirmed: no privacy-page wording change is needed for this batch.** `app/privacy/page.tsx`'s existing language, "Krovos Guide sends the messages you enter, a limited structured selection of relevant Life Graph facts, recent conversation context, and saved Krovos outcomes to Anthropic," is general enough to already cover these five additions without amendment, since it does not enumerate specific fields either for what's already shipped or for what's proposed here. The same page's separate statement, "Krovos does not automatically add raw documents, government identification numbers, account credentials, or authentication information to Guide context," remains accurate after this batch, since none of the five proposed flags touch any of those categories.

**One wording question worth a deliberate decision, not a silent choice:** the `remittance` flag's exact phrasing. `CLAUDE.md`'s own language standard says to "replace 'remittance' with plain language (e.g. 'monthly support to Lucas' family in Brazil')" for report-facing copy. Whether that same substitution should govern the internal context phrase sent to Anthropic (which the member never reads directly) or whether a more clinical internal label is acceptable for a string that exists only inside a system prompt is a small, low-stakes wording call, included in Section 3's founder-approval list rather than decided here.

---

## 5. Beta Acceptance Tests

For each of the five proposed flags, using a disposable test account:

1. **Open enrollment:** set `financial.benefits_open_enrollment` on a test account, open a fresh Krovos Guide conversation, and confirm the model's response to a general "what should I be thinking about right now" question references the open-enrollment window without the member having typed anything about benefits first.
2. **Expecting:** add an expecting child entry with no `dob`, confirm the HOUSEHOLD section of the (server-side, not member-visible) context no longer renders it as "child 1 (age unknown)," and confirm a Guide conversation demonstrates awareness of the pregnancy without the member re-explaining it.
3. **Remittance:** set `financial.remittance`, confirm the starter-question fix in Section 3 surfaces a relevant starter even without an immigration signal also present, and confirm the Guide's response references ongoing support to family abroad using approved plain language, not the raw internal label.
4. **Estate/life insurance:** set `financial.life_insurance` on an account with no children and no business, confirming the flag fires independently of the other two conditions in the same trigger, and confirm the Guide's response references estate-planning relevance appropriately.
5. **Multi-phase retirement:** set `financial.three_phase_retirement`, confirm the Guide references a staggered retirement plan without the member having to describe their pension-bridge situation from scratch.

**Cross-cutting test for all five:** confirm, via a direct inspection of the request sent to Anthropic (not just the model's reply), that only the plain-language phrase appears for each flag, never a raw document reference, an account number, a policy number, an amount tied to a specific account, or any identifier beyond what the six already-shipped flags already demonstrate is safe.

---

**No item in this contract has been implemented, approved, or acted on.** Every flag in Section 3 is a proposal pending Christine's approval; every "already approved" label in Section 1 refers to the six flags confirmed shipped in commit `89a635e`, not to anything new in this document.
