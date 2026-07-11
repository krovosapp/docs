# Krovos Open Items and Backlog

Single running source of truth for all open items, deferred decisions, and pending research that do not yet have a permanent home elsewhere in the docs. Check and update this document at the start and end of every working session, the same way CLAUDE.md is treated as living persistent context.

**Last updated: 2026-07-11 (Connected-user pricing model locked; discount and subscription lifecycle items added; storage bucket bug fixed; navigation-engine-tool-guide-mapping.md fully verified -- all speced-not-built tools corrected to built/live, Section 4 backlog count corrected to 0, missing routes added; tool-guide mapping health check item below closed)**

---

## Major Initiatives: Scoped but Not Yet Built

These are confirmed directions that have been thought through at the product/philosophy level but require dedicated technical scoping sessions before any build prompt is written.

---

### Connected-User Access Model

**Status: Conceptually scoped and pricing model locked. Technical and data-model scoping still required before any build work begins.**

This is a major upcoming initiative touching auth, billing, Life Calendar, Document Vault, Fair Play, and the financial profile. Do not hand to a builder without a dedicated technical scoping session first. The pricing and cost-control questions are now fully resolved (see locked decisions below); what remains open is the data-model, invite-flow, and permission-architecture design.

#### Locked decisions (do not re-litigate)

**One subscription price for all users, no separate household or family pricing tier, ever.** A subscriber may invite one connected user, scoped to a spouse or partner relationship. Per-category sharing toggles remain as scoped (Calendar and Fair Play shared by default, Document Vault per-item toggle, financial profile off by default with full view-and-edit support when enabled). Cost control, if needed, will come from a transparent monthly cap on AI-heavy actions -- Krovos Guide conversations and report regenerations being the two most likely candidates -- not from seat count or pricing tier. The specific cap number is intentionally not set yet; it should be calibrated against real usage data once live, not guessed at pre-launch.

#### Competitor research findings (two Perplexity passes, 2026-07-11)

**Personal finance category:** YNAB is the closest direct precedent. One subscription shared with up to six people including partners, at a single price, with no per-seat charge. This directly validates the one-price model for Krovos -- it is an existing, market-proven structure in the same product category, not an untested idea.

**Streaming services:** YouTube Premium and Spotify charge roughly 65-70% more for family tiers than individual tiers. This does not apply to Krovos. The streaming cost structure scales with concurrent content delivery; a collaborative planning tool's core value is shared workflow, not duplicated media consumption. Different economics, different model.

**AI usage caps:** Claude and ChatGPT both use transparent usage caps on paid tiers -- a stated conversation budget or message limit per rolling window -- as their cost-control mechanism, not seat-based restriction. This directly informs Krovos's approach: cap the actual cost driver (AI-heavy actions), not who can hold an account.

#### Confirmed direction

One subscription tier only. No separate household or family pricing tier. A subscriber can invite one connected user, scoped specifically to a spouse or partner relationship, not an open-ended multi-user invite. The connected user creates their own login. Access lapses automatically if the primary subscription lapses. Connected users have zero independent billing status.

#### Per-category sharing model

Access is governed by per-category sharing toggles controlled by the subscriber, not an all-or-nothing account-level permission:

| Feature | Default |
|---|---|
| Life Calendar | Shared by default -- the single-source-of-truth value depends on it |
| Document Vault | Per-item toggle, private or shared, already scoped in earlier session |
| Fair Play / household task assignment | Shared by default |
| Financial profile (Life Graph, net worth, budget, retirement, debt) | Off by default; when enabled, full view-and-edit access, not read-only |

The financial-profile sharing philosophy is aligned with Ramit Sethi's couples-planning framework: shared financial planning means joint management, not one partner observing the other.

#### Technical requirements (not yet scoped in detail)

