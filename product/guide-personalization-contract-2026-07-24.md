# Krovos Guide Personalization Contract

**Date:** July 24, 2026
**Purpose:** A single governing contract for how every $49 Life Phase Guide personalizes itself using data Krovos already has, without requiring another AI report or API call. This document extends and does not replace `product/guide-personalization-without-api.md` (decided July 20, 2026) — where that document already states something is built, this contract cites it as confirmed rather than re-describing it as new. Where this contract proposes something beyond the July 20 decision, it is explicitly marked as proposed, not built.

**Sources:** `product/guide-personalization-without-api.md`, `product/krovos-tool-catalog.md`, `product/tool-delivery-map-2026-07-24.md`, `governing/founder-edition.mdx`, `strategic/product-overview.mdx`, and `/Users/christine.smith/mcp/csuite/context/3_current_state.md` (canonical current-state record, refreshed July 23, 2026).

---

## 1. The No-Additional-API-Credit Personalization Principle

By the time a member can purchase a guide, Krovos already has an active Core subscription's worth of understanding: a completed Life Profile, a stored Life Graph, at least one generated Life View report, and — for a returning member — saved Core tool results and Life Goals. A guide is not a new report. **A guide is the same understanding, already paid for once through Core, turned into a focused, saved plan for the one life event in front of the member right now.**

The principle, stated as a rule: **no additional AI/API call may ever be required to unlock a purchased guide's promised value.** Selecting relevant Life Graph facts, calculating thresholds and scenarios with existing deterministic tool engines, sorting actions by rule-based urgency, prefilling tools, rendering checklists and timelines, saving progress, reusing already-generated Life View priorities, and scheduling rule-based reminders are all achievable with data and logic Krovos already has. A model call is only ever appropriate as an **optional, member-requested enhancement** (e.g., "explain this to me" inside Krovos Guide) layered on top of a plan that already stands on its own without it.

This matters for three reasons stated plainly:
- **Cost control:** a guide that silently depends on a fresh model call every time it's opened does not scale the way a one-time $49 purchase should.
- **Trust:** a member who paid for a guide and gets a plan built entirely from facts they already gave Krovos experiences "Krovos already knows me," which is the actual product promise. A guide that re-asks or re-generates from scratch breaks that promise.
- **Reliability:** deterministic logic is testable and auditable in a way a fresh generative call is not. A guide's core value should never depend on a live model behaving consistently.

---

## 2. Per-Guide Personalization Detail

For each of the 16 currently sold Life Phase Guides (Retirement is Core and not a separate paid guide, per the canonical record): the existing data signals eligible for reuse, the personalized content blocks the guide should show, the guide-specific tool outputs it can incorporate, the explicit "unknown / needs confirmation" states it must surface rather than guess past, and what must never be inferred.

