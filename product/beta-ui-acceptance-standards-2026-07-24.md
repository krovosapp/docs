# Krovos Beta UI/UX Acceptance Standards

**Date:** July 24, 2026
**Purpose:** A practical, design-and-acceptance standard for the UI/UX quality bar every core route must clear before beta. This is not a code task and does not claim any fix is already made. Where the canonical record confirms a fix or build shipped, this document says so and cites it. Where it does not, this document says **verify** rather than asserting the work is done.

**Sources:** `governing/founder-edition.mdx` (burden-relief principle, the Ten Laws), `brand/brand-guide.mdx` (color system, Daylight Lantern tokens, typography rules, anti-directions), `strategic/product-overview.mdx`, `product/beta-readiness-test-plan-2026-07-24.md`, `product/tool-delivery-map-2026-07-24.md`, and `/Users/christine.smith/mcp/csuite/context/3_current_state.md` (canonical current-state record, refreshed July 23, 2026).

**Governing standard, restated up front:** every acceptance criterion below exists in service of one test from the Founder Edition: *does the member experience "that's exactly me," not "here is a form"?* A route can be visually polished and still fail this standard if it presents a generic form, a fabricated example, or a burden the member has to carry themselves (per the Ten Laws: "Reduce the burden of navigating life," "Never profit from confusion," "The relationship is the product, protect it above all else"). Visual acceptance and burden-relief acceptance are the same review, not two separate ones.

---

## 1. Universal Requirements for Every Signed-In Route

These apply to every authenticated route without exception. A route that violates any of these is not beta-ready regardless of how complete its own feature logic is.

### 1a. Dark and Daylight Lantern contrast/readability
- Midnight Slate (`#1B2230`) remains the default canvas; Daylight Lantern (Warm Parchment `#F3F0E8` canvas, Ivory Paper `#FFFCF6` cards, Midnight Slate `#1B2230` primary text, Accessible Gold `#7A5B18` for gold text/accents, Daylight Ember `#A94F24`, Daylight Mist `#2F647D`) is the only approved light-mode palette. No route may render a generic white-SaaS dashboard look or a mechanical color-inversion of dark mode — per the Brand Guide, Daylight must "feel like a well-designed financial field guide," not a plain light theme.
- No text below the brand guide's stated minimum: Soft Fog body text must never drop below `/50` opacity in dark mode; Daylight text must use the darker accent variants (Accessible Gold, Daylight Ember, Daylight Mist) for normal-size text, never the brighter decorative variants, so contrast stays accessible.
- **Pass:** every text/background pairing on the route is legible at a glance in both themes, with no dark-on-dark, light-on-light, or leftover-inline-dark-background-under-light-text artifacts.
- **Fail:** any single illegible pairing, anywhere on the route, in either theme.
- This is a confirmed **open** area, not a closed one — the canonical record documents a sequence of Daylight Lantern regression fixes through July 23, 2026 (global navigation text, the Krovos Guide conversation selector, Guide-preview content, a "legacy inline-background safety layer," and a native-select treatment for conversation history), which means light mode has been fixed piecemeal as issues surface, not verified complete in one pass. Treat every route as needing its own explicit light-mode check before beta, not as inheriting a global guarantee.

### 1b. One consistent global navigation path, Settings access, home-linked wordmark, no dead ends
- Every authenticated route shows the same nav shell: Today, Goals, Calendar, Ask Krovos, Explore (Guides, Tools, Life Profile, Life Graph, Net Worth, Retirement), Settings — per the July 23, 2026 universal app-shell build. No page may mount a second, inconsistent nav or omit the shell.
- The KROVOS wordmark is always linked back to home (Today/Life Compass) and always rendered in Soft Fog `#E9EEF2` in dark mode or Midnight Slate in Daylight — never gold, per the locked wordmark rule.
- Settings must be reachable from every authenticated route through the shell, not through a page-specific workaround.
- A route-aware back path must exist from every deep page back toward Life Compass; no page may strand the member with no way back except the browser's own back button.
- **Pass:** nav shell, wordmark link, Settings access, and a back path are all present and consistent on the route.
- **Fail:** any missing element, or any route rendering its own divergent nav.
- Confirmed built per the canonical record (`npm run audit:app-shell` covering 113-115 routes) — still **verify** the current live route count matches, since the canonical record itself calls navigation the part of this codebase "most likely to drift."

