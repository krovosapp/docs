# Krovos Beta Readiness Test Plan

**Date:** July 24, 2026
**Purpose:** A practical, founder-run walkthrough plan to complete before inviting the first real beta user. This is preparation only. No live user walkthroughs are performed as part of this document; every check below is something Christine does herself, in disposable or test-mode accounts, before extending an invite.

**Sources:** `/Users/christine.smith/mcp/csuite/context/3_current_state.md` (canonical coordination record, refreshed July 23, 2026), `product/remaining-work-inventory-2026-07-24.md`, `product/build-status-inventory-2026-07-23.md`, `product/navigation-tool-guide-map-2026-07-23.md`, and `product/nav-structure-diagram.md`. Where the canonical record does not state a fact was live-verified, this plan says **Verify** rather than asserting it works. Nothing here should be read as a claim that a check has already passed.

**A note on navigation naming:** as of the July 23, 2026 universal app-shell build, the persistent authenticated nav is **Today** (Life Compass, `/life-compass`), **Goals** (`/life-goals`), **Calendar** (`/life-calendar`), **Ask Krovos** (Krovos Guide, `/krovos-guide`), a compact **Explore** menu (Guides, Tools, Life Profile, Life Graph, Net Worth, Retirement), and **Settings**. This supersedes the earlier July 17 nav structure (Life Compass / Krovos Guide / Retirement as primary tabs with a dropdown secondary nav) referenced in older documents. Use the July 23 shell as current; **verify** it still matches production before starting the walkthrough sequence in Section 6, since the canonical record itself flags navigation and tool routes as the parts of this codebase most likely to drift.

---

## 1. Route-Access Inventory

Every core destination a signed-in user must be able to reach. For each: the current route, what it should show, and what to verify given no live-verification evidence exists in the canonical record for that specific check.