- A genuine multi-account data model: a `households` or `connected_users` linking table, not a permissions flag on one account
- An invite flow: email invite, connected user creates their own Krovos login
- Object-level sharing logic across Calendar, Vault, and the financial profile
- Auth must remain platform-agnostic, not tied to Sign in with Apple or Google exclusively, so that identity is never coupled to a device or OS ecosystem if Krovos ships native iOS or Android apps

#### Explicitly ruled out

A household or family pricing tier separate from the single-user tier. Seat-based cost control. Decision preserved here so it is not re-litigated.

---

## Items Requiring Dedicated Scoping (not ready to build)

These tools are confirmed genuine market whitespace with no adequate competitive analogue. None has had a scoping session. None should be handed to a builder without one.

### 1. Immigration USCIS-Timeline Financial Modeling Tool

**Status: Built and live.** Three tools built 2026-07-10 and assigned to the Immigration and Finances guide:

| Tool | Route |
|---|---|
| Authorization-Gap Bridge Planner | /auth-gap |
| Path-to-Residency Cost Planner | /residency-costs |
| Credit-Building-From-Zero | /credit-building |

No further scoping needed. See navigation-engine-tool-guide-mapping.md for full descriptions.

---

### 2. Blended-Family Decision Engine

**Status: Built and live.** Two tools built 2026-07-10:

| Tool | Route |
|---|---|
| FAFSA Remarriage-Timing Planner | /fafsa-remarriage-timing |
| QTIP vs. Bypass Trust Decision Walkthrough | /qtip-bypass-decision |

Built as two separate tools. FAFSA tool shared with College Planning guide. QTIP/bypass tool shared with Estate Planning guide. No further scoping needed. See navigation-engine-tool-guide-mapping.md for full descriptions.

---

### 3. QDRO Navigator

**Status: Built and live.** Expanded to two tools built 2026-07-10:

| Tool | Route |
|---|---|
| QDRO Readiness Prep | /qdro-readiness |
| QDRO Tracker and Award Estimator | /qdro-tracker |

Build 1 is non-numeric (flagging and routing). Build 2 handles post-decree numeric estimation: defined-contribution award calculation, pension coverture fraction with range output (never a single number), IRA transfer guidance, government/military agency routing. Both assigned to Divorce and Transition guide. No further scoping needed.

---

## Architecture Questions Pending Decision

### 1. Dual-Retirement-Date Logic as Shared Infrastructure

**What is confirmed:** The SWR Calculator (built and live at /swr) now includes a complete dual-retirement-date mode with three-phase bridge-period modeling: active earning period, bridge period with partial income coverage, and full withdrawal phase. The logic includes bridge growth rate, partial/full coverage detection, early-withdrawal penalty warning (Rule of 55, SEPP/72(t)), and Life Graph pre-fill for partner name and retirement ages.

The Social Security Survivor Optimizer (backlog, not yet built) also involves two-person retirement date handling, since survivor benefits interact with claiming age for both the deceased and surviving partner.

**What is still open:** Whether the bridge-period and household-timeline modeling built into the SWR Calculator should be extracted into shared infrastructure and reused by CoastFI, any FreedomScenarios tool if built, and LeverAnalysis if built. Currently it lives only in app/swr/page.tsx as self-contained logic. Before any new tool that involves two retirement dates gets built, this overlap should be checked. The SS Survivor Optimizer design may benefit from or conflict with the SWR bridge model; this should be reviewed before the Optimizer is scoped.

**Decision needed next:** Before building any new two-person retirement-date tool, review the SWR bridge logic for reuse potential. Decide whether to extract it as a shared utility or keep tools self-contained.

---

### 2. Life Gaps as Financial Wellness Checkup

**Status: Resolved.** Life Gaps' context-aware 8-question flow, phase-opening recognition, and gap-card output already fulfill the financial wellness checkup function adequately. The tool identifies the user's life situation, surfaces the specific gaps most relevant to their circumstances, and routes to the appropriate next step. No enhancement and no separate internal checkup tool is needed. Closing this item.

---

## Business and Pricing Items

### Discount Tier Program

**Status: Identified, not yet researched or scoped.**

