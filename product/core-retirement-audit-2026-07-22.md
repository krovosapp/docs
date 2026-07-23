# Core Retirement Audit

## July 23 guided-workspace addendum

The Retirement Hub now presents the Core model as a connected planning workspace rather than a vertical collection of scenarios, checklists, records, and calculators. Its first screen connects Net Worth, retirement assets, active Life Goals, and the Life Profile income/spending facts feeding the projection. Members move deliberately among four views: model the plan, work through next decisions, preserve saved details, or open a specific tool.

The central model compares three editable scenarios in one field of view. Savings change and return assumptions are independent in each column, while current age, retirement assets, savings capacity, goal funding, retirement spending, withdrawal-rate assumption, pension, and Social Security remain common plan facts. All three modeled retirement ages and portfolio outcomes update together. Active goal funding is visible as its own lever, so near-term goals are neither silently ignored nor automatically treated as retirement contributions.

The specialized tool library is no longer the default long list. Krovos recommends three decision-relevant tools, explains when each is useful, and keeps the complete retirement library behind an explicit browse action. Readiness actions and decision records remain durable but no longer compete with the scenario model on the same screen.

This addendum preserves the existing A+ design grade because it corrects a serious usability weakness in the prior implementation. Authenticated restoration, household behavior, mobile widths, keyboard/screen-reader semantics, and calculation boundaries were subsequently closed in the July 23 controlled completion pass below. Human beta comprehension remains an observation gate.

## July 23 household-model correction

The first guided-workspace implementation had unintentionally removed the material staggered-retirement calculation and therefore did not justify the 99 score stated below. The correction restores the original differentiator:

- both current ages and both target retirement ages are converted to calendar timing;
- each scenario projects the shared portfolio to the first retirement;
- the existing shared bridge-period engine then applies continuing household income, retirement spending, the portfolio funding gap, growth, and the second retirement;
- each scenario shows bridge coverage, portfolio used during the transition, and portfolio remaining at withdrawal start;
- if a withdrawal-start age is not supplied, the second retirement is used instead of silently collapsing the bridge to zero;
- saved plan inputs restore, recorded FIRE and Life Graph facts prefill, and the member is told which saved facts were used.

Retirement now earns **98/A+ product design**. Controlled evidence now covers responsive layout, zoom-equivalent reflow, reduced motion, screen-reader semantics, extreme calculation boundaries, permission-gated connected-partner data, and durable saved-plan restoration. The final point remains reserved for observed novice beta comprehension; it cannot be earned through implementation assertions alone.

**Date:** July 22, 2026  
**Current product-design score:** 98/A+
**Scope:** Core subscription, not a paid Guide

## What now earns the score

- A saved 13-part operating plan coordinates investment priority, reliable income, healthcare and Medicare, withdrawals and taxes, pensions, Social Security, RMDs, sequence risk, long-term care, survivor resilience, housing, first-year life design, and annual review.
- Investment Priority fills the confirmed next-dollar gap without another AI call. It uses Life Graph facts and current 2026 limits, preserves missing-evidence boundaries, and saves the ordered list into the Core Retirement plan.
- The central single- and dual-date FI scenario now saves its inputs, FI number, progress, and scenario results into the same plan.
- Safe Withdrawal Rate, CoastFI, Roth Conversion, Retirement Withdrawal, Social Security Comparison, and RMD Deadline Tracker now save their completed inputs, modeled outcomes, decision context, and timestamp into `retirement_plans.tool_results` without another AI call.
- Separate decision records now preserve the healthcare coverage bridge, Medicare enrollment and HSA/IRMAA coordination, and pension election terms. Known Life Graph facts pre-fill the records without overwriting their source data.
- Current guidance avoids universal Roth, claiming-age, withdrawal-order, RMD-age, and WEP/GPO shortcuts.

## Why it is not yet higher