### 1c. Clear loading, empty, error, restored, locked, and prefilled states
Every route must have an intentional design for all six states that applies, not just the "happy path" with data:
- **Loading:** the branded lantern loading experience, not a blank screen or a generic spinner.
- **Empty:** an honest, specific message describing exactly what action begins the data (per the "Progress and wins" pattern already shipped on Today), never a blank card or a silently-zeroed result.
- **Error:** a plain-language statement of what didn't work and a real retry path — per the Krovos Guide fix already shipped, a failed request must never render as if it succeeded.
- **Restored:** when a saved plan/result is reloaded, the member should be able to tell it's their own prior data, not a fresh blank form.
- **Locked:** a guide-exclusive tool the member doesn't own shows an in-place explanation of what it is and why it's locked, with a path to the owning guide's preview — never a silent redirect straight to checkout.
- **Prefilled:** any field populated from a document or a confirmed Life Graph fact must carry a visible "Pre-filled from your document" (or equivalent) label, and must not be presented as if the member typed it themselves.
- **Pass:** all six states that apply to the route are designed and distinguishable from one another.
- **Fail:** any state renders as blank, generic, silently wrong, or indistinguishable from another state.

### 1d. No assumed member facts or fake example data displayed as personal data
- No tool may show a computed result, a selection, or a starting number before the member has actually entered the underlying fact — per the extensive July 22-24, 2026 input-safety correction pass (Emergency Fund, Credit Card Optimizer's fictional business, Beneficiary Checker's seven accounts, LTD Coverage Checker's policy assumptions, Purchase Planner's assumed comparisons, Offer Comparison's default equity/vesting terms, Medical Cost Planner's flat-copay assumption, and roughly twenty more tools corrected in the same pass).
- An explicitly entered zero is always valid and different from a blank/unanswered field; a route must never conflate the two.
- **Pass:** the route shows no number, selection, or result that could be mistaken for the member's own data unless the member actually entered or confirmed it.
- **Fail:** any fabricated example, default, or silent assumption presented as if it were personal.
- This category has the deepest confirmed remediation history in the canonical record — treat it as **substantially fixed for the tools explicitly named there**, and explicitly **verify** any tool not named in that correction list, since the fixes were applied tool-by-tool, not as one blanket pass.

### 1e. Digestible progressive disclosure, not long text walls
- Per the Brand Guide's anti-direction against "data-dense grid layouts" and in favor of Lantern Logic ("a source of light, not a map"): a route's primary content must be scannable in a few seconds, with secondary detail available on demand (an accessible disclosure control), not printed in full by default.
- Per the Founder Edition's Ten Laws ("Minimize the cost of being understood," "Peace of mind is the metric"): a route that requires the member to read a wall of text to find their own next action has failed, regardless of how accurate the text is.
- **Pass:** the route's primary job is answerable from what's visible without scrolling through unrelated detail; supporting detail is behind a clearly labeled disclosure.
- **Fail:** the route presents a long, undifferentiated block of text or a dense list of everything at once as the default view.
- Confirmed as an active initiative, not yet complete everywhere: the July 22, 2026 "first application-wide UI remediation" introduced a reusable accessible supporting-detail disclosure pattern and applied it to the Tools directory and Emergency Fund specifically. **Verify** this pattern's presence route by route rather than assuming it has propagated everywhere.

---

## 2. Core-Route Acceptance Criteria

For each route: the member's primary job, what must be visible immediately, what belongs behind progressive disclosure, required visual opportunities, navigation expectations, and pass/fail criteria.

### Dashboard / Life Compass (`/life-compass`, "Today")

