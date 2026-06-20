---
title: "Product OS"
description: "Complete product architecture, engines, and V1 specification."
---

**KROVOS**	PRODUCT OPERATING SYSTEM

**KROVOS**

Product Operating System

 

*The guide you were never given.*

*How Krovos Works — The complete system design for the product experience: onboarding, intelligence engines, interaction model, and V1 specification.*

v1.0  |  June 2026  |  Confidential

**SECTION 1**

**Product Philosophy**

**Capabilities Without Complexity**

Krovos contains a large and expanding set of capabilities — document extraction, life phase analysis, financial modeling, AI guidance, burden identification, opportunity surfacing, and more. The user should never feel the weight of this breadth.

| *Capabilities without complexity. The tools are invisible. The navigation is visible.* |
| --- |

This is not a UX principle — it is a product philosophy. Krovos is not a toolbox a user opens and selects from. It is a navigation system that surfaces the right capability at the right moment based on what it knows about the user's life. The user does not choose which calculator to run. The system determines what is relevant and presents it as guidance.

**Navigation Over Features**

Every product decision should be tested against this question: does this surface navigation or features? Features answer the question "what can I do here?" Navigation answers the question "what should I do next?" Krovos is always trying to answer the second question.

**The Product Is the Relationship**

The product is not the Life Understanding Report. It is not the Navigation Scoreboard. It is not any individual engine or feature. The product is the relationship — the accumulated experience of being understood, guided, and supported across time. Every screen, every interaction, every AI response is an expression of that relationship.

**SECTION 2**

**The Smart Start System**

**Design Philosophy**

"Onboarding" undersells Smart Start. Smart Start is the beginning of a relationship. The goal is not to collect data. The goal is to make the person feel genuinely understood before a single recommendation is made.

The critical design tension: understanding a life well enough to guide it requires significant information. But asking too many questions creates friction that drives abandonment. Krovos resolves this tension through three principles:

- Passive understanding first — what can be learned without asking?

- Progressive disclosure — only ask what is needed now; build understanding over time

- Minimize the cost of being understood — make sharing feel natural, not onerous

**The Document Upload Layer**

The most efficient path to understanding is not questions — it is documents. A pay stub, a recent tax return, and an insurance card contain more information about a person's situation than a 50-question questionnaire. And they eliminate the cognitive burden of self-reporting numbers the person may not remember accurately.

Each document uploaded eliminates a category of questions. The intake that follows is targeted at what documents did not cover — life phase context, goals, upcoming decisions, and the emotional and relational dimensions that documents cannot capture.

**The 10-Step Flow**

| **1** | **Identity** Name and state. Two questions. Everything else comes from documents and guided intake. |
| --- | --- |

| **2** | **Documents** Upload pay stubs, tax returns, mortgage statements, investment accounts, insurance cards, loan statements, and more. AI extraction reads and structures them all. |
| --- | --- |

| **3** | **Confirm** Review what was extracted — paginated by section with inline editing. Pre-filled from documents. Correct anything off. |
| --- | --- |

| **4** | **Housing Details** Pre-filled from mortgage statement. Home value added for equity calculation. |
| --- | --- |

| **5** | **Income** Income types detected from documents shown as chips — pre-selected, adjustable. Take-home and frequency pre-filled. |
| --- | --- |

| **6** | **Spending** Three paths: upload bank statement for AI extraction, upload budget spreadsheet, or enter manually by category. |
| --- | --- |

| **7** | **Investments** Pre-filled from investment statements. Separated by account type and owner. |
| --- | --- |

| **8** | **Debt** Pre-populated from pay stub deductions and loan statements. Balance and rate added to complete the picture. |
| --- | --- |

| **9** | **Retirement Goals** Target retirement age, monthly income needed, lifestyle preference, planning horizon, top concerns. |
| --- | --- |

| **10** | **Review** Complete Life Graph summary before report generation. |
| --- | --- |

**SECTION 3**

**The Life Understanding Report**

**What It Is**

The Life Understanding Report is the first product the user receives. It is the recognition moment — the point where Krovos demonstrates that it has genuinely understood this person's life, not as a demographic profile, but as a specific human situation.

The report is not a financial summary. It is not a score. It is a reflection — a clear, specific articulation of what Krovos now understands about this person, organized in a way that creates the felt sense of being seen.

