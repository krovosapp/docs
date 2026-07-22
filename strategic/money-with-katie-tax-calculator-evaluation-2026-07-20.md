---
title: "Tax-Smart Investment Calculator Evaluation"
description: "Krovos evaluation of Money with Katie's July 2026 lead magnet, delivery page, and workbook."
---

# Tax-Smart Investment Calculator Evaluation

**Reviewed:** July 20, 2026  
**Inputs:** Public opt-in page, public success page, copy-link workbook, workbook formulas and visible sheets, current Krovos tool catalog and implementation, and current IRS 2026 sources  
**Decision:** Adopt the problem and acquisition lesson; do not copy the workbook or funnel. Build **Investment Priority** as a deterministic Core Retirement feature after the corrected Guide work and tax-constant audit. The name and Core placement are approved. A bounded public lead-magnet version remains a near-launch experiment, not a current launch commitment.

## Executive Judgment

Money with Katie's asset is not primarily an income-tax calculator. It is a retirement and investment-account prioritization workbook for higher earners. It uses filing status, gross income, workplace-plan access, self-employment, after-tax 401(k) access, and HDHP coverage to suggest a mix among workplace plans, traditional or Roth IRAs, backdoor Roth, SEP IRA, Solo 401(k), mega backdoor Roth, and HSA. The member then enters planned traditional and Roth contributions to see an estimated federal income-tax reduction and future-value illustration.

Krovos does **not** currently have one unified tool that answers that exact cross-account question. Krovos has stronger pieces in separate places:

- **Quarterly Tax** is a more complete household tax-planning tool than the workbook's simplified ordinary-income calculation.
- **Retirement Plan Comparison**, after the July 20 correction pass, is safer for choosing among Solo 401(k), SEP IRA, and SIMPLE IRA because it asks for business structure, compensation, other-plan deferrals, age, and employees.
- Life Graph already stores account, benefits, income, household, debt, and priority context that a spreadsheet cannot reuse automatically.

However, Krovos does not yet coordinate the next investable dollar across employer match, HSA, IRA, self-employed plans, and taxable investing. The competitive gap is real.

## Acquisition and Delivery Evaluation

### Opt-in page: strong

The public page earns an **A- as a lead-magnet landing page**. It is specific about the audience, names recognizable accounts, describes one concrete problem, and promises a tangible output. The form asks only for first name and email. The CTA is obvious.

The lesson for Krovos is specificity, not imitation. “Tax calculator” is too broad. The actual promise—deciding which investment accounts to prioritize—is what makes the asset compelling.

### Success page: weak and misaligned with Krovos

The success page earns a **D for burden relief**. Its first major message is an upsell to a Domain Money strategy session, followed by a long affiliate-style financial-planning sales page, cost-of-advice comparisons, repeated calls to book, and extensive promotional content. The promised calculator is not the clear center of the experience.

Krovos should not use a thank-you page to make the member search through a second company's pitch for the thing just requested. Delivery should be immediate. Any next step should follow the promised asset, be clearly optional, and explain why it is relevant.

## Workbook Evaluation

### What it does well

- Uses the correct 2026 federal ordinary-income brackets and standard deductions for single and married-filing-jointly users.
- Uses the 2026 $24,500 workplace deferral, $7,500 base IRA, and $72,000 defined-contribution limits in the main scenario tables.
- Makes the account-choice problem understandable to a nonprofessional.
- Avoids recommending SEP IRA and backdoor Roth together in its scenario matrix.
- Lets the user override taxable income when the standard deduction is not appropriate.
- Shows traditional and Roth contributions separately and illustrates the value of investing tax savings.
- Includes an explicit disclaimer and routes material decisions to a CPA.

### What prevents it from being decision-grade

The workbook earns a **B- as free financial education and a C as personalized planning**.

1. **The tax calculation is intentionally narrow.** It models ordinary federal income tax from gross income minus the standard deduction. It does not model head of household, married filing separately, dependents, credits, self-employment tax, capital gains, QBI, additional deductions, state tax, or the household's actual return.
2. **Eligibility is reduced to yes/no questions.** It does not ask age, contribution already made, employer match, vesting, plan fees, each spouse's compensation, self-employment profit, employer contributions, existing pre-tax IRA balances, or other coverage that can disqualify HSA contributions.
3. **Maximums are displayed without the compensation facts that constrain them.** The workbook can display a $72,000 SEP maximum after learning only that some self-employment income exists. It cannot determine the allowable owner contribution without actual profit or compensation.
4. **Employee-deferral coordination is missing.** A person contributing to a workplace 401(k) and Solo 401(k) shares one employee elective-deferral limit. The workbook shows account maximums but does not ask how much of the limit has already been used.
5. **Age-based catch-ups are missing.** It does not ask age and therefore omits the 2026 $1,100 IRA catch-up, $8,000 workplace-plan catch-up, and the $11,250 ages-60-to-63 catch-up.
6. **Mega backdoor capacity is oversimplified.** The displayed amount is essentially the overall limit minus the regular employee deferral. Actual space also depends on employer contributions, plan terms, compensation, testing, and prior contributions.
7. **Backdoor Roth risk is incompletely screened.** Avoiding a new SEP recommendation is useful, but the user may already hold pre-tax traditional, SEP, or SIMPLE IRA balances relevant to Form 8606 and the pro-rata calculation.
8. **HSA eligibility is underspecified.** HDHP coverage alone does not establish eligibility; other disqualifying coverage, Medicare, dependent status, employer contributions, coverage months, and plan facts matter.
9. **“Optimal” overstates the evidence.** The matrix enumerates 32 single and 108 married scenario columns—140 combinations—while the introduction says 142 possible scenarios. More importantly, enumeration does not make a strategy optimal when the user's cash flow, debt, match, fees, goals, risk, and existing balances are absent.
10. **The future-value outputs are illustrations, not a plan.** They use user-entered return and time assumptions and assume tax savings are invested. They do not model volatility, taxes on eventual traditional withdrawals, fees, inflation unless the user adjusts the rate, or behavior over time.