Student discount and profession-based discounts for first responders, nurses, teachers, and veterans. Legitimate profession-based discount programs require third-party verification rather than an honor-system self-report. Verification services include ID.me, SheerID, and similar platforms. The implementation approach (which service, what the discount amount is, whether student discount uses a .edu email check or a verification service) must be researched and confirmed before this is scoped for build. Do not assume a self-checkbox is sufficient.

---

### Subscription Lifecycle

**Status: Identified, not yet scoped or built.**

Three related items that need to exist before real users are onboarding at scale:

**Cancellation grace period.** Files and data should not be instantly deleted on payment failure or cancellation. A grace period is needed before any data scrubbing occurs. This is particularly relevant if Document Vault ships with real file storage, since a user should not lose uploaded wills or insurance policies because a card declined. Grace period length, retry logic, and the scrubbing process all need to be designed explicitly.

**Win-back email track.** A dedicated email sequence for lapsed or cancelled users, nurturing re-engagement and demonstrating ongoing product value. Distinct from the existing Group A/B/C active-user sequences. Timing and content to be designed based on churn analysis once real cancellations are available to study.

**Cancellation feedback survey.** A mechanism to capture why a user cancelled, surfaced in the cancellation flow rather than as a follow-up email. Both for product improvement and for early churn signal. Implementation options: embedded form in the cancel confirmation screen, or a short Typeform/Tally linked from the cancellation confirmation email.

---

## eMoney Research Findings (research pass: 2026-07-10, second pass: 2026-07-10)

**What is confirmed:** eMoney Advisor is genuinely superior for advisor-mediated planning depth across every dimension researched. Specific confirmed capabilities include the Decision Center scenario solvers (side-by-side what-if modeling for major financial decisions -- confirmed as an advisor-tier module, present in the product and not removed, simply not surfaced in client-facing navigation), the Vault (secure document repository with advisor and client access), and a tax-aware plan engine that models year-by-year tax impact of planning decisions. These capabilities are real, advisor-facing, and represent a high bar for the category.

**Dual-retirement-date gap: CONFIRMED whitespace.** First-party verification complete. The eMoney Earliest Retirement Age solver explicitly assumes Client and Spouse retire in the same year. There is no mechanism in the current product to model a household where two partners retire at different dates with a bridge period between them. This is a confirmed product gap, not a suspected one. The SWR Calculator's dual-date mode with three-phase bridge-period modeling represents genuine product differentiation with no current eMoney analogue.

**Status: Resolved.** No further research action needed. The dual-retirement-date bridge model is confirmed whitespace as of the second research pass.

---

## Remaining Tool Inventory Backlog

This section tracks tools from the Navigation Engine roadmap (krovos-navigation-engine-master-reference.md, Part 3) and the tool-guide mapping (navigation-engine-tool-guide-mapping.md) that have not yet been built or scoped. The Self-Employed Mode cluster (quarterly tax, retirement plan comparison, gig engine, runway) and the FI/FIRE Mode cluster (CoastFI, SWR) are complete. What remains:

### Phase 1: Financial Foundation (core product, not guide-specific)

| Tool | Status | Notes |
|---|---|---|
| Paycheck Allocation Tool | built/live at /paycheck-allocation | Three strategy cards (Conservative Stability, Balanced Growth, Aggressive FIRE). Dollar amounts per paycheck and monthly, benchmark vs current savings rate. Life Graph pre-fill. |
| Debt Optimizer | built/live at /debt-optimizer | Per-debt inputs, three strategies (Hybrid default, Avalanche, Snowball), extra payment, payoff order, interest saved vs minimums-only. Life Graph pre-fill from debt_data. |
| Savings Vault System | built/live at /savings-vaults | Named goals with target amounts and dates, progress bars, monthly contribution needed per vault, totals strip. Life Graph pre-fill from savings_balance. |
| Emergency Fund Calculator | built/live at /emergency-fund | 1/3/6 month milestones, progress bars, date projections at contribution rate, color-coded status. Life Graph pre-fill. Distinct from /emergency-fund-priority. |
| Net Worth Tracker | built/live at /net-worth | Confirmed real tool: uses calcNetWorth lib, Life Graph data, historical snapshots with timeline chart. |

