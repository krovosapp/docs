# Product Discoverability and Contextual Delivery Audit

**Date:** July 24, 2026
**Purpose:** A direct, evidence-based audit of whether each built Core capability can actually be discovered at the moment it becomes useful, rather than requiring a member to already know it exists and go looking for it. This is read-only inspection. No app code, database, migrations, deployment settings, providers, or production data were changed to produce it.

**Method:** direct inspection of `/Users/christine.smith/krovos` (read only): `app/components/GlobalNav.tsx`, `app/life-compass/page.tsx`, `app/components/PersonalizedGuidePlan.tsx`, and the page files for every destination in scope, cross-referenced against `product/tool-delivery-map-2026-07-24.md`, `product/guide-personalization-contract-2026-07-24.md`, `product/beta-readiness-test-plan-2026-07-24.md`, `product/accessibility-responsive-readiness-audit-2026-07-24.md`, and `/Users/christine.smith/mcp/csuite/context/3_current_state.md` (all read in full earlier in this engagement). Every claim is labeled **confirmed** (traced to specific code) or **proposal** (a recommendation, not yet built or approved). Where this audit substantially restates a finding from `product/tool-delivery-map-2026-07-24.md` rather than duplicating its full detail, it says so and points there.

---

## 0. The Current Navigation Shell (Ground Truth for Every Destination Below)

**Confirmed, from `app/components/GlobalNav.tsx`:** the persistent nav is:
- **Primary:** Today (`/life-compass`), My Plan (`/life-view`, shown only when `financial?.life_view_generated`), Goals (`/life-goals`), Calendar (`/life-calendar`), plus a standalone "Ask Krovos" button (`/krovos-guide`).
- **Explore menu:** Life Phase Guides (`/guides`), Tool Library (`/tools`), Life Profile (`/life-profile`), Life Graph (`/life-graph`), Life Labor (`/life-labor`), Budget & Spending (`/life-profile/spending`), Net Worth (`/net-worth`), Retirement (`/retirement`).
- **Icon-only:** Settings.

**Confirmed:** Retirement and Net Worth are the only two individual hubs promoted alongside genuinely persistent nav items; everything else in Explore is one tap deeper. This matches `product/tool-delivery-map-2026-07-24.md`'s own recommendation not to add more individual destinations to the top-level nav, since that recommendation is already the current reality, not a pending change.

---

## 1. Life Compass / Today

1. **Signals already available:** the Life View report's own `priority_guidance.items`, the weakest-scoring Life Graph zone, a detected "confirmed change requiring review," and life-phase/family-event data already used to derive milestones.
2. **Delivered contextually, confirmed:** `app/life-compass/page.tsx` reads `content?.priority_guidance?.items` and renders exactly one `urgentFocus` card, with a direct `Link href="/krovos-guide?focus=..."` pre-loading a specific prompt (not spending an API call until the member sends it). The weakest Life Graph zone gets the same treatment, and a persistent "Something changed?" link at the page's end uses the identical pre-loaded-prompt pattern. This is a real, working, single-governed-recommendation surface, not aspirational.
3. **What the member sees after a Life Profile/report is generated:** the first-visit Today greeting and the current-focus card, sourced directly from the just-generated `priority_guidance`. **Confirmed working**, since the card's data source is the report itself.
4. **Dead ends/duplicates/unclear labels:** none found at this destination specifically. One naming note worth flagging: the nav calls this destination "Today," while most product documentation and the canonical record refer to it as "Life Compass"; both names are accurate and in active use, but a member reading external documentation and the in-app label could reasonably wonder if they're the same thing. **Confirmed naming duplication, low severity.**
5. **No-new-AI-credit delivery plan:** already the most complete example in the product. **Proposal, not yet built:** when the current-focus card's underlying signal maps to a specific tool (not only a Guide prompt), deep-link directly to that tool with relevant fields pre-filled, per `product/tool-delivery-map-2026-07-24.md` Section 5's existing recommendation.

---

## 2. Life Profile and Life Graph

