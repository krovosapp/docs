# App UI and Input Audit

**Date:** July 22, 2026  
**Scope:** Krovos application shell, Core areas, tool catalog, and all catalogued tool routes  
**Status:** First remediation batch complete; continued page-level visual QA remains a release gate

## Input reliability

The Emergency Fund fields lost focus after one character because `MoneyField` was declared inside the page component. Each keystroke updated state, recreated that component type, and caused React to replace the focused input. `MoneyField` now has stable module-level identity.

A repository regression audit now checks every route in the canonical tool catalog. The first run covered 72 unique routes and 331 input elements and found no other nested field component with the same remount risk. This is source-level coverage, not a substitute for authenticated browser and mobile testing of calculations, formatting, validation, and assistive technology.

### Currency-entry standard

All application routes now mount one currency-entry behavior. It recognizes explicit currency fields, dollar-adorned fields, and semantically labelled money fields; excludes percentages, ages, durations, counts, scores, and other non-money numbers; displays U.S. thousands separators live; preserves up to two decimal places; and passes comma-free values into existing React state and calculation paths. Native number fields are handled without allowing the formatted presentation to suppress their existing `onChange` behavior.

Live verification covered a dollar-adorned controlled field, a legacy native-number money field, async restored values, a percentage field, an age field, and a duration field. `$12,345` display was confirmed to update the Emergency Fund calculation to `$12,345/month`, while a formatted `$12,345` Investment Priority input removed the zero-amount blocker. Restored `$9,604` and `$36,214` values also formatted after asynchronous account prefill. Non-money percentage, age, and months-of-spending inputs remained unformatted.

## Portfolio-level UI findings

### What is strong

- The Midnight Slate, Lantern Gold, Ember Orange, and Mist Blue system is distinctive and consistent.
- Most tools already separate input, result, status, and saved outcome into cards.
- Life Profile demonstrates the right progressive-disclosure pattern through completed-state accordions.
- Results frequently include comparison cards, progress bars, timelines, or state color rather than text alone.

### What is creating overload

1. The Tools directory rendered every category and every tool simultaneously, making discovery feel like a long inventory.
2. Supporting education is often presented with the same visual weight as the next decision.
3. Some long pages repeat low-emphasis paragraph cards instead of collapsing reference material behind an explicit member choice.
4. Dense pages need a consistent first screen: purpose, known context, next input, then result. Explanatory detail should follow the result or live in disclosure.
5. Visual appeal should come from meaningful state, progress, comparison, time, and relationships, not decorative charts with no decision value.

## Changes in this batch

- Rebuilt the Tools directory as a category-first library. It now shows three visual summary metrics and keeps categories collapsed until selected. Money Basics opens initially so a member has an obvious starting point.
- Added a reusable accessible progressive-disclosure component for secondary education.
- Applied progressive disclosure to Emergency Fund account-placement guidance.
- Added subtle brand-light ambient depth to dark and light application backgrounds and a consistent selection treatment.
- Added a readable text-measure utility for explanatory copy.
- Added `npm run audit:tool-inputs` so the all-tool focus-remount check can run in CI and before releases.

## Required continuation batches

1. Apply the purpose, next action, result, supporting detail hierarchy to the highest-density tools first: QDRO Tracker, Life Labor, QDRO Readiness, Offer Comparison, QTIP vs. Bypass, Business Structure, Severance Bridge, Student Loan Repayment, Gig Income, Estate Tax, and Inherited IRA.
2. Review every Core area at 320, 768, 1024, and 1440 pixel widths with real long values and empty, loading, success, error, and restored states.
3. Replace paragraph-only outcome blocks with truthful visuals where a relationship benefits from one: progress, sequence, comparison, allocation, or trend.
4. Run keyboard, focus-order, zoom, reduced-motion, contrast, and screen-reader checks.
5. Observe founder walkthroughs before assuming lower text density also means lower cognitive burden.

The application-wide direction is now documented and the shared foundation is in place. The remaining route-by-route work should be shipped in controlled batches rather than making dozens of unverified presentation changes at once.

## Universal navigation, honest defaults, and Guide discovery, July 23, 2026

The application shell now owns navigation once at the root instead of relying on individual pages to remember it. Every authenticated application route inherits:

- a linked Krovos wordmark that returns the member to Today;
- Today, Goals, Calendar, and Ask Krovos as the persistent primary destinations;
- a compact Explore menu for Guides, Tools, Life Profile, Life Graph, Net Worth, and Retirement;
- a visible theme control and Settings entry;
- a route-aware back path so a tool returns to Tools, a Guide preview returns to Guides, and deeper profile steps return to Life Profile.

