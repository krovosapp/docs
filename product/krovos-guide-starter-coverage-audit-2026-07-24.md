# Krovos Guide Starter Coverage Audit

**Date:** July 24, 2026
**Purpose:** A findings-only reconciliation of every Life Graph signal that creates, or should create, a Krovos Guide starter question, verified against the current `krovos` `main` branch, including commit `9d7e6bf`. This supersedes the parts of `product/krovos-guide-context-completeness-privacy-contract-2026-07-24.md` that no longer match shipped code, without deleting that document, it remains the historical record of what was recommended and why. No app code, prompts, database, API configuration, or production systems were changed to produce this document.

**Method:** direct inspection of `app/api/krovos-guide/route.ts` (current `main`, at commit `ea481b6`, including commit `9d7e6bf`, "Complete Krovos Guide life context") and `lib/krovos-guide-starters.ts` in full, both read line by line, not summarized from an earlier pass.

---

## 1. The Definitive Current Matrix

Every signal `detectLifePhase()` in `lib/krovos-guide-starters.ts` can produce, matched against whether a starter question exists for it and whether its high-level context reaches Anthropic today.

| Signal / detector | Starter question shown | Reaches Guide context today | Accurate and privacy-safe | Dead path (no starter question) |
|---|---|---|---|---|
| `financial.business_data` | `business_owner` (`bo1`-`bo5`) | Yes, `'business ownership planning is active'` | Yes | No |
| `financial.fire_planning` | `fire` (`fi1`-`fi5`) | Yes, `'financial-independence planning is active'` | Yes | No |
| `financial.wedding_planning` | `wedding` (`w1`-`w4`) | Yes, `'wedding planning is active'` | Yes | No |
| `financial.immigration_costs` / `family.partner_visa_type` / `family.visa_type` | `immigration` (`im1`-`im4`) | Yes, `'immigration or work-authorization planning is active'` | Yes | No |
| `family.aging_parent` | `elder_care` (`el1`-`el4`) | Yes, `'caregiving for an aging parent is active'` | Yes | No |
| `financial.college_planning` | `college` (`co1`-`co4`) | Yes, `'college planning is active'` | Yes | No |
| `financial.benefits_open_enrollment` | `open_enrollment` (`oe1`-`oe4`) | **No.** Still absent from the context block. | N/A, not sent | No, the starter question exists; only the context flag is missing |
| `family.children[].expecting` | `expecting` (`ex1`-`ex4`) | **Yes, as of commit `9d7e6bf`**, `'a new child is expected'` | **Partially.** The new flag itself is accurate and privacy-safe. A separate, still-present rendering issue means the same expecting entry is also shown elsewhere as an ambiguous child of unknown age, see Section 3. | No |
| `financial.partner_rsus` / `financial.partner_commission` | `variable_income` (`vi1`-`vi3`) | Yes, `'variable household income is present'` | Yes | No |
| `financial.debts.length >= 2` | `debt_focus` (`df1`-`df3`) | Effectively yes, via the raw DEBT section figures already sent, no phase-label needed | Yes | No |
| `financial.debts.length >= 4` | `debt_crisis` (`dc1`-`dc4`) | Same as above | Yes | No |
| `financial.remittance` | *(none, see Section 4)* | **Yes, as of commit `9d7e6bf`**, `'regular family support or remittance planning is active'` | Yes | **Yes.** No `startersByPhase.remittance` key exists and `'remittance'` is absent from `priorityOrder`. |
| `financial.pension_data` | `pension` (`pe1`-`pe4`) | Yes, `'pension planning is active'` | Yes | No |
| `financial.my_income_types` includes `freelance_1099`/`self_employed`/`gig` | `gig_income` (`gi1`-`gi4`) | Yes, `'self-employed or gig income is present'` | Yes | No |
| `financial.three_phase_retirement` | `multi_phase_retirement` (`mr1`-`mr4`) | **Yes, as of commit `9d7e6bf`**, `'a staged retirement timeline is active'` | Yes | No |
| Age 40-54 | `optimization` (`op1`-`op4`) | Effectively yes, `myAge` is sent directly in the HOUSEHOLD section | Yes | No |
| Age 28-39 | `building` (`bu1`-`bu3`) | Effectively yes, same reasoning | Yes | No |
| Age under 28 | `early_career` (`ec1`-`ec4`) | Effectively yes, same reasoning | Yes | No |
| Age 55+ | *(none, see Section 4)* | Effectively yes, `myAge` is sent directly | Yes | **Yes.** No `startersByPhase.pre_retirement` key exists and `'pre_retirement'` is absent from `priorityOrder`. |
| `financial.debts.length === 0` | *(none, see Section 4)* | Effectively yes, an empty DEBT section already communicates this | Yes | **Yes.** No `startersByPhase.debt_free` key exists and `'debt_free'` is absent from `priorityOrder`. |
| `family` has non-expecting children, or `financial.life_insurance`, or `financial.business_data` | `estate` (`es1`-`es4`) | **Yes.** `financial.life_insurance` now shipped as `'life-insurance or estate planning is active'` (commit `9d7e6bf`); the children-count and business-ownership legs of this same trigger condition were already visible via other means before this commit. | Yes | No |