1. **Signals available:** Life Profile has none needed, it is the intake itself. Life Graph's signal is simply "has the member ever visited it," since it's a reference/diagnostic surface, not a task-triggered one.
2. **Delivered contextually:** **confirmed not delivered contextually today.** Both are Explore-menu items, one tap deep, with no event that proactively surfaces either. The tool-delivery map's proposal to trigger a Life Graph node's evidence panel from a life-event signal (Section 4 there, "newly relevant life phase") remains a proposal, not built.
3. **What the member sees after a life change:** nothing that proactively points back to Life Profile or Life Graph to reflect the change; the member must navigate to Explore and choose one. **Confirmed gap.**
4. **Dead ends/duplicates/unclear labels:** Explore's "Budget & Spending" link resolves to `/life-profile/spending`, the same page used as a Life Profile onboarding step. **Confirmed, a genuine dual-purpose route**: one URL serves both "step 7 of onboarding" and "the ongoing budget view members return to at payday check-ins" (per its own Explore description, "the budget used at payday check-ins"). This is not necessarily broken, the page likely branches its own copy/behavior by whether onboarding is complete, but it is a single route carrying two distinct member-facing purposes, worth a direct check that neither context leaks the other's framing (e.g., a returning member seeing onboarding-style copy, or a first-time member seeing "reconcile your month" language before they have a month to reconcile).
5. **No-new-AI-credit delivery plan, proposal:** surface a direct link to the relevant Life Profile step from any Life Graph node's evidence panel (already the design spec's own stated intent, per the canonical record) and confirm this is actually wired, since this audit did not trace that specific link in Life Graph's own code this pass.

---

## 3. Life Goals

1. **Signals available:** a stated Life Phase intent (getting married, buying a home, going back to school), any existing debt/savings data that could inform a target.
2. **Delivered contextually:** **confirmed partial.** Step 2's auto-seed (getting_married → wedding, buying_home → home_purchase, going_to_school → education, with an idempotency check) is a real, working, event-triggered goal-creation path, confirmed in `CLAUDE.md`'s own build history. Beyond that seed moment, **no recommendation surface exists on the Life Goals page itself**, confirmed by a direct search of `app/life-goals/page.tsx` finding zero matches for "recommend," "next best," "suggested," or "current focus."
3. **What the member sees after creating a goal:** the goal appears in the active list; **no confirmed "get ready for this goal" prompt or relevant-tool surfacing exists yet**, matching `product/tool-delivery-map-2026-07-24.md`'s own "proposed, not built" status for this exact pattern.
4. **Dead ends/duplicates/unclear labels:** `/goals` redirects to `/life-goals` (a confirmed, correctly single-hop redirect from the July 17 renaming pass); no dead end found.
5. **No-new-AI-credit delivery plan, proposal, restated from the tool-delivery map:** when a goal of a recognizable type is created, surface the one or two directly relevant tools (Down Payment vs. PMI for a home-purchase goal, Wedding Budget Planner for a wedding goal) on the goal's own detail view. This remains this audit's highest-leverage Life Goals recommendation, unchanged from the prior document's finding.

---

## 4. Budget and Payday/Life Updates

1. **Signals available:** the payday window itself (a calendar-computed state), a recorded life event needing reconciliation.
2. **Delivered contextually, confirmed and working well:** `app/life-compass/page.tsx` renders a payday strip with a direct `Link href="/update"` ("Start check-in" / "Reconcile month"), and `app/components/PersonalizedGuidePlan.tsx` independently links to `/update` as well. **This is a genuinely strong, already-built example of exactly the contextual-delivery pattern this audit is checking for**, not a gap.
3. **What the member sees after a life change:** the Life Updates check-in flow itself is the mechanism for recording a life change; discoverability of *that entry point* is confirmed via the payday strip and the Guide-plan link above.
4. **Dead ends/duplicates/unclear labels:** `/update` has **no entry in the persistent nav or the Explore menu at all**, confirmed by its absence from `GlobalNav.tsx`'s link arrays. This is very likely intentional (a payday check-in isn't a browse-anytime destination the way Net Worth is), but it does mean a member who dismisses or misses the payday-window strip has no other confirmed way to find this flow again until the next payday window opens. **Confirmed gap, moderate severity**, distinct from the otherwise-strong contextual delivery already in place.
5. **No-new-AI-credit delivery plan, proposal:** add `/update` to Settings or a low-emphasis link somewhere reachable outside the payday window (not the primary nav, which would work against the "this is a specific-moment flow, not a browse destination" design intent), so a member who wants to record a life change off-cycle isn't left with no path at all.