| Guide | Eligible Existing Data Signals | Personalized Content Blocks | Guide-Tool Outputs to Incorporate | Unknown / Needs Confirmation | Must Never Be Inferred |
|---|---|---|---|---|---|
| **Early Career** | Income type/amount; employer benefits and match; student-debt entries; housing type (renting); Foundation life phase; no prior benefit elections recorded | First-90-days sequenced plan; employer-match capture callout; debt-payoff order; apartment-readiness checklist | First Job Decisions' saved election; Student Loan Repayment Planner's saved plan type; First Apartment Calculator's saved readiness record | "Employer match percentage not yet captured"; "Loan servicer or type not yet confirmed" | Employer match percentage; loan-forgiveness eligibility; take-home pay before entered |
| **Newlywed** | Relationship status Married/Engaged; wedding date and budget entry; partner income (only if explicitly shared); existing beneficiary designations | Account-structure decision summary; wedding funding path; post-marriage administration checklist (name, address, beneficiary updates) | Joint Finances Comparison's saved model; Wedding Budget Planner's saved funding gap; Filing Status Comparison's saved recommendation | "Partner's income not yet entered or not shared with this guide" | Partner's income or assets without explicit sharing consent; an assumed account-merge preference |
| **Immigration** | Visa type/status; work-authorization timeline; remittance amount; household income; state | Authorization-gap runway; cost/fee timeline; credit-building sequence | Work Authorization Gap Planner's saved runway; Green Card Cost Planner's saved cost timeline; Credit Builder's saved plan | "Visa expiration date not yet confirmed" | Immigration/legal status beyond what the member explicitly entered; work-authorization eligibility |
| **Home Buying** | Renting status; income/spending/debt; a home-purchase goal and target down payment; state/city | Maximum household payment; cash-to-close guardrail; property-comparison sequence | Down Payment vs. PMI's saved decision; Homeownership Cost's saved scenario; Closing Cost's saved estimate; Escrow Comparison's saved recommendation | "Target purchase price not yet entered" | Mortgage rate (no live feed exists); property tax/insurance rates beyond disclosed national estimate ranges |
| **New Parent** | Expecting entry or a child under ~5; HSA/FSA and benefits data; income; childcare-cost context; state | Birth-year cash calendar; leave-income gap; childcare and coverage sequence | Parental Leave Planner's saved leave-cash plan; Childcare Bubble Calculator's saved cost projection; Child Costs Planner's saved record; 529 Projection Calculator's saved plan; Life Insurance Calculator's saved coverage need | "Employer parental-leave policy not yet confirmed" | Employer leave-pay percentage; childcare cost region if state/city is missing |
| **College Planning** | Children's ages; 529 balance; income; remarried/stepparent context | Four-year funding plan; aid-contributor path; borrowing guardrails | Financial Aid Comparison's saved funding record; Parent Loan Comparison's saved decision; FAFSA Remarriage Planner's saved workflow | "Award letter not yet entered" | Aid amount before an actual award letter is entered; FAFSA parent-of-record without explicit confirmation |
| **Career Transition** | "Between jobs" status; severance/COBRA data; a new offer entered; cash reserve | Before-notice timeline; income runway; coverage bridge; saved offer decision memo | Severance Bridge Calculator's saved runway; Offer Comparison's saved decision; COBRA vs. Marketplace's saved choice | "Severance terms not yet entered" | Severance amount or COBRA cost without an actual entry |
| **Starting a Business** | Business-owner/self-employed flag; entity type; revenue; household income floor; benefits needed | Revenue Target milestone; benefits-replacement plan; entity/tax/retirement sequence | Business Structure Comparison's saved comparison; Quarterly Tax Calculator's saved estimate; Retirement Plan Comparison's saved choice; Revenue Target's saved milestone | "Entity type not yet selected" | Business revenue or expenses (this is already a fixed input-safety rule); entity type |
| **Gig Work** | Gig/1099 income entries; platform data where supplied; cash reserve | Baseline/ceiling/floor income plan; tax-reserve target; recurring operating-rhythm calendar | Gig Income Tracker's saved log; Gig Paycheck Calculator's saved smoothing plan; Savings Runway Calculator's saved projection | "Platform or income source not yet specified" | Monthly gig income before an entered average exists |
| **Divorce** | Divorced/Separated status; joint debt; property; support obligations; fresh-start context | Safety-aware inventory; temporary-plan flags; settlement scenarios; single-income rebuild plan | Emergency Fund Rebuilder's saved plan; Single Income Planner's saved budget; Credit Rebuilding Planner's saved plan; QDRO Readiness Checklist/Tracker's saved status | "Settlement or decree terms not yet finalized" | Legal settlement terms, custody, or support amounts before entered; any assumption about fault or reason for separation |
| **Caregiving** | Aging-parent flag; care costs; work-hours change; sibling/household context where supplied | Care-cost path; caregiving retirement-impact exposure; benefits routing; family-contribution plan | Care Options Comparison's saved decision; Caregiving Retirement Impact's saved model; Caregiver Cost Tracker's saved split | "Number of siblings sharing cost not yet entered" | An assumed sibling contribution split; care-facility cost without a region entered |
| **Disability** | Disability/leave flag; policy terms; income; medical costs; HSA/FSA | Month-by-month income/coverage plan; claim-documentation checklist; benefit-offset explanation | Disability Benefits Calculator's saved timeline; Medical Cost Planner's saved plan; LTD Coverage Checker's saved adequacy check | "STD benefit format not yet confirmed" | Policy terms (waiting period, benefit percentage, offset); approval timing |
| **Widowhood** | Widowed status with a date; survivor-benefits context; accounts; children | Date-aware first-year checklist; urgent-vs.-wait sequence; survivor-benefit scenarios; professional handoff packet | Widowhood Checklist's saved progress; Survivor Benefit Calculator's saved comparison; the shared Divorce trio's saved plans | "Date of passing not yet entered" (affects checklist sequencing) | Cause or circumstances of death; a survivor-benefit amount without a member-confirmed, SSA-sourced figure |
| **Blended Family** | Remarried status; children from either partner; a prior-estate-documents flag | Household obligations agreement; inheritance-conflict flags; FAFSA and estate decision summary | Blended Family Inheritance's saved flags; FAFSA Remarriage Planner's saved workflow; QTIP vs. Bypass's saved prep notes | "Prior estate documents' beneficiary designations not yet confirmed" | A prior spouse's or ex's entitlements or existing legal obligations without explicit entry |
| **Estate Planning** | Household composition; dependents; property; beneficiary designations; state; insurance | Readiness inventory; discrepancy report (mismatched beneficiaries); decision-maker record; attorney handoff packet | Beneficiary Checker's saved audit; Estate Tax Checker's saved exposure estimate; shared QTIP/Bypass and Life Insurance outputs | "Existing will or trust status not yet confirmed" | Legal-document validity or existence — never assume a will or trust exists or doesn't |
| **Inheritance** | A windfall/inheritance event entered; asset type; existing goals and debts | Deadline calendar (10-year rule, RMDs); basis/document record; irreversible-decision warnings; integration into existing Life Goals | Inheritance Planner's saved allocation; Step-Up Basis Calculator's saved estimate; Inherited IRA Planner's saved distribution plan | "Asset basis not yet confirmed by executor or custodian" | Cost basis without custodian confirmation; the member's relationship to the decedent beyond what's entered |

