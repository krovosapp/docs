# Competitive Feature Opportunity Decision Memo

**Date:** July 24, 2026
**Purpose:** Audit three competitive-feature opportunities against the current Krovos codebase and canonical documentation, and recommend at most one to build first. This is a decision memo, not an implementation. No app code, migrations, settings, Stripe, Supabase, Vercel, or production systems were changed to produce it, and no canonical project-state file was updated.

**Method:** direct, read-only inspection of `/Users/christine.smith/krovos` (the Spending page, Paycheck Planner, Life Compass payday strip, Retirement Hub and its scenario workbench, Investment Priority, Roth Conversion Calculator, `lib/tax/`, Credit Card Optimizer, Life Labor's facts layer, and Life Calendar), cross-referenced against `/Users/christine.smith/mcp/csuite/context/3_current_state.md` and `docs/product/build-status-inventory-2026-07-23.md`. Krovos's manual-entry, no-bank-sync model is treated throughout as an intentional strategic choice, not a gap to close.

---

## 1. DollarWise: Low-Friction Budgeting and "Safe to Spend" Clarity

### What Krovos already has
- **`app/life-profile/spending/page.tsx`** (also serving as the ongoing "Budget & Spending" destination linked from Explore, a confirmed dual-purpose route per the discoverability audit): a four-framework adaptive budget (Stabilize First, Build and Grow, Optimize and Protect, Retirement Transition), auto-detected from life phase, age, and retirement proximity. Shows a real-time zero-based allocated-versus-unallocated total, a named tithing line, and conditional childcare/pet guidance.
- **`app/paycheck-planner/page.tsx`**: splits each paycheck across savings, bills, and goals, and restores the member's last saved allocation as a durable Core tool result.
- **`app/life-compass/page.tsx`**'s payday strip and the Life Updates flow (`/update`): a next-payday countdown and a payday reconciliation check-in (confirm or edit income and life changes).
- **`app/net-worth/page.tsx`**: a running total with historical snapshots.

### Truly missing versus renamed
A single, always-visible **"safe to spend" figure is genuinely missing**, not merely named differently. Krovos already has every input this would need (the active spending framework's unallocated amount, the paycheck allocation, and upcoming bills already surfaced on Life Calendar), but has never synthesized them into one moment-to-moment spendable number. This is the one real, buildable gap in this category.

### Overlap or collision risk
**High**, specifically with **Household Budget Framework**, which `3_current_state.md` and `docs/product/build-status-inventory-2026-07-23.md` both confirm is "completely unscoped" and explicitly speced-not-built. A "safe to spend" figure is very plausibly a component of whatever that framework eventually becomes. Building it now risks either duplicating work that framework will later need, or needing rework once that framework is actually scoped. Secondary overlap with Paycheck Planner (allocation) and Life Updates (payday reconciliation), risking a third, competing "budget surface" if not built as a synthesis of the existing two rather than a fourth new page.

### Core, paid guide, add-on, or out of scope
**Core**, if built. This is a daily-use utility relevant to every member regardless of life phase, which argues against gating it behind a paid guide, and against treating it as an optional add-on.

### User problem, benefit, privacy, data model, household behavior, smallest V1
- **User problem:** "can I afford this today without derailing my plan" currently requires a member to check two or three separate pages themselves.
- **White-glove/burden-relief fit:** strong in principle. Krovos already holds every input; burden relief means presenting the answer, not requiring the member to assemble it.
- **Privacy:** no new sensitive data category; reuses existing Life Graph financial fields. The one real privacy-adjacent requirement is honesty in framing: because Krovos has no bank sync, this figure must be clearly labeled as an estimate derived from what the member has told Krovos, never implied to be a live account balance.
- **Data model:** no new table required for a first version; a derived, computed value from existing `life_graph.financial`, the saved Paycheck Planner allocation, and Life Calendar's already-derived bill overlay.
- **Household behavior:** should follow the same combined-versus-separate-finances branching Retirement already uses, not a new sharing model.
- **Smallest credible V1:** one read-only card on Life Compass and/or the Budget & Spending page: the active framework's unallocated amount, minus any known bill due before the next payday, minus any goal contribution due, labeled explicitly as an estimate. No new tool page, no new AI credit.

### Timing
**After beta.** Not a blocker, and Household Budget Framework's undecided scope is a real reason to wait for at least one round of beta feedback on the existing Spending, Paycheck Planner, and Life Updates surfaces before adding a synthesized number that framework's eventual scope might require rebuilding.

---

## 2. WealthBridge: Deterministic Retirement Scenarios, Goal-Priority Solver, Tax-Cliff Visibility, Household Scenario Comparison

### What Krovos already has
This category has the strongest existing overlap of the three, most of it is already built, not missing.
- **`app/retirement/page.tsx`**: the Retirement Hub. Confirmed built: a three-scenario comparison holding the household's own entered target date(s) fixed rather than solving for an "optimal" one, a household bridge model (separate retirement dates, continuing income, funding gap, portfolio drawdown during the bridge), a "Seven So Whats" risk exercise (income loss, weak returns, inheritance uncertainty, slower debt payoff, a major one-time cost, staggered retirement, care/longevity spending), and sequential Just-me/Me+partner and combined-versus-separate-finance branching. Scored 98/A+ per the canonical record.
- **`app/investment-priority/page.tsx`**: already a working goal-priority solver, a saved "next-dollar" list across stability, debt, employer match, HSA, workplace plans, IRA paths, business-owner plans, partner opportunities, and taxable investing, and explicitly labels missing evidence rather than claiming an incomplete sequence is optimal.
- **`app/roth-conversion-calculator/page.tsx`**, **`app/social-security-comparison/page.tsx`**, **`app/rmd-tracker/page.tsx`**, **`app/retirement-withdrawal-planner/page.tsx`**: deterministic, already-live Core tools, all confirmed to have passed the July 22-24 input-safety correction pass.

### Truly missing versus renamed
Deterministic scenarios, the goal-priority solver, and household scenario comparison are **not missing**, they already exist under Krovos's own names. The one genuinely missing piece is **tax-cliff visibility**: a direct search of the codebase found no modeling or visualization anywhere of marginal-cliff thresholds (an IRMAA Medicare-premium tier, an ACA premium-subsidy cliff, or a capital-gains-bracket edge) where one additional dollar of recognized income causes a disproportionate cost jump. The only "cliff" references found in the codebase are equity-vesting cliffs (`app/offer-comparison/page.tsx`) and an explicit disclaimer inside Estate Tax Checker that state-level "cliff rules" are out of that tool's scope, neither is the tax-cliff visibility this opportunity describes.

### Overlap or collision risk
**Low** for the already-built scenario/solver/comparison capabilities, this is recognition of existing work, not new scope. **Moderate** for tax-cliff visibility specifically, since its natural home is an extension of the existing Roth Conversion Calculator and the shared `lib/tax/` engine, not a new parallel tool; building it as a standalone feature instead of an extension would fragment tax logic Krovos has already centralized once.

### Core, paid guide, add-on, or out of scope
**Core**, as an enhancement to an existing Core surface (Roth Conversion Calculator inside the Retirement Hub), matching how the Retirement Hub itself was built, Core, not gated, not a separate paid guide.

### User problem, benefit, privacy, data model, household behavior, smallest V1
- **User problem:** a near-retirement or retired household considering a Roth conversion or realizing a capital gain can cross a threshold that costs far more than the marginal tax on the dollar itself, for example, a small income increase pushing Medicare premiums up by thousands of dollars for a full year via IRMAA tiering, with no warning.
- **White-glove/burden-relief fit:** exactly the kind of consequence the Roth Conversion tool already promises to illuminate ("if you did X, here is what would happen"), currently unable to show for this specific, high-stakes case.
- **Privacy:** no new sensitive data; uses figures Roth Conversion Calculator already requires (income, filing status, capital gains).
- **Authoritative-source, testing, and legal-disclosure requirements, stated explicitly per this memo's own constraint:** IRMAA thresholds require sourcing from CMS.gov and change annually; ACA subsidy-cliff figures require sourcing from CMS/healthcare.gov, vary by state, household size, and plan, and have been subject to temporary legislative extensions that could lapse, meaning this figure carries more year-to-year uncertainty than IRMAA or the capital-gains brackets already partially represented in `lib/tax/tax-year-data.ts`. This needs the same "verified current-year figures, no stale-year data" discipline already applied to that file, plus an explicit "this is not tax advice, confirm with a licensed CPA before finalizing a conversion near a cliff" disclosure, matching the escalation-to-a-licensed-professional pattern the Roth Conversion tool and others already use. This feature should not ship without that sourcing and disclosure work completed and reviewed, not merely drafted.
- **Data model:** additive fields on `lib/tax/tax-year-data.ts` (IRMAA tiers, ACA subsidy-cliff figures); no new Life Graph fields beyond what Roth Conversion Calculator already collects.
- **Household behavior:** IRMAA and MAGI are household-level for a married couple; this must respect the combined-versus-separate-finances model Retirement Hub already implements, not introduce a second one.
- **Smallest credible V1:** a single "cliffs ahead" visual band on the Roth Conversion Calculator's existing result view, showing the nearest IRMAA tier and/or capital-gains bracket edge relative to the member's projected income, with the same disclosure language already used elsewhere in the tool. Not a new tool, not a new page.

### Timing
**After beta for tax-cliff visibility specifically**, since it requires real sourcing and legal-disclosure work before it should be trusted with a live figure. **No new work needed at all for the scenario/solver/comparison capabilities**, since they are already built and already beta-ready.

---

## 3. Thrifty Traveler Card Tracker: Manual, Privacy-First Household Card-Benefit/Deadline/Annual-Fee ROI Tracking

### What Krovos already has
**`app/credit-card-optimizer/page.tsx`**: card selection and ongoing usage optimization, gated on an explicit payment-behavior confirmation (a member carrying interest is routed to Debt Payoff Planner instead of receiving a rewards-card recommendation). This tool is about which card to use for which spending category and whether rewards-earning makes sense given payment behavior. It does not track a specific card's annual fee against the perks actually redeemed, renewal or anniversary dates, or benefit-expiration deadlines.

### Truly missing versus renamed
**Fully missing, not renamed.** No code anywhere in the repository tracks card-level annual-fee ROI, renewal deadlines, or benefit-usage. A direct search for "annual fee," "card benefit," "renewal deadline," and "benefit tracker" found no matches beyond Credit Card Optimizer's own generic fee-comparison logic, which is a different concept.

### Overlap or collision risk
**Low to moderate.** Modest conceptual overlap with Credit Card Optimizer (the same underlying object, a credit card, viewed through a different lens); should be cross-linked with that tool, not duplicate its rewards logic. Real, useful overlap with two already-built patterns worth reusing rather than colliding with: Life Calendar's confirmed-but-currently-unpopulated capacity to display a `guide_linked`-style dated action (per the Life Calendar and discoverability audits, this category is fully supported in the UI with no current producer, a card-renewal date would be a legitimate, additive producer for it), and Life Labor's household facts layer (already built as shared, household-level reference data, e.g. the Hermo.ai-inspired "facts" concept referenced in the canonical record), which is a better-fitting existing pattern for shared card data than inventing a new table and sharing mechanism from scratch.

### Core, paid guide, add-on, or out of scope
**A future Core add-on** (Money Basics cluster), if built, not a paid Life Phase Guide, since it is not tied to a specific life event or phase the way guides are scoped, and not out of scope, since it is a real, low-risk, buildable utility. It is the **lowest-priority** of the three relative to Krovos's Life Navigation thesis: a narrower, more transactional life-admin utility than the other two, closer to a nice-to-have than a "guide you were never given" moment.

### User problem, benefit, privacy, data model, household behavior, smallest V1
- **User problem:** cards accumulate welcome bonuses, annual fees, and perks; it is easy to forget a renewal date, miss redeeming a benefit before it expires, or keep paying a fee for a card whose perks are no longer being used.
- **White-glove/burden-relief fit:** fits the model well only if built as a proactive Life Calendar-linked nudge ("your card's annual fee posts in 30 days, have you used its perks this year"), not a static list nobody revisits.
- **Privacy, stated explicitly per this memo's own constraint against assuming bank sync:** this is, and should remain, a fully manual-entry feature. The member enters a plain-text card nickname (for example, "Chase Sapphire Reserve"), not a card number or last-four digits, an issuer/network label only if useful for perk-matching, an annual-fee amount, a renewal or anniversary date, and a short list of perks with a member-marked "used this year" state. No card number, no linked account, no live transaction or balance sync should ever be requested, matching Krovos's existing manual-entry model exactly rather than treating that model as a gap to close.
- **Data model:** one small new table (for example, mirroring the shape of Life Labor's existing facts table), with the fields above and nothing resembling an account identifier.
- **Household behavior:** naturally a shared household reference item, reusing the existing Life Labor facts-sharing toggle rather than a new sharing mechanism.
- **Smallest credible V1:** a simple list page (add a card: nickname, annual fee, renewal date, one to three perks with a used/not-used checkbox), one auto-generated Life Calendar entry per renewal date, and a plain, member-facing "fee justified this year?" comparison of perks used against the fee, explicitly a subjective, member-judged comparison, not a computed financial recommendation, so it carries none of the authoritative-source or disclosure requirements the tax-cliff feature above does.

### Timing
**Deferred**, clearly after beta and after the other two opportunities in this memo, since it has no existing partial infrastructure to build on (unlike the other two) and is the least aligned with what Krovos is currently proving out during beta.

---

## 4. Ranked Recommendation: Exactly One Candidate to Build First

None of the three belongs before beta; the current beta-launch backlog (account deletion, Daylight Lantern fixes on Life Graph and Retirement, Krovos Guide context gaps) takes priority over all of them, per `product/beta-launch-runbook-2026-07-24.md`. Among the three, ranked for whichever comes first after beta:

1. **Recommended first: WealthBridge's tax-cliff visibility, built as a single addition to the existing Roth Conversion Calculator.** This has the lowest collision risk of the three (it touches no unscoped, deferred framework), builds directly on Krovos's single most mature and highest-scored existing surface, has the smallest credible V1 of the three (one visual band on one existing tool, reusing an already-centralized tax engine and an already-established professional-escalation disclosure pattern), and carries the clearest white-glove payoff per unit of build effort, preventing a specific, potentially costly, hard-to-reverse mistake a member could not discover on their own. It explicitly requires the sourcing and legal-disclosure work named above before any real member relies on it, not before beta, but before this specific feature goes live.
2. **Second: DollarWise's "safe to spend" figure**, once Household Budget Framework's scope is actually decided, so this isn't built twice.
3. **Third, and lowest priority: the Thrifty Traveler Card Tracker**, a real but narrower utility with no existing partial infrastructure, best picked up only after the first two, or whenever Money Basics-cluster capacity is otherwise idle.

**No item in this memo has been built or acted on.** Every recommendation reflects read-only inspection of the code and canonical documentation as of July 24, 2026, not a change made during this review, and no canonical project-state file was updated.