**Report Architecture**

| **01** | **Your Situation** A powerful opening that makes the person feel seen. Names every household member, cites specific facts, and frames the situation honestly. The "that's exactly me" moment. |
| --- | --- |
| **02** | **Financial Snapshot** Household income, invested assets, mortgage, debt — honest, clear, and specific to their numbers. What is strong. What needs attention. |
| **03** | **What You Are Carrying** The burden inventory. Prioritized by cost of leaving unresolved. Each burden has a specific next step. Recognition before recommendation. |
| **04** | **Your Opportunities** Quantified opportunities specific to their situation. Employer match gaps, government program eligibility, insurance optimization, refinancing windows, investment account strategies. |
| **05** | **What To Do First** Ranked priority actions with timeline, specific next step, and definition of success. Never generic. Never hedged. |
| **06** | **Moving Forward** Strengths acknowledged with substance. Path forward specific to them. One action they can take today. |

**The Recognition Test**

Every Life Understanding Report should be evaluated against a single test: does the user experience "that's exactly me"? This felt sense of recognition is the product promise made real. If a report could have been generated for anyone, it has failed. Krovos knows this person.

**SECTION 4**

**The Navigation Engines**

Navigation Engines are Krovos's specialized capabilities organized by life phase. They activate based on what the Life Graph reveals about where the person is and what they need. The user never selects an engine — the system surfaces it when relevant.

| **Phase 1** **Financial Foundation** Emergency fund engine · Debt optimizer · Paycheck allocation · Retirement trajectory · Coast FI calculator · Savings vaults · Milestone engine | **Phase 2** **Household** Family calendar · Division of labor · Children's planning · Parental leave navigator · College savings tracker · Benefits optimization | **Phase 3** **Optimizer** Open enrollment comparison · Credit card optimization · Tax planning · Career navigation · Net worth tracker · Investment rebalancing |
| --- | --- | --- |

| **Phase 4** **Young Adult** College decision engine · Career assessment · Early career starter kit · Student loan optimizer · First apartment guide | **Phase 5** **Modular Marketplace** Estate planning · Home buying · Business owner toolkit · Caregiver support · Retirement transition · Pre-retirement deep dive | **All Phases** **Trusted Guide Layer** The persistent AI that holds the Life Graph, demonstrates active understanding, orients to life phase and active decisions, and ends every exchange with a specific actionable next step. |
| --- | --- | --- |

**SECTION 5**

**V1 — The Smallest Complete Krovos**

V1 is not a minimal viable product in the traditional sense. It is the minimum set of systems required for a person to genuinely experience what Krovos is: understood, guided, and relieved. The V1 architecture must be designed to deliver that experience.

**V1 Components — Built and Working**

- Smart Start onboarding — 10 steps, document extraction across 26+ document types

- Life Graph — structured persistent understanding of the complete household situation

- Life Understanding Report — AI-generated, personalized, specific to this person's data

- Document extraction engine — AI reads and structures pay stubs, tax returns, mortgages, investments, insurance, debt, and more

- Partner and household income separation — correctly assigns income by person

- Investment account separation by owner and type — Roth IRA, Traditional IRA, 401k, 457b, HSA, brokerage, 529

- Debt pre-population from pay stub deductions and loan statements

- Retirement goal modeling with timeline calculations

- Full Krovos brand identity — dark premium design system, animated lantern loading

**V1 Success Criteria**

V1 succeeds when a user experiences three things in sequence:

- "That's exactly me" — recognition. The report reflects their specific situation, not a template.

- "Now I know what to focus on" — clarity. The priority guidance is specific enough to act on.

- "I feel less overwhelmed" — relief. The burden inventory surfaces and begins to resolve what they were carrying.

**V1 Roadmap**

- Marketing website (krovos.app) — separate from the app

- Navigation Scoreboard — the ongoing engagement surface

- Trusted Guide Layer — persistent AI with full Life Graph context

- Mobile-optimized layout and PWA

- Plaid integration for automatic spending analysis

- iOS and Android native apps

 

**KROVOS**

*The guide you were never given.*

krovos.app  ·  @krovosapp  ·  founder@krovos.app

	Founding Document — Confidential