- **Primary job:** know, in one glance, what matters most right now and what to do about it.
- **Visible immediately:** the payday/next-payment strip, the single governed current-focus card (confirmed change, first Life View priority, or weakest Life Graph zone — in that priority order), and the nearest-6-month milestone timeline.
- **Progressively disclosed:** the full vertical milestone list (behind "view all"), the complete zone-health breakdown beyond the ordered summary strip.
- **Required visual opportunities:** milestone timeline should read as a horizontal, colored strip, not a table; "Progress and wins" should visually distinguish Net-Worth-history-based evidence from goal-contribution-based evidence so the member can tell which kind of proof they're looking at.
- **Navigation:** always the home base; every other route's back path should ultimately lead here.
- **Pass:** exactly one current-focus card is shown (not zero, not multiple competing callouts); milestones shown are all traceable to real recorded facts, with no placeholder dates.
- **Fail:** more than one competing "most important" callout is shown at once (this was an identified anti-pattern the July 22 rebuild specifically removed — a redundant second priority card — so its reappearance is a regression, not a new problem); any milestone with no underlying fact.
- Confirmed built to 94/A design per the canonical record; **verify** authenticated persona, mobile, accessibility, and boundary-date behavior specifically, since those are the explicitly stated remaining gates for this route.

### Life Profile and Life Graph

**Life Profile (`/life-profile/about` → `/review`)**
- **Primary job:** feel genuinely understood while providing the minimum information Krovos actually needs.
- **Visible immediately:** the document-upload card at the top of every step, and a clear "Step X of 8" indicator.
- **Progressively disclosed:** conditional threads (business/self-employed, immigration, starting-over) should only appear once their triggering selection is made, never shown as empty sections in advance.
- **Required visual opportunities:** pre-filled fields need a consistent, unmissable "Pre-filled from your document" treatment across every step, not a per-step reinvention of the label.
- **Navigation:** step-nav dropdown reachable from every step; no standalone `/confirm` page; generation triggers directly from the Review step's CTA.
- **Pass:** every step shows correct conditional threads for its triggering selection and no others; generation flow is Review → loading → Life View with no intermediate page.
- **Fail:** a conditional thread appears without its trigger, or a dead-end step with no forward/back path.