---

## 5. Net Worth

1. **Signals available:** a meaningful asset/liability change (a new mortgage recorded, a debt paid off, a windfall entered).
2. **Delivered contextually:** **confirmed not delivered contextually.** `app/net-worth/page.tsx` shows zero matches for "recommend"/"next best"/"suggested"/"current focus." It is reachable only via the Explore menu.
3. **What the member sees after a tool result or life change affecting net worth:** nothing that surfaces Net Worth proactively; the member must navigate there themselves to see the updated figure.
4. **Dead ends/duplicates/unclear labels:** none found; a single clean route, no redirect chain.
5. **No-new-AI-credit delivery plan, proposal, restated from the tool-delivery map:** when a specific asset/liability category changes meaningfully, show a single "what this changes" prompt linking to the one most relevant tool (Escrow Comparison for a new mortgage, Credit Health for a debt paid off, Inheritance Planner for a windfall), rather than leaving Net Worth as a purely descriptive view members must remember to check.

---

## 6. Retirement

1. **Signals available:** age, existing retirement assets, a detected lower-income year, proximity to a target retirement date, Social Security eligibility age.
2. **Delivered contextually, confirmed the strongest example among the individual hubs:** `app/retirement/page.tsx` is the one route outside Life Compass itself with confirmed matches for recommendation-adjacent language (three occurrences), consistent with the canonical record's own description of Retirement recommending "a small decision-based set" of tools rather than its full library by default.
3. **What the member sees after a Life Profile/report is generated:** Retirement's own guided workspace prefills from Life Profile income/spending and any recorded retirement-goal fields (per the canonical record); this audit did not re-trace that specific prefill logic line by line in this pass, since it was already covered in prior audits of this route.
4. **Dead ends/duplicates/unclear labels:** `/retirement-trajectory` correctly redirects to `/retirement` (confirmed, single-hop, from the July 17 renaming pass). No new duplicate found.
5. **No-new-AI-credit delivery plan, proposal, restated from the tool-delivery map:** surface Roth Conversion Calculator specifically in a detected lower-income year, rather than only listing it as one of several always-available options, extending the recommendation logic this route already has rather than inventing a new pattern.

---

## 7. Life Labor