## Krovos Product Decision

### Scope **Investment Priority** in Core Retirement

Retirement is part of Core, so this should not become a $49 Guide or create another AI-report charge. It should be a deterministic, saved decision tool using current Life Graph data.

The member version should:

1. confirm minimum cash stability and high-cost debt before recommending contributions beyond an employer match;
2. identify each available account, owner, provider, match, vesting, fees, Roth/traditional availability, and contributions already made;
3. confirm HSA eligibility and employer contributions rather than infer them from an HDHP label;
4. coordinate the shared employee-deferral limit across employers and self-employed plans;
5. calculate owner-plan limits from actual compensation and business structure;
6. ask about existing pre-tax IRA balances before showing a backdoor Roth path;
7. calculate mega-backdoor space only from actual plan terms and all employer/employee contributions;
8. include age and ages-60-to-63 catch-up rules;
9. compare traditional and Roth without always treating the current deduction as superior;
10. show spouse-specific opportunities and household cash-flow consequences;
11. produce a prioritized next-dollar list with amount, reason, blocker, deadline, and evidence needed; and
12. save the chosen priority and progress to the member's Core Retirement plan without another API call.

Krovos should use labels such as **available**, **needs confirmation**, and **not currently supported**. It should not call an account sequence optimal when an eligibility or plan fact is missing.

### Evaluate a limited public version as a lead magnet near launch

If beta evidence supports a public acquisition version, it can ask only the minimum non-sensitive facts needed to show a preliminary priority path. It should give useful recognition on the page, then offer **Email my priority list** as the email-capture action. The email is exchanged for saving and delivery, not for revealing whether the person has a problem.

The public result should clearly distinguish:

- what is known from the answers;
- what must be confirmed with an employer, provider, or tax professional;
- which result changes after Krovos understands the full household; and
- why the member version is safer and more personalized.

Do not copy Money with Katie's formulas, scenario tables, copy, design, or Domain Money funnel. Build from official rules and Krovos's own governed decision model.

The complete lead-magnet portfolio, release gate, audience system, and profit architecture are governed by [Audience Acquisition and Profit System](./audience-acquisition-and-profit-system-2026-07-20.md).

## Required Work Before Build

1. Complete a repo-wide annual-limit and tax-constant audit. The July 20 review already found and corrected stale 2025 values labeled as 2026 in the report prompt, HSA tools, Early Career copy, inheritance tool, milestone copy, and retirement-plan context.
2. Finish and test the Starting a Business retirement-plan correction pass.
3. Define the account-priority decision policy with Founder Book safeguards and professional review.
4. Add official-source versioning and an annual update owner.
5. Design public and member outputs separately.
6. Test at minimum: single W-2, married dual W-2, W-2 plus 1099, owner-only S corp, existing pre-tax IRA, HSA-ineligible HDHP lookalike, ages 50+, ages 60-63, low free cash flow, and high-interest debt.

## Source Layer

- IRS, [2026 inflation adjustments and standard deductions](https://www.irs.gov/newsroom/irs-releases-tax-inflation-adjustments-for-tax-year-2026-including-amendments-from-the-one-big-beautiful-bill)
- IRS, [2026 retirement-plan and IRA limits](https://www.irs.gov/retirement-plans/cola-increases-for-dollar-limitations-on-benefits-and-contributions)
- IRS, [401(k) and profit-sharing contribution limits](https://www.irs.gov/retirement-plans/plan-participant-employee/retirement-topics-401k-and-profit-sharing-plan-contribution-limits)
- IRS, [One-Participant 401(k) Plans](https://www.irs.gov/retirement-plans/one-participant-401k-plans)
- IRS, [Publication 560](https://www.irs.gov/publications/p560)
- IRS, [2026 HSA limits](https://www.irs.gov/irb/2025-21_IRB)
- IRS, [2026 FSA and other inflation-adjusted limits](https://www.irs.gov/irb/2025-45_IRB)