**Life Graph (`/life-graph`)**
- **Primary job:** see the whole life situation as a single connected picture, and understand why each node is scored the way it is.
- **Visible immediately:** the SVG node map (10 nodes, 3 zones), each node's key metric and health color.
- **Progressively disclosed:** the plain-language evidence panel (confirmed vs. missing facts) opens only on tap/select, not shown for every node simultaneously.
- **Required visual opportunities:** zone health colors (Lantern Gold / Ember Orange / muted red) must read correctly in both themes; incomplete nodes should visibly dim (~40% opacity) with a clear "Add" prompt rather than looking broken or unfinished.
- **Navigation:** tapping a node's evidence panel should link directly to the relevant Life Profile step to update that data.
- **Pass:** every node announces its score and status to a keyboard/screen-reader user (confirmed built); the evidence panel explicitly states the score is not a judgment of the member's choices.
- **Fail:** a node with no accessible name/label; an evidence panel that reads as judgmental rather than descriptive.
- Confirmed built and scored 90/A-; **verify** authenticated household edge cases (a connected partner's view of a shared node) specifically, since that is the explicitly named remaining gap.

### Life Goals (`/life-goals`)

- **Primary job:** see what I'm working toward and how close I am.
- **Visible immediately:** active goals with real progress, a clear "+ New goal" action, and the active/completed/abandoned tab structure.
- **Progressively disclosed:** contribution history detail behind opening a specific goal, not shown inline on the list view.
- **Required visual opportunities:** per the Tool Delivery Map's proposed (not yet built) "recommended next" pattern — when it ships, a goal's detail view should visually separate "your goal" from "what Krovos recommends next for this goal," never blend the two into one ambiguous block.
- **Navigation:** reachable directly from the nav shell (Goals); a household-scoped goal should visually indicate shared ownership when a connected partner also has edit access.
- **Pass:** completed/abandoned goals are never permanently deleted (status-based only, confirmed built); a connected partner without edit permission sees a visibly read-only state, not a silently-failing edit control.
- **Fail:** a delete action offered where only status-change should exist; an edit control that appears interactive but silently rejects writes with no visible explanation.

### Retirement and Net Worth

**Retirement (`/retirement`)**
- **Primary job:** know whether I'm on track for the retirement I actually want, and what to change if I'm not.
- **Visible immediately:** the "Just me / Me + partner" selector, and — once a model is saved — the three-scenario comparison against the member's own chosen target date(s), not a solved-for-earliest-date substitute.
- **Progressively disclosed:** Next decisions, Decision tools, and Saved plan should stay visibly disabled until a baseline model is saved, with the workspace explicitly instructing the member to complete and save the model first.
- **Required visual opportunities:** the workspace's first workbench-specific chart and branded baseline empty state are already built — this is the template every other dense tool should eventually match, not a one-off.
- **Navigation:** reachable from the nav shell (Explore); tools launched from within Retirement should return to Retirement, not strand the member in the standalone tool.
- **Pass:** the comparison holds the member's entered target date(s) fixed and reports funding percentage/gap against that date, rather than silently substituting a different "optimal" date.
- **Fail:** any scenario shown before the baseline is saved; any silently-substituted target date.
- Scored 98/A+ per the canonical record; **verify** with a real human comprehension walkthrough regardless of that score, since the record itself states this "cannot honestly be marked complete by code alone."

**Net Worth (`/net-worth`)**
- **Primary job:** see one honest number for where I stand, right now.
- **Visible immediately:** the current aggregated total and its trend, if history exists.
- **Progressively disclosed:** account-by-account and category-by-category breakdown behind a disclosure, not printed in full by default.
- **Required visual opportunities:** per the Tool Delivery Map, this route should eventually surface a single "what this changes" prompt when a meaningful asset/liability event occurs — not yet built; do not claim it exists.
- **Navigation:** reachable from the nav shell (Explore).
- **Pass:** the total reflects combined household data only when `financial_shared` is true; an unconnected or non-sharing account never sees combined figures.
- **Fail:** any leak of a connected partner's figures into an account that hasn't enabled sharing.

### Life Labor and Life Calendar

**Life Labor (`/life-labor`)**
- **Primary job:** see whether household work is actually divided the way it feels, without turning it into a scoreboard.
- **Visible immediately:** the task list, filtered by effort/energy if a filter is active, and the Money and Home Work correlation view as a plain-fact statement, never a judgmental score.
- **Progressively disclosed:** the household facts layer and Seasons rebalancing detail behind their own tabs/sections, not shown inline with the task list.
- **Required visual opportunities:** income and home-labor percentage bars must remain visually separate, never combined into a single blended score, per the explicit product decision recorded in the canonical record.
- **Navigation:** promoted into the universal Explore menu (confirmed built).
- **Pass:** a brand-new workspace begins genuinely empty with an explanation, not a generic seeded task list (confirmed corrected July 23-24, 2026); paid-hours fields begin blank, not defaulted to 40.
- **Fail:** any seeded/default data appearing as if it were the member's own household's data.

**Life Calendar (`/life-calendar`)**
- **Primary job:** see everything dated — household, personal, work, and guide-linked — in one place, without work bleeding into personal or vice versa.
- **Visible immediately:** the current month with color-coded, independently toggleable layers.
- **Progressively disclosed:** provider-connection detail (Google/Outlook/Apple) collapsed by default, opening only when a connection needs attention (confirmed built).
- **Required visual opportunities:** each date should be a native, keyboard-operable control with a screen-reader label including its date and item count (confirmed built); unselected/selected-empty/no-upcoming-events states must each give clear direction, not render as blank space (confirmed built).
- **Navigation:** reachable from the nav shell (Calendar).
- **Pass:** work-layer events never appear editable or visible to a connected partner in any setting combination; an undated recurring Life Labor task never appears on the calendar.
- **Fail:** any leak of an owner-only work event to a connected partner; any undated task appearing on a calendar date.
- Google import confirmed live and verified; Microsoft and Apple import remain **pending, do not test as complete**.

### Krovos Guide (Ask Krovos, `/krovos-guide`)

- **Primary job:** ask anything and get an answer that already knows my situation, without having to re-explain it.
- **Visible immediately:** relevant starter prompts (life-phase-aware once Life Graph data exists, generic-but-useful when it doesn't), and the low-emphasis privacy/AI-details disclosure link rather than a long processing disclaimer.
- **Progressively disclosed:** the full accurate processor/context disclosure lives on the Privacy page, not repeated inline on every message.
- **Required visual opportunities:** the conversation history/selector must have confirmed Daylight-mode contrast (this was a specific regression found and fixed) — **verify** it remains fixed after any subsequent nav or theming change.
- **Navigation:** reachable from the nav shell (Ask Krovos); a life-event-triggered card elsewhere in the app (per the Tool Delivery Map) should be able to open a Guide conversation pre-loaded with a specific prompt, without spending an API call until the member chooses to send.
- **Pass:** a failed request keeps the prior conversation intact, states plainly that the message wasn't sent, and offers a real retry — never renders as if an answer was received.
- **Fail:** any failed request that renders silently, ambiguously, or as if it succeeded.

### Guides Index and Guide Detail (`/life-phase-guides`, `/guides`)

- **Primary job (index):** understand which guide, if any, is relevant to my actual situation, without having to evaluate all sixteen.
- **Visible immediately (index):** guides most relevant to the member's recorded Life Graph facts should rise toward the top (confirmed built); the $49 one-time price stated once, not repeated on every card.
- **Progressively disclosed (index):** full guide list remains available for self-directed browsing, but relevance-ranking is the default presentation.
- **Primary job (detail/preview):** understand exactly what this guide adds before paying for it.
- **Visible immediately (detail):** the durable outcome, key enhancements, and included tools — in that order, per the Guide commerce journey documented in `product/navigation-tool-guide-map-2026-07-23.md`.
- **Progressively disclosed (detail):** individual tool descriptions behind a expandable list, not all rendered as full paragraphs by default.
- **Required visual opportunities:** guide-preview content needs confirmed Daylight contrast (this was a named regression found and fixed) — **verify** it stays fixed.
- **Navigation:** index and detail must never send a member directly to checkout without passing through the outcome explanation first.
- **Pass:** a locked tool's dialog explains the owning guide and offers to preview it, never redirects straight to Stripe checkout.
- **Fail:** any direct-to-checkout redirect from a locked-tool click, or any guide card repeating the price string.

### Tools Catalog and Individual Tools (`/tools`)

- **Primary job (catalog):** find a specific tool when I already know what I want, without being pushed to buy anything.
- **Visible immediately (catalog):** Core Tools grouped into their five sub-categories (Money Basics, Home, Family, Health, Retirement and Legacy), always unlocked; Guide Tools grouped by guide with visible lock state.
- **Progressively disclosed (catalog):** the Guide Tools section stays behind one optional disclosure, per the confirmed July 23 rebuild — the catalog is a secondary library, not the default landing experience.
- **Primary job (individual tool):** get a real answer to a specific question using only what I've actually told Krovos.
- **Visible immediately (individual tool):** the blank/unconfigured state's explanation of exactly what's needed before a result appears.
- **Progressively disclosed (individual tool):** supporting detail (methodology notes, assumption disclosures) behind a disclosure, not printed above the fold.
- **Required visual opportunities:** live U.S. thousands-separator formatting on every money field (confirmed built as an application-shell-wide formatter); a locked guide tool renders at roughly 50% opacity with a lock icon, never fully hidden.
- **Navigation:** clicking a locked tool routes to its owning guide's preview, never to checkout directly.
- **Pass:** no tool renders a result before its required real inputs are present; a locked tool remains visible and readable, just not interactive toward its result.
- **Fail:** any tool computing a result from blank/default inputs; any locked tool that's invisible rather than greyed-and-explained.

### Settings (`/settings`)

- **Primary job:** control my account, sharing, and connections without guessing at what state I'm in.
- **Visible immediately:** the correct one of three connection-state panels — unconnected (invite form), primary (connected/active with sharing toggles), or connected party (connected/active, no remove-connection control).
- **Progressively disclosed:** the adult-migration claim generator only appears once a tracked child crosses 18 (age-gated, hidden entirely before that).
- **Required visual opportunities:** an account/person icon for the settings entry point rather than a gear icon (a specific, confirmed correction); theme toggle should be present and immediately usable from this page.
- **Navigation:** reachable from every authenticated route via the shell; the wordmark on this and other standalone-nav pages must link back to home (a specific, confirmed correction — this was previously an unlinked div).
- **Pass:** each of the three connection states renders its correct panel with no dead branch (this exact bug — an unreachable "Remove connection" button and a permanently-inaccessible invite form for new accounts — was found and fixed once already; treat it as a specific regression risk, not a settled fact).
- **Fail:** any of the three states rendering the wrong panel, or the Remove-connection control appearing unreachable again.

---

## 3. Ten Highest-Impact Visual Improvements Before Beta

Grouped by evidentiary status, per the instruction not to invent completed work.

### Confirmed current gaps (the canonical record explicitly states these are still open)

1. **Meaningful route-specific illustrations, diagrams, richer charts, and branded empty states, application-wide.** The canonical record calls this "a required pre-beta product gate across the application" and states plainly that only Retirement has one workbench-specific chart and one branded empty state so far — "this does not mark the full application visual-composition pass complete." This is the single largest confirmed gap in this document.
2. **Full Daylight Lantern coverage.** Multiple named regressions were found and fixed individually through July 23, 2026 (nav text, Guide conversation selector, Guide-preview content, a legacy inline-background safety layer, a native-select conversation-history treatment). The pattern of repeated one-off fixes is itself evidence that a comprehensive light-mode pass has not yet happened; do not treat any single fix as proof the whole app is covered.
3. **Full mobile and tablet responsive coverage.** A twelve-destination responsive matrix (phone/tablet/desktop) has been completed for the Core money tools, but tablet layout specifically and Windows PWA install are still listed as open items in `CLAUDE.md`'s own backlog and were not confirmed resolved anywhere in the canonical record.
4. **Accessible naming for controls beyond the named batch already fixed.** Life Labor's 25 controls, the Krovos Guide conversation/message controls, and Settings' partner-email field were confirmed fixed in one pass — but the canonical record itself states "full route-by-route... QA remains a controlled continuation, not represented as complete," meaning other unnamed controls may still exist elsewhere in the app.

### Items requiring live verification (built per the record, but not confirmed via a real, current production check)

5. **Retirement's full human-comprehension pass.** Scored 98/A+ by the internal design audit, but the record itself says this score "cannot honestly be marked complete by code alone" — a real founder walkthrough is the only thing that can close this.
6. **Settings' three-connection-state panel correctness**, re-confirmed after the universal app-shell change. This exact bug was found and fixed once (July 16, 2026); it has not been re-verified since the later nav/shell rebuilds that touched Settings' surrounding chrome.
7. **External calendar provider states beyond Google.** Google OAuth is live and verified; Microsoft OAuth and a founder-authorized real Apple iCloud import remain pending — do not present these as visually or functionally complete.
8. **The relationship between `/dashboard` and `/life-compass`.** Earlier build notes reference a `/dashboard` route independent of Life Compass (e.g., "Dashboard no longer mounts a second navigation"), but the canonical record never fully resolves whether this is a distinct live route or a legacy alias. Confirm which is true before treating either as the definitive home base in visual QA.

### Intentionally deferred (do not treat as a defect)

9. **Light-mode-native logo/hero art.** The current KrovosIcon is optimized for dark backgrounds; a light-mode-native version of the icon and the landing-page hero panel was discussed and explicitly deferred pending outside feedback on whether dark-on-light reads correctly for the brand. This is a deferred design decision, not a missed fix — do not report it as a bug.
10. **Family Mode's four focus-area labels with no ordered workflow.** Family Mode correctly activates/deactivates for the right household composition, but its configured priority-tool sequence is not rendered — the canonical record explicitly calls this "a dedicated future product workstream, not represented as fixed by this detector pass." Visually, this will look unfinished; it is a known, intentionally sequenced gap, not a regression to chase down before beta.

---

## 4. Preserving the White-Glove, Burden-Relief Standard

Every acceptance criterion above should be read against one question, restated from the Founder Edition: **would the guide you always needed but never had present it this way?** A route that is visually polished but still asks the member to browse, self-select, or interpret a wall of numbers has not met the standard — burden relief is a design requirement, not a bonus. Conversely, a route with an honest, well-labeled blank state and a clear next step meets the standard even before every visual polish item above is complete. Sequence the ten items in Section 3 accordingly: confirmed gaps first, verification items second, and never let the deferred items in the third group get re-opened as if they were newly discovered defects.