---

## 3. Shared Guide-Page Structure

Every purchased guide should present these five elements, in this order, before its editorial/written content. Items 1, 2 (partially), and 4 (partially) are confirmed built per the July 20, 2026 decision and its July 22 deployment; items 3 and 5 as specified here are proposed extensions — see Section 4 for the exact built/proposed split.

### 3a. Immediate "what Krovos already knows"
Shown first, above the fold, with no scrolling required: the handful of Life Graph facts most relevant to this specific guide (per the "Eligible Existing Data Signals" column in Section 2). Confirmed built per the July 20 decision's shared layer item 1 — never re-asks a known fact, never displays unconfirmed data as truth, shows "Not yet captured" rather than assuming zero, and provides a direct update path when a missing fact materially affects the plan.

### 3b. Next best action
A single, clearly ranked "do this next" — not a flat list of three to seven equally-weighted actions presented all at once. This applies the same principle the Tool Delivery Map recommends for Life Compass (exactly one governed current-focus card) at guide scope: even though the underlying plan may contain several sequenced actions, the guide page's own immediate framing should point to one next step, with the fuller sequence available directly beneath it. **Proposed refinement**, not yet confirmed built — the July 20 decision's "Your Krovos plan" block already sequences three to seven actions with priority and timing, but does not confirm a single-action-first presentation layer on top of that sequence.