1. **Signals available:** an accepted household connection existing at all (the trigger for Life Labor being relevant in the first place).
2. **Delivered contextually:** **confirmed the household connection itself is the trigger**, and Life Labor is promoted directly into the Explore menu (not buried further), matching the canonical record's own note that it was "promoted into the universal Explore menu instead of remaining discoverable only inside the Tool Library." Beyond that promotion, `app/life-labor/page.tsx` shows zero matches for "recommend"/"next best"/"suggested"/"current focus," consistent with `product/tool-delivery-map-2026-07-24.md`'s finding that this page's own internal Seasons/rebalancing prompt is its de facto "recommended next" mechanism, not a separate financial-tool recommendation, which that document explicitly recommends **not** grafting onto this page. This audit agrees with that prior conclusion and does not re-open it as a gap.
3. **What the member sees after a household connection is accepted:** discoverability of Life Labor itself, once a connection exists; this audit did not find a specific "you're now connected, here's Life Labor" moment distinct from Life Labor simply being reachable in Explore going forward. **Needs live verification** for whether the connected-acceptance flow itself surfaces this, or whether a member must independently think to look.
4. **Dead ends/duplicates/unclear labels:** `/household-calendar` correctly redirects to `/life-labor` (confirmed, single-hop, from the July 15-16 rename). No new issue found.
5. **No-new-AI-credit delivery plan, proposal:** at the moment a household connection is accepted (Section 3's gap), surface a one-time pointer to Life Labor specifically, distinct from its passive Explore-menu presence, since a newly-connected member has just gained a genuinely new capability that nothing currently announces.

---

## 8. Life Calendar

1. **Signals available:** a dated life event already recorded elsewhere (a wedding date, a due date, an expiring COBRA election window), per `product/tool-delivery-map-2026-07-24.md`'s Section 5 proposal for this exact destination.
2. **Delivered contextually:** **confirmed not yet delivered contextually** in the direction of "a dated milestone elsewhere in the app surfaces on the calendar as its own action"; `app/life-calendar/page.tsx` shows zero matches for the recommendation-language search pattern. The calendar's own internal features (provider import, recurring Life Labor occurrences, the derived payday/bill/milestone overlay documented in `product/life-calendar-beta-readiness-audit-2026-07-24.md`) are real and working, but that is the calendar showing its own data well, not other parts of the product pointing members toward the calendar at the right moment.
3. **What the member sees after a calendar event is added:** the event appears on the correct date with correct layer coloring (confirmed in the prior Life Calendar audit); no confirmed onward recommendation exists from that point.
4. **Dead ends/duplicates/unclear labels:** the prior Life Calendar audit already confirmed the calendar's own event-type system declares a `'goal'` category with no code path that ever populates it, and that `guide_linked` calendar items are fully supported in the UI with zero producers anywhere in the codebase. **Restated here as confirmed, not re-discovered**, since both are directly relevant to this audit's discoverability question: a member cannot discover a goal or a guide action on the calendar today, because neither type of item is ever placed there in the first place, regardless of how well the calendar itself would display it.
5. **No-new-AI-credit delivery plan, proposal, restated from the tool-delivery map:** when a dated milestone elsewhere (Life Compass's own timeline, a guide's own timeline) corresponds to a calendar-relevant date, show that milestone as an action from within the calendar too, mirroring the existing Life Compass milestone-to-Guide-action pattern rather than inventing a new interaction model. This depends on the Life Goals/guide-linked producer gaps above being resolved first, or it will have nothing new to surface.

---

## 9. Krovos Guide

1. **Signals available:** every signal already described for every other destination above, since Guide is the universal fallback/companion entry point.
2. **Delivered contextually, confirmed and working well:** the `?focus=` query-parameter pattern, used by Life Compass's current-focus card, its zone-health link, its "something changed" link, and `PersonalizedGuidePlan`, is a real, consistently-applied mechanism for opening a Guide conversation pre-loaded with a specific, relevant prompt without spending an API call until the member sends it. This is a genuinely strong, confirmed pattern, and one other destinations' proposed improvements (Life Goals, Net Worth, Life Calendar) should reuse rather than reinvent, since it already exists and works.
3. **What the member sees after any triggering event:** whatever prompt the linking page constructed; Krovos Guide itself does not need to independently detect the triggering event, it only needs to be linked to correctly, which the confirmed pattern above already does at every site this audit found using it.
4. **Dead ends/duplicates/unclear labels:** none found; "Ask Krovos" (nav label) and "Krovos Guide" (everywhere else) is the one naming variance worth noting, low severity, matching Section 1's "Today"/"Life Compass" pattern.
5. **No-new-AI-credit delivery plan:** Guide itself needs no new delivery mechanism; it is already the best-built example of contextual delivery in the product. The remaining work is entirely on the *other* destinations' side, wiring more of them into the existing `?focus=` pattern, not building anything new here.

---

## 10. Guides and Guide Tools

1. **Signals available:** every Life Graph signal named per-guide in `product/guide-personalization-contract-2026-07-24.md`'s Section 2 table (relationship-status changes, a new dependent, immigration/visa data, business ownership, and so on).
2. **Delivered contextually:** **confirmed partial.** Guide relevance ranking on the index page is real (per the canonical record's "likely-relevant guides rise to the top from recorded Life Graph facts"), but this audit did not re-trace that specific ranking logic in this pass. Individual guide-tool recommendation *outside* the context of having already opened that guide's own page is **confirmed not built**, matching `product/tool-delivery-map-2026-07-24.md`'s classification of most guide-exclusive tools as "Guide-only" or "Rec, not yet built" rather than "confirmed contextually delivered."
3. **What the member sees after a guide purchase:** the guide's personalized plan (per `product/guide-personalization-contract-2026-07-24.md`), including "what Krovos already knows," a sequenced plan, and a link into `/update` from `PersonalizedGuidePlan` specifically. **Confirmed built and reasonably strong.**
4. **Dead ends/duplicates/unclear labels:** none newly found; the "never redirect a locked tool straight to checkout" behavior is already confirmed correct in `product/accessibility-responsive-readiness-audit-2026-07-24.md`'s review of the Tools dialog.
5. **No-new-AI-credit delivery plan, proposal, restated from both the tool-delivery map and the guide-personalization contract:** extend the existing Core-Tools "up to five contextually appropriate actions" mechanism to include owned guide-exclusive tools flagged "Rec" in the tool-delivery map, and build the guide-personalization contract's proposed "documents to gather" and "conversations to have" blocks, both of which remain proposals, not built, as of this audit.

---

## 11. Document-Related Features Already Built

1. **Signals available:** none needed for discoverability, since documents are uploaded at specific, already-contextual moments (a Life Profile step, an adult-migration carryover).
2. **Delivered contextually:** **confirmed correct for the upload moment itself** (each Life Profile step's upload card, per the established, repeated pattern across `about`, `benefits`, `income`, `debt`, `spending`); **confirmed no ongoing "see your documents" surface exists anywhere**, per a direct search finding no dedicated documents route, list view, or nav entry anywhere in the app. This matches the canonical record's own explicit statement that Document Vault "remains speced-not-built" and that current uploads live only in Life Profile.
3. **What the member sees after uploading a document:** the extracted, pre-filled fields on that same step, with the established "Pre-filled from your document" label pattern; no confirmed way to later view, replace, or remove a previously uploaded document outside re-visiting the same Life Profile step.
4. **Dead ends/duplicates/unclear labels:** not a dead end so much as a genuine absence; a member cannot discover their own document history because there is currently nowhere built for it to live. This is a scope boundary already correctly documented, not a hidden defect.
5. **No-new-AI-credit delivery plan:** none proposed here beyond what the canonical record already states, Document Vault remains explicitly out of scope pending the connected-user model being proven stable first, and this audit does not recommend building around that decision.

---

## 12. Founder Decisions Needed Before Implementation

1. **Standardize the destination name shown in-product versus in documentation** ("Today" vs. "Life Compass," "Ask Krovos" vs. "Krovos Guide"). Low severity, but a five-minute decision that removes a recurring, low-grade confusion this audit found at two separate destinations.
2. **Confirm whether `/life-profile/spending` serving as both the onboarding step and the ongoing "Budget & Spending" destination is intentional**, and if so, confirm its copy correctly branches by context; if not, decide whether the ongoing view should be its own route.
3. **Approve extending the `?focus=`-into-Krovos-Guide pattern to Life Goals, Net Worth, and Life Calendar**, since this audit confirms the mechanism already exists and works well at every site it's used, meaning the remaining work is wiring, not invention, once approved.
4. **Decide whether Life Labor needs a one-time "you're now connected" discoverability moment**, distinct from its passive Explore-menu presence, at the point a household connection is accepted.
5. **Decide whether `/update` needs any reachable entry point outside the payday window**, given it currently has none in the persistent nav or Explore menu at all.

---

## 13. Prioritized No-New-AI-Credit Delivery Plan

Sequenced by dependency and confirmed current gap, not raw severity, since several items build on the same already-existing mechanism.

1. **Wire Life Goals' "get ready for this goal" prompt using the existing `?focus=` Guide-link pattern first**, since the mechanism is proven and the trigger (goal creation) is a clean, already-logged event. This is the smallest, highest-leverage new wiring in this plan.
2. **Wire Net Worth's "what this changes" prompt the same way**, once item 1 confirms the pattern extends cleanly to a second destination.
3. **Resolve the Life Goals-to-calendar and guide-linked-to-calendar producer gaps** (Section 8, restated from the Life Calendar audit) before investing in any new calendar-recommendation surface, since a recommendation pointing at an empty category delivers nothing.
4. **Extend the Core-Tools "up to five contextual actions" mechanism to owned guide-exclusive tools**, per the tool-delivery map's own priority-2 recommendation, reusing infrastructure rather than building a parallel one.
5. **Add the household-connection-acceptance Life Labor pointer and the `/update` off-cycle entry point** (Section 12, decisions 4 and 5), once those two founder decisions are made, since both are small, additive UI changes with no dependency on anything else in this plan.
6. **Build the guide-personalization contract's "documents to gather" and "conversations to have" blocks last**, since they are the most content-heavy (per-guide copy needed) items in this plan and benefit from the simpler wiring work above being proven first.

**No item in this plan was built or acted on by this audit.** Every recommendation reflects read-only inspection of the code as committed, and several restate, rather than duplicate, findings already established in this repo's prior tool-delivery and Life Calendar audits.