### Phase 2: Household Operations (core product)

| Tool | Status | Notes |
|---|---|---|
| Document Vault | speced-not-built | Referenced across many guides. Core product. Requires connected-user access model to be technically scoped first if vault items will be shareable between partners. |
| Household Budget Framework | speced-not-built | Referenced across Newlywed, Caregiving, Widowhood, Remarriage guides. |
| Fair Play / Family Calendar | speced-not-built | Designated core product (not guide-gated) per master reference Part 1. Will be shared by default under the connected-user access model. |

### Phase 3: Optimizer (core product)

| Tool | Status | Notes |
|---|---|---|
| Credit Card Engine | built/live at /credit-card-optimizer | Category-level spending in, annual reward value out for 6 card archetypes, top 2 ranked for user spending profile. |
| Refinance Calculator | built/live at /refinance-calculator | Amortization, break-even, total savings over keep period, favorable/unfavorable verdict. |
| Insurance Adequacy Analyzer | built/live at /insurance-adequacy | Life-stage profiles (dual-income/single-parent/near-retirement), four-category bar visualization. |
| Roth Conversion Modeling | built/live at /roth-conversion | Bracket analysis, fill-to-next-bracket suggestion, blended rate for cross-bracket conversions. |
| Withdrawal Sequencing | built/live at /withdrawal-sequencing | Taxable-first sequence vs. naive traditional-first, annual tax savings comparison. |
| Inherited IRA Distribution Strategy | built/live at /inherited-ira-distribution | SECURE Act 10-year rule, level/front-loaded/back-loaded comparison, per-year income override. |
| Disability Mixed-Funding Tax | covered by /ltd-analyzer | LTD analyzer already implements proportional method with employer-share slider. No separate route needed. |
| Annual Enrollment Comparison | speced-not-built | Referenced in Career Transition, New Parent, Disability, Retirement. Medicare content enhancement noted as pending. |
| Purchase Strategy Simulator | built/live at /purchase-strategy | Four-option true cost comparison (cash/personal loan/0% promo/BNPL) with opportunity cost accounting. |
| Credit Health Dashboard | built/live at /credit-health | Credit snapshot with FICO bands, utilization optimizer, key actions by score band, routing to credit-card-optimizer and credit-rebuilding. |
| Trusted Pro Network | speced-not-built | Core product. Referenced across Estate, Caregiving, Widowhood, Remarriage, Divorce, Disability guides. Notary and employer-benefit-check additions noted. |

### Starting Over Mode tools (Divorce and Widowhood guides)

Three tools built 2026-07-10, shared across Divorce and Widowhood guides:

| Tool | Route | Notes |
|---|---|---|
| Emergency Fund Priority Tool | /emergency-fund-priority | Built. Independent savings only, three milestones, urgency classification. Distinct from core product Emergency Fund Calculator by starting-over framing. |
| Single Income Budget | /income-stabilization | Built. Consolidates Income Stabilization Planner and Single Income Adjustment Tool -- confirmed identical concept. Prior vs. current income, eight expense categories, gap/surplus, per-category flags. |
| Credit Rebuilding Timeline | /credit-rebuilding | Built. Situation-specific milestones for separating from joint accounts. Not generic credit building. |

### Immigration and Finances tools

Three tools built 2026-07-10, assigned to Immigration and Finances guide:

| Tool | Route | Notes |
|---|---|---|
| Authorization-Gap Bridge Planner | /auth-gap | Built. Three-phase timeline for income gaps during USCIS authorization delays. User-entered processing time with link to USCIS source. Runway calculation, gap flagging. |
| Path-to-Residency Cost Planner | /residency-costs | Built. Hardcoded USCIS fee table (verified April 1, 2024), attorney fee input, monthly savings target, progress tracking. |
| Credit-Building-From-Zero | /credit-building | Built. Country-of-origin for Nova Credit eligibility, starting-point-adaptive milestone sequence, authorized-user as parallel accelerant. Separate from /credit-rebuilding. |

