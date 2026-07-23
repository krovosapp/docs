# Krovos Navigation Engine and Life Phase Guide Master Reference
*Updated to reflect the canonical 17-guide structure across 5 clusters. Supersedes all prior versions.*

---

## Part 1: The Final Life Phase Guide List (17 total)

### Starting Out (3)
1. Early Career Starter Kit
2. Newlywed / Wedding and Joining Finances
3. Immigration and Finances

### Building a Household (3)
4. Home Buying
5. New Parent / Pregnancy, Baby and Family Leave
6. College Planning

### Career (3)
7. Career Transition
8. Starting a Business
9. Gig Work / Freelance Income

### Life Changes (5)
10. Divorce and Transition
11. Caregiving
12. Disability Insurance and Personal Illness
13. Widowhood
14. Remarriage and Blended Families

### Later Life (3)
15. Retirement
16. Estate Planning
17. Inheritance

### Notes on scope
- Career Transition is the single home for both voluntary transitions and involuntary job loss. A distinct Part 5 covers the layoff case.
- New Parent / Pregnancy, Baby and Family Leave is the single home for all parental leave content, including Adoption as a subsection.
- Estate Planning includes Special Needs Planning as an explicit branch covering benefits preservation, ABLE accounts, first-party and third-party trusts, trustee selection, letters of intent, and a specialized professional handoff.
- Retirement is the intended home for Medicare Gap and healthcare bridge content (not yet built). FIRE content is already present as Part 5.
- Gig Work / Freelance Income is a standalone guide, not merged with Starting a Business.

### Explicitly not a guide, moved to core product
Fair Play household labor content and family calendar functionality belong in core product (Life Compass, tied to the "Family" Financial Mode), not gated behind any single guide purchase, since MARRIED_ESTABLISHED and HAS_CHILDREN are ongoing situations, not one-time trigger events.

---

## Part 2: Confirmed Content Gaps in Already-Written Guides

Verified directly against the actual files, not assumed:

1. **New Parent / Pregnancy, Baby and Family Leave guide: employer-provided short-term disability insurance is entirely absent.** The guide covers California's SDI/PFL system and FMLA in depth but gives no funding mechanism for the birth-recovery income gap in the other 42 states. This is the highest-priority fix of the three, it currently tells readers outside select states their gap exists without giving them a path to address it.
2. **Retirement guide: pension planning and WEP/GPO are entirely absent.** The live product's Life View report generation already models this; the written guide does not mention pensions at all.
3. **Home Buying guide: the prior rent-vs-buy decision is not addressed.** The guide assumes the reader has already decided to buy; no framework exists for someone still deciding.

---

## Part 3: Navigation Engine Feature Roadmap

See the companion document, navigation-engine-tool-guide-mapping.md, for the complete, corrected tool-to-guide mapping, including tool statuses and the confirmed-gap backlog of net-new tools to build. Core product tools are available to every user. Guide-specific tools are surfaced when a user engages with that guide.

### Already built and shipped (core product)
Life Compass, Life Calendar, Krovos Guide, Financial Modes (detection and display layer).

### Phase 1 - Financial Foundation
Paycheck Allocation (needs to become a real recommendation engine, not just a pay-date reminder), Debt Optimizer, Savings Vault System, Emergency Fund Calculator. Net Worth Tracker is speced-not-built.

### Phase 2 - Household Operations
Document Vault, Household Budget Framework, Fair Play / family calendar (core product, not guide-specific, see Part 1).

### Phase 3 - Optimizer
Credit Card Engine (selection half and usage half, two distinct features), Refinance Calculator, Insurance Adequacy Analyzer, Tax Planning Toolkit, Annual Enrollment Comparison, Purchase Strategy Simulator, Credit Health Dashboard.

### Phase 4 - Young Adult
Career Path Tool, Student Loan Strategy, First Apartment Planner.

### Phase 5 - Modular Marketplace
All 17 Life Phase Guides (Part 1), Trusted Pro Network (conceptually present in business-architecture.mdx as "Partner Referrals"; additions include notary referrals, an employer-benefit check, and pre-packaged documentation handoff as the real differentiator).

---

## Part 4: Standing Decisions (Previously Undocumented, Still Need a Home in the Docs)

**Investment execution:** Krovos stays guidance-only, does not become a registered investment advisor. Routes through Trusted Pro Network for execution.

**Legal document generation:** Krovos organizes and refers, does not draft legal documents directly. Routes through Trusted Pro Network for actual drafting.

Both belong in business-architecture.mdx's Partner Referrals section.