The landing, sign-in, sign-up, privacy, terms, and Life Gaps surfaces retain their purpose-built public headers, with linked Krovos home navigation. This avoids authenticated Life Graph behavior on public acquisition and account-entry routes while preserving the same escape path and theme access where applicable.

Tool entry states were also corrected. Hard-coded numeric examples were removed from tool state rather than merely restyled as placeholders. Personal-choice controls such as filing status, coverage tier, school type, ownership type, utilization, life stage, platform, and state now start unselected. A value may appear only when a member enters it or a documented Life Profile/Life Graph prefill supplies it. Internal calculation fallbacks do not display as member answers.

Guide discovery now follows one coherent journey:

1. The Tools library keeps Guide tools behind one optional disclosure rather than placing the entire locked catalog in the default scan path.
2. A locked tool opens an in-place explanation of what it does and which Guide includes it. It does not navigate the member away.
3. The member may keep exploring or intentionally open the Guide preview.
4. The Guides index states the common $49 one-time price once, uses concise descriptors, and links each Guide to its preview.
5. The Guide preview explains its durable outcome, highlights, and included tools before presenting the checkout action. Locked tools remain visibly identifiable and non-interactive until purchase.

`npm run audit:app-shell` now protects this structure. Its initial passing result covered 113 application routes, 72 unique tool routes, and all 67 Guide-linked tool routes. It also rejects page-level duplicate navigation, hard-coded numeric member-input defaults, missing root navigation, missing locked-tool explanation behavior, and missing Guide preview outcomes. `npm run audit:tool-inputs`, TypeScript, and the production build remain companion gates.

This closes the structural dead-end and merchandising problems. It does not claim that every content-heavy page has completed its individual visual-density redesign. The route-by-route responsive, accessibility, loading/error/restored-state, and founder walkthrough review listed above remains required.

## Daylight Lantern and retirement workspace correction, July 23, 2026

Light mode is now a strict two-choice system: Midnight Slate or Daylight Lantern. The former Auto mode was removed from the provider, compact toggle, expanded selector, and pre-hydration script. Existing stored Auto preferences safely migrate to dark rather than leaving a third state that appears unresponsive.

Daylight Lantern now remaps the previously missed 95-percent navigation background and common legacy inline dark-mode colors, translucent panels, borders, and form controls. This closes the most visible contrast gaps on older pages that could not be reached by Tailwind utility remapping alone. Browser review confirmed a warm editorial surface, dark primary text, stronger blue supporting text, visible card borders, and a matching light navigation shell on Retirement and Krovos Guide. The application-wide route review remains necessary because bespoke SVGs and unusual inline color strings may still require targeted corrections.

Krovos Guide no longer opens with a long processor disclosure that competes with the invitation to ask a question. The first screen focuses on the member benefit. The persistent low-emphasis footer now combines the existing guidance disclaimer with a `Privacy & AI details` link. The full, accurate disclosure remains available on the Privacy page.

Retirement was rebuilt from a long vertical collection into one connected workspace:

- Net Worth, retirement assets, active Life Goals, and income/spending inputs appear as connected context at the top.
- Four guided views separate plan modeling, next decisions, saved evidence, and specialized tools.
- The model uses a three-step input path and keeps three editable scenarios in the same field of view.
- Each scenario independently adjusts savings and return assumptions and immediately displays the retirement-age, savings, target, and portfolio impact.
- Active-goal funding is shown separately and reduces modeled retirement capacity only through an explicit visible input; Krovos never silently ignores or double-counts goals.
- The Tools view recommends a small decision-based set first and keeps the full library collapsed until requested.

This correction improves usability without claiming predictive certainty. Return rates and withdrawal rates remain labeled planning assumptions. The model still requires controlled calculation-boundary, mobile, accessibility, authenticated restoration, and household testing before production-readiness grading.

## Corrective accessibility, Guide merchandising, and household Retirement pass, July 23, 2026

This pass corrects regressions found during the founder walkthrough and replaces several incomplete claims above with verified behavior:

