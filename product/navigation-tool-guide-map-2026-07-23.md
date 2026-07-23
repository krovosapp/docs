# Krovos Navigation, Tool, and Guide Map

**Date:** July 23, 2026  
**Purpose:** Record where tools belong, how members discover them, and how the Guide purchase journey works.

## Navigation model

The persistent authenticated navigation is intentionally small:

- **Today:** Life Compass, the member's current focus and next action.
- **Goals:** the outcomes the member is working toward.
- **Calendar:** dated household, personal, work, and Guide-linked actions.
- **Ask Krovos:** contextual guidance using the member-approved Krovos context.
- **Explore:** the secondary library containing Guides, Tools, Life Profile, Life Graph, Net Worth, and Retirement.
- **Settings:** account, privacy, sharing, subscription, and access controls.

The Tools library is not the intended primary experience. It is a complete secondary library and fallback. Life Compass, Life Goals, Life Profile, Life Graph, Core hubs, and purchased Guides should surface the right tool when the member's known facts, goal, or current decision makes it useful.

## Tool ownership map

The source catalog currently contains 72 unique tool routes. Every Guide-linked route is present in the same catalog. Tools are organized into five Core areas and sixteen Life Phase Guide areas.

### Core

- **Money Basics:** net worth, debt payoff, paycheck planning, savings goals, emergency fund, credit health, credit card structure, purchase planning, and milestones.
- **Home:** mortgage and refinance decisions that belong to the ongoing Core financial picture.
- **Family:** shared household and protection decisions that remain relevant outside a purchased life phase.
- **Health:** ongoing medical and benefits decisions.
- **Retirement and Legacy:** retirement readiness, withdrawal, Social Security, required distributions, beneficiaries, and ongoing legacy maintenance.

### Life Phase Guides

- Early Career
- Newlywed
- Immigration
- Home Buying
- New Parent
- College Planning
- Career Transition
- Starting a Business
- Gig Work
- Divorce
- Caregiving
- Disability
- Widowhood
- Blended Family
- Estate Planning
- Inheritance

The catalog is the canonical route map. A tool may be relevant to more than one context, but it has one clear ownership and access rule. Relevance elsewhere should be expressed as a recommendation or cross-link, not as a duplicate implementation.

## Member discovery rules

1. Do not ask the member to scan all tools to understand what Krovos can do.
2. Prefer one strongest recommendation tied to a known fact, goal, deadline, or unresolved decision.
3. Explain why the tool is relevant before asking the member to open it.
4. Never invent a member fact to create a recommendation.
5. Keep the full Tools library available under Explore for self-directed members.
6. Keep Guide tools out of the default Tools scan path. When deliberately explored, show their locked state and explain the owning Guide without redirecting.

## Guide commerce journey

The Guides index is a discovery surface, not a checkout wall. It communicates the shared one-time $49 price once and sends each card to a dedicated Guide preview. The preview must answer:

- What durable outcome does this add to Core?
- Which knowledge gaps does it close for this life phase?
- How does it use what Krovos already knows without another report-generation charge?
- Which tools are included and what does each one do?
- What remains available after the immediate transition?

Checkout belongs after that explanation. Index cards and locked-tool dialogs should never send a member directly to payment.

## Automated evidence

`npm run audit:app-shell` verifies:

- the root application shell owns navigation;
- individual pages do not create inconsistent copies;
- all Guide tool routes are catalogued;
- locked tools use an in-place explanation;
- Guide tools use progressive disclosure;
- Guide previews contain explicit outcomes;
- tool pages contain no hard-coded numeric member-input defaults.

Latest verified inventory: 113 application routes, 72 unique tool routes, and 67 Guide-linked route mappings.
