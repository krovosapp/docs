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