- Daylight Lantern now remaps the missing 90, 75, 65, and 55 percent Soft Fog text utilities, the legacy `#242C3D` panel, and the missing 90 and 65 percent Lantern Gold text utilities. Browser inspection confirmed dark readable global-navigation text on the light surface, a light Krovos Guide conversation selector, and readable Guide-preview title, body, outcome, price, and tool text.
- Settings now uses an account/person symbol rather than the rejected gear. The Dashboard's page-level navigation was removed, leaving the root shell as the single navigation source.
- `/guides` is now the primary application path. Existing `/life-phase-guides` routes remain as compatibility routes so saved links do not break.
- Guide cards now use two concise sentences each. Life Graph signals promote three likely-relevant Guides to the first section without hiding the complete catalog. The common price is stated once on the index, and the preview purchase button no longer repeats `$49`.
- Guide previews now sell a finished member outcome and three numbered actions instead of a dash-heavy list. They explain that Krovos applies known information, asks the member to confirm gaps, and saves the finished outcome.
- Retirement tools opened from Retirement carry `from=retirement`; the global back path returns to Retirement instead of the generic Tools directory.
- Budget and Spending is explicitly available from Explore and the Life Compass payday strip. It is the recurring cash-flow workspace; Net Worth remains the balance-sheet view.
- The Retirement model now restores saved inputs, pre-fills saved FIRE balances and target ages, derives available annual saving from recorded household take-home minus spending, and keeps values intact while the member moves between steps. A fourth household step is present when a partner is recorded.
- Staggered retirement is once again a real calculation. Krovos converts both current ages and both target ages into calendar timing, projects the portfolio to the first retirement, then uses the shared bridge-period engine to model continuing household income, annual funding gap, portfolio withdrawals, portfolio at withdrawal start, and the second retirement. If no withdrawal-start age is supplied, the second retirement is the default. The three scenarios remain visible and independently adjustable.
- An active FIRE plan is identified in the Retirement workspace with a direct route to review its source facts.

### Verification performed in this pass

- TypeScript and the full production build pass across 153 generated routes.
- `audit:tool-inputs` passes across 72 catalog routes and 331 inputs.
- `audit:app-shell` passes across 115 inherited-navigation routes, 72 unique tools, and 67 Guide-linked routes.
- Authenticated browser review at 320, 768, 1024, and 1440 pixels found no horizontal overflow on Retirement. Its visible controls had accessible labels in the tested state.
- Repository-wide ESLint still fails on the existing broad backlog of 378 errors and 116 warnings. This is not represented as a passing gate.

### Durable controlled-release checklist

The following work is not allowed to disappear into task history. A box may be marked complete only with recorded evidence.

- [x] Urgent Daylight Lantern navigation, Guide selector, and Guide-preview readability.
- [x] Single Dashboard navigation, linked home path, visible Settings path, and revised Settings icon.
- [x] Consistent Guide-card copy, improved preview value framing, single CTA price, personalized ordering, and `/guides` primary path.
- [x] Context-aware Retirement-tool back navigation.
- [x] Budget location and payday access made explicit.
- [x] Retirement input restoration, Life Graph/FIRE prefill, guided progression, partner ages, separate retirement dates, and material bridge-period calculations.
- [x] Retirement responsive overflow check at 320, 768, 1024, and 1440 pixels for the authenticated founder state.
- [ ] Retirement keyboard-only focus order, 200 percent zoom, reduced-motion, screen-reader announcement, extreme calculation boundaries, two-account household permissions, cross-device restoration, and novice beta comprehension.
- [ ] Empty, loading, error, partial-prefill, restored, and long-value visual states for every Core area at all four widths.
- [ ] Purpose, known context, next action, result, and supporting-detail hierarchy applied and verified on QDRO Tracker, Life Labor, QDRO Readiness, Offer Comparison, QTIP vs. Bypass, Business Structure, Severance Bridge, Student Loan Repayment, Gig Income, Estate Tax, and Inherited IRA.
- [ ] Truthful progress, sequence, comparison, allocation, or trend visuals added only where they improve a decision.
- [ ] Contextual Life Graph and Life Goal delivery mapped for all 72 tools, including why the tool is being surfaced and where its saved outcome returns.
- [ ] Full authenticated founder walkthrough and subsequent novice beta walkthroughs. Lower text density alone is not accepted as evidence of lower cognitive burden.

App commit `46d9829` and docs commit `c12d0c9` are pushed to the Krovos-owned `main` branches. Vercel production deployment `dpl_7y91jhfMgWv75hiXwcy9Xq3RGwMP` reached Ready. An unauthenticated live request to `www.krovos.app/guides` correctly preserved the requested destination and routed to sign-in; authenticated production visual verification remains part of the founder walkthrough gate.