| Destination | Route | Current status per canonical record | What to verify |
|---|---|---|---|
| **Today / Dashboard (Life Compass)** | `/life-compass` | Built to full three-zone spec, 94/A design (July 22, 2026): payday strip, nearest-6-month milestone timeline, zone health summary, governed single current-focus card, "Progress and wins," persistent Krovos Guide entry point. | Verify the greeting logic (first post-report visit vs. return visit), that milestones only render from real recorded facts (no placeholders), and that a distinct legacy `/dashboard` route (referenced in earlier build notes as no longer mounting a second navigation) is either an alias of `/life-compass` or a real separate page — the canonical record does not fully resolve this. Confirm which is true in production before beta and correct this table if `/dashboard` is a separate live route. |
| **Life Profile** | `/life-profile/about` → `/life-phase` → `/benefits` → `/income` → `/investments` → `/debt` → `/spending` → `/review` | Canonical 8-step flow, no standalone `/confirm` page, generation triggers from Review CTA directly. | Verify every step's document-upload card appears first, pre-fill labels render correctly, and the step-nav dropdown shows "Step X of 8." |
| **Life Graph** | `/life-graph` | Built and live: SVG node map, 10 nodes, 3 zones, health scoring, zone strip, tap-to-select bottom sheet, plain-language evidence panel per node, keyboard-accessible nodes that announce score and status. Scored 90/A- by the Core audit. | Verify historical scoring and correction provenance are not silently missing, and check authenticated household edge-case behavior (a connected partner's view of a shared Life Graph node). |
| **Life Goals** | `/life-goals` (`/goals` redirects) | Fully built: individual/household classification, connected-partner edit path gated on `financial_shared` + `financial_edit_access`, change-log-audited edits, concurrency protection, completed/abandoned status instead of hard delete. Scored 94/A design. | Verify the redirect from `/goals` still resolves with a single hop, and that a household goal correctly denies write access to a connected partner without edit permission. |
| **Retirement** | `/retirement` | Rebuilt as one guided workspace: connects Net Worth, retirement assets, active Life Goals, and Life Profile income/spending; "Just me / Me + partner" selector; sequential gating (Next decisions, Decision tools, Saved plan locked until a model is saved); scenario workbench with side-by-side paths. Scored 98/A+; the only remaining gate is human comprehension. | This is the single most-built, most-scored surface in the product — still walk it end to end yourself (see Section 6) rather than treating a 98 score as a substitute for a real pass, since the canonical record itself states the comprehension gate "cannot honestly be marked complete by code alone." |
| **Net Worth** | `/net-worth` | Live in secondary/Explore nav. | Verify it reads live Life Graph investment/debt data correctly and reflects a household's combined position when connected and shared. |
| **Life Labor** | `/life-labor` | Built and live (renamed from Household Calendar / Fair Play framing): task list, effort/energy tagging, "I have 30 minutes" quick-access mode, Money and Home Work correlation tab, Seasons rebalancing prompts, household facts layer. Promoted into the universal Explore menu. | Verify blank-state correctness (a new workspace starts empty, not with a generic seeded list, and paid-hours fields start blank per the July 23-24 correction) and that sharing/edit-access gating (`life_labor_shared`, `life_labor_edit_access`) behaves correctly for a connected partner. |
| **Life Calendar** | `/life-calendar` | Built and live: one-time and recurring task occurrences, non-task household/personal/work/Guide-linked event layers, Google Calendar import and read-only work feed (live and verified), Outlook and Apple import foundations shipped but provider authorization pending. | Verify Google import end to end with a real test account; **do not** attempt Microsoft or Apple import verification yet, since Microsoft OAuth and a founder-authorized Apple iCloud test remain pending per the canonical record. Reminder/notification delivery for calendar items is not built — do not test for it. |
| **Krovos Guide (Ask Krovos)** | `/krovos-guide` | Built and live: always presents starter prompts (life-phase-aware even with a sparse Life Profile), context-specific questions once Life Graph data exists, failed requests no longer render as a misleading assistant reply (explicit retry offered instead), footer privacy/AI-details link. | Verify a genuinely fresh account with a sparse Life Graph still gets useful starter prompts, and deliberately trigger a failed request to confirm the retry state renders correctly rather than a silent or misleading failure. |
| **Settings** | `/settings` | Built and live: household-connection invite/accept/revoke/reconnect UI, sharing toggles, theme control, adult-migration claim generation (age-gated, hidden until a tracked child crosses 18). | Verify the three connection states (unconnected, primary, connected-party) each render the correct panel — this was a real bug found and fixed July 16, 2026, so specifically re-check it rather than assuming the fix is still intact. |
| **Guides (index)** | `/life-phase-guides` (also reachable via `/guides` per the July 23 Guide-journey correction) | 16 currently-sold Life Phase Guides across 5 clusters (Starting Out, Building a Household, Career, Life Changes, Later Life). Retirement no longer sells as a separate guide — it moved into the Core Retirement Hub, which is why guide-count language elsewhere in older docs said 17; **16 is the current sellable count.** Guide previews describe durable outcome, key enhancements, and included tools before checkout; the $49 price is stated once. | Verify guide-relevance surfacing from recorded Life Graph facts (likely-relevant guides rising to the top) and that the purchase CTA does not repeat the price string. |
| **Guide tools** | Various, under each guide's slug; catalog lists 72 unique tool routes total (Core + Guide) | Locked-tool behavior: a tool tied to a guide the signed-in user doesn't own renders greyed with a lock icon and opens an in-place explanation, never a direct redirect to checkout. OR-logic (owning either of two qualifying guides unlocks a shared tool) is implemented for Life Insurance Calculator, FAFSA Remarriage Planner, QTIP vs. Bypass Comparison, and the Divorce/Widowhood trio (Emergency Fund Rebuilder, Single Income Planner, Credit Rebuilding Planner). | Verify the OR-logic case specifically (own only one of the two qualifying guides, confirm the shared tool unlocks) and that a zero-guide account sees every guide-tool category locked, not just most of them. |
| **Document Vault** | No dedicated route exists | **Speced-not-built.** The canonical record is explicit: "Document Vault is not an orphaned finished feature: it remains speced-not-built. Current uploads live in Life Profile; no Vault destination should be advertised until the secure per-item sharing product is actually built." | Do not test this as a feature. Confirm instead that nothing in the current app (nav, Settings, guide copy) advertises a Document Vault destination that doesn't exist. |

---

## 2. Five Test Personas

Each persona should be run as its own disposable or designated test account (Jordan Ellis, Morgan Callahan, Alex Nguyen, and Christine's own account already exist for this per `CLAUDE.md`'s Test Accounts table; re-use them or create fresh disposable accounts as needed, clearing prior data first per the standing Jordan-walkthrough convention).

### Persona 1: Single early-career user
**Profile shape:** unmarried, no dependents, W-2 or early self-employed income, limited assets, first real job or recently graduated.

Test:
- Life Profile 8-step flow with no household complexity.
- Early Career Starter Kit guide: First Job Decisions, Student Loan Repayment Planner, First Apartment Calculator.
- Core tools: Debt Payoff Planner, Emergency Fund Calculator, Credit Card Optimizer.
- Retirement in "Just me" individual mode only — do not test the partner path with this persona.
- Life Goals as an unconnected, single-owner account (this is also the natural control case for confirming an unconnected account shows no household-sharing banners anywhere).
- Life Compass first-visit greeting and empty "Progress and wins" state before any history exists.

### Persona 2: Partnered household with shared finances
**Profile shape:** married or engaged, combined or mostly-combined finances, both partners active in the app via a household connection.

Test:
- Full household-connection flow: invite sent from `/settings`, accepted via `/invite/[token]`, connected onboarding pre-seeded from the primary's Life Graph.
- Newlywed guide: Joint Finances Comparison, Wedding Budget Planner, Filing Status Comparison.
- Retirement in "Me + partner" mode with the Life Profile money-management setting driving whether asset/savings inputs are combined or kept separate per person.
- Life Labor's Money and Home Work correlation tab and Seasons rebalancing prompt, plus effort/energy tagging and the "I have 30 minutes" quick-access mode.
- Life Calendar's shared personal-event layer (collaboratively editable when `calendar_shared` is on) versus the owner-only work layer.
- Collaborative Life Goals: a household-scoped goal edited by the connected partner, confirming the change-log entry and concurrency protection.
- `HouseholdMergedBanner` on Life Compass when `financial_shared` is true.

### Persona 3: Family with children
**Profile shape:** one or more children, possibly an expecting entry, dependent documents attached.

Test:
- Adding a child in `/life-profile/about`: name, DOB, relationship, an `expecting` checkbox that hides identity-document upload for an unborn child, and a document upload for an existing child.
- New Parent guide: Parental Leave Planner, Childcare Bubble Calculator, Child Costs Planner, Life Insurance Calculator (shared with Estate Planning), 529 Projection Calculator.
- Family Mode: confirm it activates for a household with a child or expected child, and stays active alongside (not instead of) Caregiving context if an aging parent is also present.
- Spending page's childcare guidance band (10-20%) appearing only when children are present.
- Life Calendar's kids'-schedule layer.
- If a tracked child is near or past 18, verify the parent-generated adult-migration claim flow in `/settings` (claim generation, expiration, and data-carryover scope — name, DOB, relationship, and that child's attached documents only).

### Persona 4: Caregiver / sandwich-generation user
**Profile shape:** caring for an aging parent, possibly also raising children (true sandwich generation) or caregiving alone.

Test:
- Caregiving guide: Caregiver Cost Tracker, Care Options Comparison, Caregiving Retirement Impact.
- Family Mode: confirm it does **not** activate from an aging parent alone (only from a child or expected child), so a caregiver with no children of their own should see Caregiving context only, not Family Mode.
- If also raising children, confirm both operating modes present sensibly at once rather than one silently overriding the other.
- Estate Planning's Special Needs Planning branch, if the dependent being cared for has special needs (SSI/Medicaid preservation, ABLE eligibility, trust structuring, professional handoff).
- Life Labor's household facts layer if used to track caregiving-related logistics for a connected household.

### Persona 5: Transition user (job loss, divorce, or widowhood)
**Profile shape:** relationship-status or employment-status change, the highest-complexity conditional-thread case in `/life-phase`.

Test:
- `/life-phase` relationship-status conditional threads: Divorced/Separated triggers alimony and child-support fields; Widowed triggers a survivor-benefits question; Remarried triggers a prior-obligation question.
- Career Transition guide (for the job-loss variant): Severance Bridge Calculator, Offer Comparison, COBRA vs. Marketplace Comparison.
- Divorce and Transition guide: Emergency Fund Rebuilder, Single Income Planner, Credit Rebuilding Planner (all three shared with Widowhood), QDRO Readiness Checklist, QDRO Tracker.
- Widowhood guide: Survivor Benefit Calculator (single-guide now that Retirement no longer sells separately), Widowhood Checklist.
- The OR-logic shared-tool unlock for the Divorce/Widowhood trio specifically, using this persona's real guide ownership.
- If this persona is also cancelling: run the `/cancel` 7-screen flow (interstitial, survey, 50%-off save offer on the too-expensive path, pause via Stripe `pause_collection`, confirm, done) in Stripe **test mode only**.

---

## 3. Release Gates

Grouped by category. "Owner" is Christine unless a check is fully automated by an existing build-time audit script, in which case the automated gate is noted separately from the human sign-off it still requires.

### Functional

| Gate | Expected outcome | Pass/fail criterion | Owner |
|---|---|---|---|
| Life Profile completion → Life View generation | A fresh account completes all 8 steps and receives a Life View report reflecting only data entered through the flow | Fail if any figure in the report cannot be traced to an entered or extracted value | Christine (founder walkthrough) |
| Core tool blank-state safety | Every audited tool (Emergency Fund, Retirement Withdrawal Planner, Roth Conversion, RMD Tracker, Social Security Comparison, Inherited IRA, Parent Loan Comparison, Caregiving Retirement Impact, Inheritance Planner, Insurance Gap Checker, Disability Benefits Calculator, Student Loan Repayment, Financial Aid Comparison, LTD Coverage Checker, Life Insurance Calculator, Estate Tax Checker, Joint Finances Comparison, Parental Leave Planner, Filing Status Comparison, First Job Decisions, First Apartment Calculator, Wedding Budget Planner, Business Structure Comparison, Closing Cost Calculator, Homeownership Cost, Escrow Comparison, Refinance Calculator, Down Payment vs. PMI, COBRA vs. Marketplace) starts blank and withholds a result until real member input exists | Fail if any tool renders a computed result, a fabricated example number, or a silently-assumed default before the member enters that fact | Automated: `npm run audit:tool-inputs` (already gates the build); Christine confirms it is green before beta |
| Tool input remount stability | Typing continuously into a numeric field (e.g. `3500`) never loses focus mid-entry | Fail on any dropped keystroke or focus loss | Automated: `npm run audit:tool-inputs`; Christine spot-checks Emergency Fund and one guide-exclusive tool manually |
| Guide gating enforcement | A signed-out or non-purchasing visitor cannot reach a guide-exclusive tool's real functionality | Fail if any guide-exclusive route bypasses `GuideGate` or remains in `isPublicPath` | Automated: `npm run audit:app-shell`; Christine spot-checks 2-3 guide-exclusive routes directly |
| Household connection core loop | Invite → accept → connected onboarding → shared data visibility completes without error for a fresh pair of test accounts | Fail on any step that errors, silently no-ops, or shows the wrong party's data | Christine (founder walkthrough, Section 6) |

### Navigation

| Gate | Expected outcome | Pass/fail criterion | Owner |
|---|---|---|---|
| Universal app shell present everywhere | Every authenticated route shows the Today/Goals/Calendar/Ask Krovos/Explore/Settings shell with a route-aware back path, no page mounts a duplicate nav | Fail if any route shows no nav, a stale nav, or two navs at once | Automated: `npm run audit:app-shell` (passed across 113-115 routes per canonical record); Christine confirms current route count matches before beta |
| Public-path accessibility for Core routes | Signed-out visitors can view Core/public routes (e.g. `/debt-payoff-planner`, `/net-worth`, `/retirement`) without being bounced to sign-in | Fail if any route `proxy.ts` marks public actually redirects an anonymous visitor | Christine, verify directly with cookies/localStorage cleared |
| Deprecated-route redirects | `/smart-start`, `/report`, `/retirement-trajectory`, `/goals`, `/household-calendar`, `/debt-optimizer`, `/w2-exit-milestone-calculator` and other renamed routes single-hop redirect to their current route | Fail on a broken link, a 404, or a multi-hop redirect chain | Christine, spot-check with `curl -I` on each |

### Light mode

| Gate | Expected outcome | Pass/fail criterion | Owner |
|---|---|---|---|
| Daylight Lantern coverage | Every route renders legibly in light mode: no dark-on-dark or light-on-light text, correct SVG label contrast on Life Graph, correct Krovos Guide conversation-selector contrast | Fail on any illegible text/background pairing found by direct inspection | Christine — this remains an explicitly open pre-beta gate per the canonical record ("meaningful route-specific illustrations... remains open pre-beta work" and several Daylight Lantern regression-fix entries); **verify** each core route individually rather than assuming full coverage |
| Theme toggle persistence | Switching theme in Settings persists across reload and across routes, with no hydration-mismatch warning | Fail on a flash of the wrong theme or a console hydration warning | Christine |

### Empty / prefilled state

| Gate | Expected outcome | Pass/fail criterion | Owner |
|---|---|---|---|
| Honest blank states | A brand-new workspace (Life Labor, Beneficiary Checker, Purchase Planner, Offer Comparison, etc.) begins empty or unselected, never with fabricated example data standing in for member facts | Fail if any tool or workspace shows a number, selection, or example that reads as the member's own data before they entered it | Christine, cross-check each item in the "Genuinely fabricated-default corrections" list from the canonical record (Emergency Fund, Credit Card Optimizer's business defaults, Beneficiary Checker's seven accounts, LTD Coverage Checker's policy assumptions, and the others listed in Section 6 below) |
| Life Graph pre-fill correctness | Where a confirmed Life Graph field exists (investments total, savings balance, emergency fund balance, computed monthly income, monthly spending total, target retirement age, number of children, state), a tool pre-fills it and labels it "Pre-filled from your document" or equivalent | Fail if a pre-fill silently overwrites a value the member already edited, or if a field not in the confirmed list is guessed at | Christine |
| "Progress and wins" empty state | A new account with no Net Worth history and no goal contributions sees an honest empty state pointing to the specific actions that begin history, not a blank or broken card | Fail if the card is blank, errors, or implies history exists when it doesn't | Christine |

### Household sharing / privacy

| Gate | Expected outcome | Pass/fail criterion | Owner |
|---|---|---|---|
| Unconnected-account isolation | A genuinely unconnected account never sees any shared-household UI, banner, or data | Fail if any household-only element renders for an unconnected account | Christine, using Alex Nguyen or a fresh disposable account as the control |
| Sharing toggle enforcement | `financial_shared`, `financial_edit_access`, `life_labor_shared`, `life_labor_edit_access`, and `calendar_shared` each independently gate exactly the read/write access they claim to | Fail if any toggle grants more or less access than documented, or if flipping one toggle affects a different one | Christine, test each toggle in isolation with a real connected pair |
| Connected-partner data boundaries | A connected partner never sees the primary's work-calendar events, guide-linked private tasks, or unshared personal items | Fail on any leak of owner-only data to a connected partner in any setting combination | Christine |
| Revocation and reconnection | Removing a household connection revokes shared access immediately; reconnecting offers a restore-vs-fresh choice | Fail if access persists after revocation, or if reconnection silently restores without the choice | Christine |

### Mobile / accessibility

| Gate | Expected outcome | Pass/fail criterion | Owner |
|---|---|---|---|
| Responsive layout at core breakpoints | No horizontal overflow at phone (~320-375px), tablet (~768px), and desktop (~1024-1440px) widths on the twelve primary destinations | Fail on any horizontal scroll or clipped control | Christine, resize and check manually — this is explicitly listed as a remaining release gate in the canonical record, not yet claimed complete for every route |
| Accessible control naming | Interactive controls (Life Labor's task controls, Krovos Guide conversation/message controls, Settings' partner-email field, calendar day buttons) have real accessible names, not blank or generic labels | Fail on any control a screen reader announces with no meaningful name | Christine, spot-check with a screen reader or the browser's accessibility inspector |
| Reduced motion respected | Animations and transitions honor the OS/browser reduced-motion preference | Fail if any animation ignores the preference | Christine |
| Windows PWA install | The PWA installs and runs correctly on Windows | **Verify** — this is listed as an open cross-platform testing item, not confirmed complete anywhere in the canonical record | Christine |
| Tablet layout | Tablet-width layout has not been separately confirmed beyond the twelve-destination responsive matrix | **Verify** explicitly before beta, since tablet is called out as still-open cross-platform testing in `CLAUDE.md`'s own backlog | Christine |

### Payment / gifts / discounts

| Gate | Expected outcome | Pass/fail criterion | Owner |
|---|---|---|---|
| Founding Member checkout, test mode | A Stripe test-mode checkout with `referral_code` metadata completes and the code writes correctly to the `profiles` table | Fail if the metadata is missing from the session or the write to `profiles` doesn't happen | Christine, **verify in Stripe test mode only** — do not touch live Stripe |
| Discount attestation flow | Student/military/nonprofit/educator attestation on `/subscribe` correctly applies the 15% coupon and displays the discounted price | Fail if the discount is promised but not applied at checkout | Christine, test mode only (already verified once per the canonical record — re-confirm rather than assume it still holds after any subsequent deploy) |
| Gift subscription purchase and redemption | A test-mode gift checkout at $52 (3 months) or $189 (12 months) completes, the recipient email is deliverable, and redemption correctly handles each recipient state (no subscription, active renewing, scheduled-cancellation, paused) | Fail on any redemption path that grants the wrong duration, double-grants access, or silently fails | Christine, Stripe test mode only; **do not** attempt this against live Stripe, and do not open gift sales to the public until the CPA/legal review noted in Section 5 is complete |
| Cancellation and pause flow | The `/cancel` 7-screen flow (interstitial, survey, save offer, pause, confirm, done) completes and the resulting subscription state (paused vs. cancelled) matches the member's choice | Fail if the wrong Stripe state results, or if the confirmation email doesn't send | Christine, test mode only |
| Adult-migration claim + gift stacking | A gift code redeemed against an account with an active, unconsumed migration claim completes both without conflict | Fail if the claim consumes before the migration completes, or if gift access and migration carryover interfere with each other | Christine, disposable test accounts only |

### Email / feedback

| Gate | Expected outcome | Pass/fail criterion | Owner |
|---|---|---|---|
| In-app feedback button | Floating feedback button appears on every authenticated page, captures category/rating/text/screenshots, and triggers a Resend alert to founder@krovos.app | Fail if the button is missing on any authenticated route or the alert email doesn't arrive | Christine |
| Transactional email delivery | Waitlist confirmation, Life Gaps results delivery, cancellation confirmation, and gift-recipient emails all send and reach `delivered` status in Resend, not just `queued` | Fail on any email stuck at `queued`/`bounced` beyond ordinary latency, or any email that never sends | Christine, check Resend's dashboard directly for each send |
| Life-phase-aware Krovos Guide starters | A sparse account still gets relevant starter prompts | Fail if a new account sees generic or broken prompts | Christine |
| Beta-specific flow (if inviting via `/beta`) | Beta code claim, self-service guide unlocking via `/beta-guides`, and the one-time "tell us what you think" toast all function | Fail if a beta code doesn't grant durable `beta_tester` status, or guide selection doesn't persist | Christine, using a disposable beta-code account |

---

## 4. Not a Beta Blocker Yet

Intentionally deferred or genuinely unbuilt work. If any of the following comes up during the walkthrough, it is **not a defect** — do not report it as one, and do not let it block a beta invite. (Sourced from `product/remaining-work-inventory-2026-07-24.md`, Sections 2-4.)

**Speced-not-built features:**
- Document Vault (per-item private/shared document repository) — current uploads live in Life Profile only.
- Household Budget Framework (full shared operating budget with per-category visibility and edit controls) — Budget & Spending and payday check-ins are foundations, not this.
- Trusted Pro Network (curated professional-referral directory).
- Connected-user financial write path for goals — the toggle exists in the UI; no endpoint lets a connected partner actually write.
- Life Graph `goals[].scope` field and cross-record household-goal sync.
- Household Connections Phase 2 (widowhood/emergency access) and Phase 3 (subscriber merge) — do not build or expect these; no code exists.
- Family Mode priority-tool sequencing — the mode activates correctly, but there's no ordered workflow behind its four focus labels yet.
- Krovos Guide → Life Updates handoff (a confirmed change from a Guide conversation should route into Life Updates for review, not silently write to the Life Graph).
- Calendar reminder/notification delivery.

**Deferred strategic decisions (no founder decision made yet, do not build):**
- Goal-Based Investing scope (guidance-only vs. execute/manage).
- Estate documents (organize-and-refer vs. draft).
- Trusted Pro Network's operating/monetization model.
- VerifyPass-based steeper discount tier.
- Occasion-based messaging suppression and deceased-partner date suppression on the Life Calendar.
- Light-mode logo/hero art variant.
- The "AI heads of department" specialized-agent concept.
- Medicare Advantage vs. Medigap comparison tool.
- Christine's captured idea-stage backlog: swipe/card decision mechanic, productivity/habit tracking, second-brain notetaking, registry builder, party planner, digital-envelope budgeting, and the deeper multi-provider Unified Life Calendar vision beyond what's shipped.

**Business/legal work (not a product defect):**
- Trademark clearance call (Luedeka Neely).
- Blount County business license.
- Solo 401k (deadline December 31, 2026).
- CPA confirmation of gift-subscription accounting/tax treatment, and legal review of gift refund/no-cash-value wording — both block *public sale* of gifts, not the feature's existence.
- Microsoft OAuth app registration and a founder-authorized real Apple iCloud import test for Life Calendar.
- Google Calendar OAuth app remains in Testing status in Google Cloud — publishing/verification with Google is an open decision.

**Known pre-existing engineering debt, not new:**
- Repository-wide ESLint remains blocked by a pre-existing ~378-error/116-warning backlog unrelated to any single feature. This is documented as a known gap, not represented as passing anywhere in the canonical record, and should not be treated as a new finding during beta prep.

---

## 5. Final Founder Walkthrough Sequence

Complete in this order before inviting the first beta user. Use disposable or designated test accounts throughout; never real payment methods or live Stripe/Supabase/Vercel changes as part of this document (none of that is in scope here — this file only records the plan).

1. **Fresh signup, no seeded data.** Create or fully clear a test account. Complete the Life Profile 8-step flow start to finish, confirm document upload and pre-fill on each applicable step, and confirm generation runs directly from the Review CTA into Life View with no `/confirm` page.
2. **Life View read.** Confirm every figure in the generated report traces to entered/extracted data, there are no em dashes, and the report correctly frames "What To Do First" as Krovos-owned actions, not user homework.
3. **Life Compass / Today, first visit.** Confirm the first-visit greeting, the empty "Progress and wins" state, and that the milestone timeline shows only real, fact-derived items.
4. **Life Graph.** Open the SVG map, tap into 2-3 nodes, read the evidence panel, confirm keyboard navigation works and nodes announce score/status.
5. **Life Goals.** Create a goal, log a contribution, mark one complete and one abandoned, confirm the tab view and that permanent deletion is not offered (status-based only).
6. **Life Calendar.** Add a one-time personal event and confirm a recurring Life Labor task occurrence renders correctly; if Google is connected, run a real import and confirm the read-only work-feed behavior; do not attempt Microsoft or Apple import yet.
7. **Retirement.** Run the full guided workspace solo ("Just me" mode): complete all steps, save the model, confirm Next decisions/Decision tools/Saved plan unlock only after saving, and check at least one scenario adjustment (savings/return change, a windfall, a one-time cost).
8. **Core money tools.** Spot-check Emergency Fund, Debt Payoff Planner, Credit Card Optimizer, and Purchase Planner specifically for blank-state correctness (no fabricated starting numbers) and for the currency-formatting behavior on entry.
9. **Krovos Guide.** Open a fresh conversation from a sparse account and confirm the starter prompts are relevant; deliberately trigger a failed send (e.g., by simulating an API error if feasible, or by testing on a flaky connection) to confirm the retry state.
10. **Household connection, second account.** From the first account's Settings, invite a second disposable test account. Accept the invite, complete connected onboarding, and confirm: shared Life Compass banner, Retirement "Me + partner" mode, Life Labor sharing/edit-access gating, Life Calendar's shared personal layer, and collaborative Life Goals editing — each toggle tested independently.
11. **Guide purchase path.** As a zero-guide account, confirm every guide-exclusive tool renders locked with an in-place explanation (not a redirect to checkout). Then grant one guide (via a real test-mode purchase, or the beta self-unlock path if testing as a beta account) and confirm its tools unlock, including at least one OR-logic shared tool.
12. **Settings.** Walk all three connection states (unconnected, primary, connected) to confirm the correct panel renders for each. Toggle theme light/dark and click through 8-10 core routes to check Daylight Lantern legibility.
13. **Mobile pass.** Resize to phone width and re-walk steps 1, 3, 7, and 10 above; confirm no horizontal overflow and that touch targets are usable.
14. **Payment surfaces, test mode only.** Run a Founding Member checkout with a referral code, a discount-attestation checkout, a gift purchase and redemption, and a `/cancel` flow through to pause and to full cancellation — all in Stripe test mode.
15. **Feedback and email.** Submit one in-app feedback item and confirm the Resend alert arrives; confirm at least one transactional email (e.g., cancellation confirmation) reaches `delivered` status, not just `queued`.
16. **Only after all of the above pass:** invite the first real beta user, and only then begin the actual founder/beta walkthrough process referenced elsewhere in `CLAUDE.md` — which remains out of scope for this document.