1. Long-term care, survivor resilience, housing, and first-year life design remain coordinated saved actions rather than dedicated structured records. Test-user behavior should determine whether each needs a separate record.
2. Novice beta evidence is still required to confirm that members understand the staged inputs, three simultaneous scenarios, and staggered household timeline without founder explanation.
3. The Investment Priority calculation needs scenario walkthroughs covering ages 50 and 60–63, multiple employers, HSA ineligibility, pre-tax IRA pro-rata exposure, self-employment, and partner-specific opportunities.

## Next upgrade sequence

1. Review the remaining Core hubs against the same evidence, persistence, burden-relief, and correction standard.
2. Run the controlled founder test-account matrix after all Core upgrades are complete.
3. Confirm the 99/A+ product-design score through observed novice comprehension before treating it as a production-readiness score.

## July 23 controlled completion evidence

The authenticated Retirement workspace was checked at phone, tablet, and desktop widths with no horizontal overflow and no unnamed controls in the tested state. Application-wide reduced-motion handling now covers animated and transitioned elements, and the phone-width result also supplies the reflow required at a 200 percent zoom equivalent. Retirement step controls expose their selected state, changing result groups announce politely, and save feedback is a screen-reader status message.

## Household input correction, July 23, 2026

The partner model is no longer a household-total shortcut. When the member selects the household option, the control uses the recorded partner name and the first step presents two clearly separated people. Each person has their own current age, retirement accounts, and annual retirement savings. Reliable-income inputs likewise remain separate for each person's pension and Social Security amount and start age. The final timing step contains only the two target retirement ages, household bridge income, and portfolio-withdrawal timing.

The projection combines the two asset and contribution streams for household modeling while preserving the person-level inputs in the saved plan. Combined pension or Social Security income is not treated as fully available until both entered streams have started, avoiding an early-income overstatement. The workspace label changes to **Model our plan**, and the view order now explicitly tells the member to start with the model, move to next decisions after the scenarios make sense, open decision tools only as needed, and use the saved plan as reference.

Authenticated local verification confirmed the recorded names Christine and Ethan, separate current ages and balances, the household workspace label, and all three scenario results. TypeScript, changed-file lint, the Core UI regression audit, and the full production build pass. Novice comprehension remains the final human release gate.

## Money-style and guided-workspace correction, July 23, 2026

Retirement now follows the household's recorded Life Profile money style. Fully combined and mostly combined households keep person-level ages, pensions, Social Security, and retirement dates distinct, but use one prefilled household retirement-asset total and one household annual retirement-savings amount. This total includes individually titled retirement accounts and shared family-planned investments. Members are not asked to assign a brokerage account or other family resource to one partner merely to satisfy the interface. Mostly separate and fully separate households retain person-level balances and contributions.

The workspace no longer gives equal access to unfinished downstream work. Model our plan is the only available starting destination. Next decisions, Decision tools, and Saved plan are disabled and labeled as locked until the member saves the model. A previously saved model restores them as available. Authenticated local verification confirmed Christine and Ethan as distinct people, a prefilled household retirement total of $409,181.66 from the recorded combined-money profile, and disabled downstream destinations before save.

Connected-partner inputs are fetched only through `/api/household/partner-data`, which preserves the existing active-connection and financial-sharing permission checks. Authorized partner retirement assets, income, date of birth, name, and saved target age can now prefill the shared model. No direct partner Life Graph read was added.

The production calculation functions were extracted into `lib/retirement-scenarios.ts` and are exercised by `npm run audit:retirement`. Passing cases cover zero and negative inputs, an already funded plan, the safe withdrawal-rate fallback, a ten-year projection, a partially funded staggered bridge, and a fully income-covered bridge. `npm run audit:core-ui` protects the accessible and household integration semantics. TypeScript and the full 153-route production build pass.

App commit `b40aa9a` was deployed to production as `dpl_66j9GUKwAA311VMfFXyRawDzFHAL`. Authenticated verification on `www.krovos.app/retirement` confirmed the deployment is live and exposes the Retirement step selected-state and polite live-region semantics.
