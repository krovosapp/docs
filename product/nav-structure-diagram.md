# Navigation and Access Structure

**Established:** July 17, 2026
**Source:** `app/components/GlobalNav.tsx`, `proxy.ts`, `app/tools/page.tsx`, `app/life-phase-guides/[slug]/page.tsx`, as they exist post the July 17, 2026 tool renaming pass and `/tools` locked-tool rebuild. Re-verify against these files directly before trusting this diagram in a future session -- nav structure and tool routes are the parts of this codebase most likely to drift.

This replaces the long-standing backlog item asking for a visual (not text-only) map of how a user reaches each guide and tool.

---

## Top-level navigation (every page, via GlobalNav)

```mermaid
flowchart TB
    subgraph Primary["Primary nav (always visible)"]
        LC["Life Compass\n/life-compass\nHome base"]
        KG["Krovos Guide\n/krovos-guide"]
        RET["Retirement\n/retirement\nCore, public"]
    end

    subgraph Secondary["Secondary nav (dropdown, less prominent)"]
        GU["Guides\n/life-phase-guides"]
        TO["Tools\n/tools"]
        LP["Life Profile\n/life-profile"]
        LG["Life Graph\n/life-graph"]
        NW["Net Worth\n/net-worth"]
        LV["Life View\n/life-view"]
        LCAL["Life Calendar\n/life-calendar"]
    end

    LC -.->|all roads route back here| Primary
```

---

## `/tools` structure (post July 17, 2026 rebuild)

`/tools` is one page in two sections. Section 1 (Core Tools) is never gated. Section 2 (Guide Tools) shows every tool but locks the ones tied to a guide the signed-in user does not own.

```mermaid
flowchart TB
    TOOLS["/tools"]

    TOOLS --> CORE["Core Tools\n(never locked)"]
    CORE --> MB["Money Basics\n9 tools"]
    CORE --> HOME["Home\n2 tools"]
    CORE --> FAM["Family\n2 tools"]
    CORE --> HEALTH["Health\n1 tool"]
    CORE --> RETL["Retirement and Legacy\n7 tools"]

    TOOLS --> GUIDET["Guide Tools\n(grouped by guide, lock state per tool)"]
    GUIDET --> G1["one section per guide\n(17 guides)"]

    G1 --> OWNED{"Does the signed-in user\nown this guide, or a\nqualifying guide via\nOR-logic, or inherit it\nfrom a connected primary?"}
    OWNED -->|Yes| UNLOCKED["Tool renders normally\nClick -> the real tool route"]
    OWNED -->|No| LOCKED["Tool renders greyed,\nlock icon, ~50% opacity\nClick -> /life-phase-guides/[slug]"]
```

Ownership check (client-side, on `/tools` mount): query `life_phase_guides` for id+slug, query `user_guide_purchases` for the signed-in user's owned guide ids, and for any guide not directly owned, check `/api/household/guide-access` (connected-subscriber inheritance from a primary subscriber). Six tools carry more than one qualifying guide slug (OR-logic) and unlock if the user owns *any* of them: Life Insurance Calculator (New Parent or Estate Planning), FAFSA Remarriage Planner (College Planning or Blended Family), QTIP vs. Bypass Comparison (Blended Family or Estate Planning), and the Divorce/Widowhood trio -- Emergency Fund Rebuilder, Single Income Planner, Credit Rebuilding Planner.

---

## Guide page structure

```mermaid
flowchart LR
    MKT["/life-phase-guides\nmarketplace, 5 clusters"] --> SLUG["/life-phase-guides/[slug]\none page per guide"]

    SLUG --> GATE{"Owned?"}
    GATE -->|Yes| CONTENT["Full guide content\n+ Tools for this guide\nsection, GUIDE_TOOLS lookup"]
    GATE -->|No| CTA["Purchase CTA, $49 one-time\n(or the two CTAs if the guide\nshares an OR-logic tool)"]

    CONTENT --> TOOLLINK["Each tool link ->\nthe real tool route directly\n(already confirmed owned,\nno lock check needed here)"]
```

The client-side check on the guide page itself (`GuideGate`, wrapping every guide-exclusive tool route directly) is a second, independent gate below the `/tools` lock treatment above -- a user who bypasses `/tools` entirely and pastes a tool URL directly still hits `GuideGate`, which redirects unauthenticated visitors to sign-in and shows the same purchase CTA to signed-in non-owners. `/tools`'s lock treatment is a discoverability/UX layer; `GuideGate` is the actual enforcement layer, per the July 15, 2026 "Guide-Exclusive Tool Enforcement" decision in CLAUDE.md.

---

## Where a locked tool click actually goes

```mermaid
flowchart LR
    CLICK["User clicks a locked tool\non /tools"] --> GUIDEPAGE["/life-phase-guides/[slug]"]
    GUIDEPAGE --> CHECK{"Owned?"}
    CHECK -->|No| CTA2["Purchase CTA shown"]
    CHECK -->|"Yes (edge case: ownership\nchanged between page load\nand click)"| REAL["Full guide content,\nreal tool link available"]