### 3c. Plan / timeline / documents / conversations to have
The sequenced action plan itself (confirmed built, per the July 20 decision's "Your Krovos plan" block: priority, timing, why it matters for this household, the Krovos tool or account area that completes it, a clear completion condition). **Proposed extension**, not yet confirmed built: two additional sub-blocks within this same section —
- **Documents to gather**: a short, guide-specific checklist of documents the member will likely need for this life event (e.g., a wedding date and vendor contracts for Newlywed, an award letter for College Planning, a decree for Divorce) — checkbox-style, not a re-upload requirement, since Krovos already has a document-upload path in Life Profile.
- **Conversations to have**: where a guide's topic requires licensed or official help (legal, tax, immigration, benefits, healthcare, estate), a short list of who to talk to and what to ask, tied directly to the existing professional-help-trigger rules from the July 20 decision (item 5 of the shared layer) rather than a new routing system.

### 3d. Tools available
The guide's own tool list (per `product/krovos-tool-catalog.md`), each shown with its current state: not yet started, in progress with a saved partial result, or completed with a saved decision. Confirmed built in part — guide tools are purchase-gated and prefilled correctly per the July 20 hard gates; **saved-result persistence is only partially built** per that decision's own Implementation Sequence item 4 ("meaningful outputs from each guide tool still need account persistence"). Do not present a tool's state as "saved" unless persistence for that specific tool is confirmed.

### 3e. Progress and re-entry state
A simple, explicit statement of where the member left off: how many actions are complete out of the total, when the plan was last updated, and what changed (if anything) in the Life Graph since the plan was generated. This is distinct from the July 20 decision's "Saved decisions and scenarios" block (which persists tool-level results) and its data-precedence rule (which governs what happens when the Life Graph and a stored report disagree) — this element is specifically about giving the *member* a legible sense of resumption, not about the underlying data-consistency logic. **Proposed, not yet confirmed built** as its own visible UI element, though the underlying `guide_plans` schema (created/updated/completed timestamps, action statuses) already stores what this element would need to display.

---

## 4. Confirmed Built / Proposed Deterministic / Future AI-Powered

### Confirmed currently built (per `product/guide-personalization-without-api.md` and the canonical record)
- The shared deterministic plan engine (`lib/guide-personalization.ts`) and the versioned `guide_plans` migration.
- The shared personalized-plan UI (`PersonalizedGuidePlan.tsx`) on every purchased guide's page.
- Initial deterministic plan definitions for all 16 paid guides, using relevant Life Graph fields and existing Life View items.
- Fail-closed guide-tool access when a configured guide slug cannot be resolved.
- Shared copy stating the plan reuses saved Krovos information and does not generate another AI report.
- Action-progress persistence to `guide_plans`.
- The data-precedence rule (current Life Graph → current saved deterministic tool result → existing Life View narrative → guide defaults/general content) and the rule that a numerical recommendation is never copied out of a stale Life View if the underlying Life Graph has since changed.
- A connected household member with inherited guide access receives their **own** personalized plan from their own permitted Life Graph data — plans are not silently shared.

### Proposed deterministic personalization (no additional AI credits required, not yet built anywhere in the canonical record)
- Completing per-tool meaningful-result persistence to `guide_plans` for every guide tool, not only the subset already confirmed (this closes an explicitly named partial-build gap, not a new one).
- The "documents to gather" checklist block (Section 3c).
- The "conversations to have" block, wired to the existing professional-help-trigger rules rather than a new system (Section 3c).
- The explicit progress/re-entry statement as its own visible element (Section 3e), reading from timestamps and action statuses the schema already stores.
- A single-action "next best action" presentation layer above the full sequenced plan (Section 3b).
- Extending the Tool Delivery Map's proposed cross-guide, signal-triggered recommendation logic (e.g., recommending Beneficiary Checker on any major life event, not only inside a purchased Estate Planning guide) — still deterministic, still no new API usage, but a larger cross-cutting change than the guide-scoped items above.

### Future AI-powered enhancements (explicitly optional, member-requested only, never required to unlock the guide's base value)
- An on-request, Krovos-Guide-authored plain-language walkthrough of the member's specific saved plan, when the member explicitly opens a Guide conversation from the guide page.
- On-request answers to member questions about their specific plan inside that same conversation.
- Any dynamic re-ranking of actions based on nuance surfaced in a Guide conversation — this must remain a suggestion the member can accept or decline, never a silent rewrite of the deterministic plan.

---

## 5. Highest-Value Five Personalization Upgrades Before Beta

Ranked by member benefit against engineering practicality, given the deterministic engine and schema already exist.

1. **Complete per-tool saved-result persistence to `guide_plans`.** Highest benefit: without it, "Tools available" (Section 3d) cannot honestly show a completed/in-progress state, which directly undermines the "Krovos already prepared this" promise the moment a member revisits a guide. High practicality: the schema and pattern already exist for the tools that are done; this is extending known-working infrastructure, not inventing new infrastructure.
2. **Add the explicit progress/re-entry statement (Section 3e).** High benefit: this is the single cheapest way to make a returning member feel picked up where they left off, which is core to the "sustained relationship, not a one-time report" brand promise. High practicality: the underlying timestamps and action statuses are already stored in `guide_plans`; this is a read-and-display task, not new logic.
3. **Add the "documents to gather" checklist block (Section 3c).** High benefit: this is a direct, visible burden-relief moment — a member sees exactly what to have on hand before starting, instead of discovering mid-plan that something is missing. Moderate practicality: content is mostly static per guide (which documents matter for which life event), plus simple per-member checkbox state.
4. **Add the "conversations to have" block, wired to existing professional-help-trigger rules (Section 3c).** High benefit, especially for the legal/tax/estate-heavy guides (Divorce, Widowhood, Estate Planning, Blended Family, Immigration) where knowing *what to ask* a professional is itself a burden the member currently carries alone. Moderate practicality: reuses the already-built professional-help-trigger routing rather than inventing new routing logic — mostly a content and presentation task.
5. **Unify the "unknown / needs confirmation" states (Section 2's column) into one visible surface inside "What Krovos already knows" (Section 3a).** Medium-high benefit: today, missing data can be silently absent rather than explicitly called out per guide; making every guide consistently show its specific unknowns (not just Core tools, which already do this per the input-safety correction pass) closes a trust gap and gives the member one clear list of what to confirm. Moderate practicality: requires defining the unknown-state copy per guide (already drafted in Section 2 above) and surfacing it consistently in the existing shared UI component.

---

## 6. Privacy Boundaries

Guide personalization may use any member data Krovos already has appropriate access to. It must never expose the following, regardless of how convenient it would be for personalization:

- **Partner-private data not covered by an explicit sharing toggle.** A guide may only use a partner's income, assets, or other financial data if `financial_shared` (or the equivalent specific toggle) is actually on. Absent that, a guide must treat partner data as unknown, not infer or estimate it.
- **Household-restricted data beyond a connected member's own permission level.** Per the July 20 decision, a connected household member with inherited guide access receives their own plan built from their own permitted Life Graph data — never the primary's full raw Life Graph. A guide must never silently widen this to "helpfully" personalize further.
- **Document-raw content.** Raw uploaded file bytes, OCR text, or document images must never render inside guide content. Only structured, extracted fields (the ones already confirmed in the Life Graph) may be used. A guide should never display "your pay stub said..." with a raw excerpt — only the structured figure it extracted.
- **Credentials of any kind.** Passwords, API keys, Supabase Storage signed URLs beyond their intended narrow scope, and transient credentials such as an app-specific password used for a one-time calendar import must never appear in guide content, logs the member can see, or saved plan data.
- **Government-issued ID numbers.** Social Security numbers, passport numbers, visa/immigration document numbers, and driver's license numbers must never surface in guide content verbatim, even if such a number exists somewhere in an extracted document field. A guide may reference *that* an ID document is on file (e.g., "identity document received") but never the number itself.
- **Payment and financial account credentials.** Card numbers, bank account and routing numbers, and similar identifiers must never appear in guide content, even when the underlying tool (e.g., Closing Cost Calculator) legitimately needs dollar amounts from those accounts.
- **Cross-account leakage in shared or gifted-access scenarios.** A gift-subscription recipient's or an inherited-access household member's guide plan must never expose the purchasing or primary account's own private data beyond what that specific access model already permits.

Where the July 20 decision's data-precedence rule and this contract's per-guide "must never be inferred" column both apply, the more restrictive rule governs. When in doubt, a guide should show "Not yet captured" or route to a professional-help trigger rather than infer, display, or guess at anything in this section.