**Summary: 21 distinct signal paths audited. Eighteen have both a working starter question and correct Guide context. One (`benefits_open_enrollment`) has a starter question but no context flag. Three (`remittance`, `pre_retirement`, `debt_free`) have context or equivalent underlying data but no starter question at all.**

---

## 2. Post-`9d7e6bf` Reality, Verified Precisely

`git show 9d7e6bf` confirms exactly four lines added to the `lifeContexts` array in `app/api/krovos-guide/route.ts`, nothing else in the file changed:

```
financial?.remittance ? 'regular family support or remittance planning is active' : null,
family?.children?.some((child) => child.expecting) ? 'a new child is expected' : null,
financial?.life_insurance ? 'life-insurance or estate planning is active' : null,
financial?.three_phase_retirement ? 'a staged retirement timeline is active' : null,
```

**Confirmed: expecting child, remittance, life insurance/estate, and staged retirement are all now shipped in Guide context**, exactly as the reconciliation directive states. **Confirmed: `benefits_open_enrollment` is the only remaining candidate context flag** from the prior contract's original five-item list.

### Is `benefits_open_enrollment` worth adding?

**Yes, using the same strict standard already applied to every flag above.** The proposed addition is a single boolean-derived phrase, matching the exact pattern already used eleven times in the same array:

```
financial?.benefits_open_enrollment ? 'benefits open enrollment is active' : null,
```

This requires no benefit-plan document content, no medical detail, no carrier or plan name, and no identifier or account information, only the fact that the window is active. It is structurally identical in shape and sensitivity to every already-shipped flag in this file. The starter question set for this phase (`oe1`-`oe4`) already exists and already works; only the context flag is missing, meaning a member who clicks one of these four starter questions today still has to explain their own open-enrollment situation to the Guide from scratch, exactly the "never re-explain" violation this whole line of work exists to close.

---

## 3. The Expecting-Child Accuracy Concern, Investigated

**Does the Guide still render an unborn child as "child N (age unknown)"? Yes, confirmed, this is still present in the current file.**

Two separate pieces of code handle an expecting child, and only one of them was touched by commit `9d7e6bf`:

1. **The new context flag** (line added by `9d7e6bf`): `family?.children?.some((child) => child.expecting) ? 'a new child is expected' : null`. This correctly and accurately tells the Guide a child is expected.
2. **The unchanged children-list rendering**, present before and after `9d7e6bf` with no modification:
   ```
   const children = (family.children || []).map((c: any) => ({
     age: c.dob ? calculateAge(c.dob) : null,
   }))
   ```
   rendered later as `Children: ${children.length > 0 ? children.map((c, index) => \`child ${index + 1} (age ${c.age ?? 'unknown'})\`).join(', ') : 'none'}`.

Because an expecting entry typically has no `dob`, it still maps to `age: null` and still renders as `"child N (age unknown)"` in the HOUSEHOLD section, in the same request that separately and correctly states `"a new child is expected"` in the RELEVANT LIFE CONTEXT section. **The two statements are not contradictory, but they are redundant and imprecise together**: a member with one born child (age 5) and one expecting entry would produce `"Children: child 1 (age 5), child 2 (age unknown)"` alongside `"a new child is expected"`, requiring Claude to infer, rather than being told directly, that child 2 and the expected child are the same entry, or to wonder whether "age unknown" means a data-quality gap rather than a pregnancy.

**The exact minimal correction needed, not implemented here:** exclude expecting entries from the `children` array used for the HOUSEHOLD count and list, mirroring the filter `detectLifePhase()`'s own `hasKids` constant already applies correctly (`(family?.children || []).filter((c: any) => !c.expecting)`). Concretely, change the `children` mapping to filter on `!c.expecting` before mapping ages, so an expecting entry appears only once, in the RELEVANT LIFE CONTEXT section, and the HOUSEHOLD children count reflects only children who exist today. This is a one-line filter change to an existing `.map()` call, not a new mechanism.