### Confirmed Backlog (genuine whitespace, scoping needed)

**Zero items remain in the confirmed-gap backlog.**

RESOLVED: Sibling cost-sharing fairness ledger -- built and live at /caregiver-ledger. Assigned to Caregiving guide.
RESOLVED: Social Security survivor-benefit optimizer -- built and live at /ss-survivor-optimizer. Shared: Widowhood and Retirement guides.
RESOLVED: Immigration USCIS-timeline financial modeling tool -- three tools built and live. See scoping section above.
RESOLVED: Blended-family FAFSA-timing modeling tool and QTIP/bypass trust walkthrough -- two tools built and live. See scoping section above.
RESOLVED: QDRO navigator -- expanded to two tools (/qdro-readiness and /qdro-tracker), built and live. See scoping section above.

### Phase 4: Young Adult

| Tool | Status | Notes |
|---|---|---|
| Career Path Tool | speced-not-built | Listed in master reference Phase 4. No scoping. |
| Student Loan Strategy | speced-not-built | Listed in master reference Phase 4. No scoping. |
| First Apartment Planner | speced-not-built | Listed in master reference Phase 4. No scoping. |

### Phase 5: Modular Marketplace

The 17 Life Phase Guides are written and live in the docs repo. The guide-specific tools assigned to each are tracked in navigation-engine-tool-guide-mapping.md. What remains in Phase 5 is primarily the Trusted Pro Network build (see Phase 3 above) and any guide-specific tools that don't yet appear in the mapping.

---

## Infrastructure and Security Notes

### documents Storage Bucket: Bug Fixed (2026-07-11)

The Life Profile benefits step was uploading documents to a bucket named `user-documents`, while every other step and the extraction API only used the `documents` bucket. The `user-documents` bucket never existed, so all benefits-step uploads silently failed with a bucket-not-found error swallowed by a bare `catch {}`. No files were ever misfiled (nothing landed anywhere), so no data migration was needed. Fixed by correcting the upload call in `app/life-profile/benefits/page.tsx` to use `documents`. Committed as `25fc2bf`.

### documents Storage Bucket: RLS Policy (2026-07-11)

A storage RLS policy restricting authenticated users to their own folder (`{user_id}/` prefix) was confirmed active in production via behavioral testing but was not in version control. Added as a migration file at `supabase/migrations/20260711_documents_storage_rls.sql`. The migration is idempotent (`DROP POLICY IF EXISTS` before create). To apply to production explicitly, use `supabase db push` via the CLI. No action needed now since the policy is already in place -- this entry exists so the migration is not skipped or treated as unapplied in a future `db push` run.

---

## Documentation Health Check: COMPLETED 2026-07-11

### navigation-engine-tool-guide-mapping.md

**Status: RESOLVED. Full verification pass completed 2026-07-11.**

All speced-not-built tools updated to built/live. Section 4 backlog count corrected from "2 remaining" to "0 remaining." Missing routes (Parental Leave Navigator, Childcare Bubble, Children's Planning Hub, Life Insurance Needs, LTD Analyzer, Caregiver Ledger, SS Survivor Optimizer) added to appropriate guide sections. Committed to docs repo as 33aa341. No further action needed.

---

## How to Use This Document

At the start of each working session: read this document and note any items that have resolved since the last update. At the end of each session: update any items that changed, add any new items that surfaced in the session, and update the Last updated date at the top.

Items move out of this document when they have a permanent home: a completed scoping session produces a spec that lives in the appropriate docs/product/ guide file; a resolved architecture question produces a decision entry in the relevant CLAUDE.md Product Decisions Log section; a completed tool build moves to the built/live row of navigation-engine-tool-guide-mapping.md.