---

## 4. Every Detected Phase With No Starter Question

### `remittance`
**Exact current behavior:** `detectLifePhase()` pushes `'remittance'` onto its returned array when `financial?.remittance` is set. No `startersByPhase.remittance` key exists, and `'remittance'` is absent from `priorityOrder`, so this phase contributes zero starter questions under any circumstance. Separately, the `immigration` phase's own `im2` question ("We send money to family abroad every month...") is tagged `category: 'remittance'`, but only surfaces for a member who also triggers `immigration`, never for a member with `financial.remittance` set on its own.
**Real gap or intentionally appropriate:** a real member-value gap. Sending money to family abroad is a common circumstance independent of immigration status (for example, a member who is a citizen but supports family in another country), and the signal now reaches Guide context (Section 2) with no matching recognition moment on the starter-question surface.
**Recommended smallest addition, consistent with the white-glove model and no fear-based framing:** a small `startersByPhase.remittance` set, for example a generalized version of the existing `im2` question plus one or two adjacent ones (how recurring international transfers affect a budget, and whether there's a lower-cost way to send the same amount), added to `priorityOrder`. Reuses existing language and tone rather than inventing a new register.

### `pre_retirement`
**Exact current behavior:** any member aged 55 or older triggers `'pre_retirement'`. No `startersByPhase.pre_retirement` key exists, and `'pre_retirement'` is absent from `priorityOrder`. Because the four age bands (`early_career`, `building`, `optimization`, `pre_retirement`) are mutually exclusive by construction (each covers a disjoint age range), **a member aged 55 or older who triggers no other phase gets zero starter questions at all**, an empty starter list, not a fallback to a more general set.
**Real gap or intentionally appropriate:** a real, higher-severity gap than the other two dead paths in this section, precisely because it can leave an entire member segment, an uncomplicated household approaching retirement with no pension, no business, no immigration complexity, and no other triggering signal, with a blank starter-question screen on their very first Guide visit.
**Recommended smallest addition, consistent with the white-glove model and no fear-based framing:** a small `startersByPhase.pre_retirement` set focused on forward-looking, non-alarmist questions in the same register as the existing `optimization` set (for example, "am I on track for the retirement I want," "what should I be doing differently in these final working years," "how do I think about Social Security claiming timing"), added to `priorityOrder`. This is the highest-priority of the three dead paths to fix.

### `debt_free`
**Exact current behavior:** triggers when `financial.debts.length === 0` or `debts` is absent. No `startersByPhase.debt_free` key exists, and `'debt_free'` is absent from `priorityOrder`. Unlike `pre_retirement`, this phase does not correspond to a mutually-exclusive banding, a debt-free member still lands in exactly one age band (`early_career`, `building`, `optimization`, or `pre_retirement`), so being debt-free alone does not, by itself, produce an empty starter list, unless that member is also 55 or older, in which case both dead paths compound.
**Real gap or intentionally appropriate:** a genuine but lower-urgency gap. Being debt-free is a positive state worth acknowledging, not a problem to solve, which makes it a good natural fit for the product's own no-fear-based-copy standard, but its absence is less consequential than `pre_retirement`'s, since most debt-free members still see some starter questions from their age band.
**Recommended smallest addition:** optional, lower priority than the two above. If added, frame it entirely as forward-looking and positive (for example, "now that I don't have any debt, where should extra money go first," "how should being debt-free change my emergency fund or investing plan"), explicitly avoiding any framing that implies debt-free is unusual or that debt itself was a failure, consistent with the product's stated ban on judgment-toned copy.

---

## 5. Explicit Correction to the Prior Contract

`product/krovos-guide-context-completeness-privacy-contract-2026-07-24.md` has been updated with a superseding notice at its top, pointing here, rather than rewritten or deleted. Its Section 1 table rows for `remittance`, `expecting`, `life_insurance`/estate, and `three_phase_retirement`, and its Section 3 "recommended smallest follow-up batch" framed as five flags, no longer describe current code. This document's Section 1 above is the current, complete reference. The prior document's `benefits_open_enrollment` recommendation and its privacy-wording analysis (Section 4 there) remain accurate and are not repeated in full here; Section 2 above restates only the specific verification that flag remains the sole open item.

---

**No item in this audit has been implemented. Every recommendation in Sections 2, 3, and 4 is a proposal for a future session to build and for Christine to approve, not a change made here.**
