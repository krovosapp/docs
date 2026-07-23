# Core Retirement Audit

## July 23 guided-workspace addendum

The Retirement Hub now presents the Core model as a connected planning workspace rather than a vertical collection of scenarios, checklists, records, and calculators. Its first screen connects Net Worth, retirement assets, active Life Goals, and the Life Profile income/spending facts feeding the projection. Members move deliberately among four views: model the plan, work through next decisions, preserve saved details, or open a specific tool.

The central model compares three editable scenarios in one field of view. Savings change and return assumptions are independent in each column, while current age, retirement assets, savings capacity, goal funding, retirement spending, withdrawal-rate assumption, pension, and Social Security remain common plan facts. All three modeled retirement ages and portfolio outcomes update together. Active goal funding is visible as its own lever, so near-term goals are neither silently ignored nor automatically treated as retirement contributions.

The specialized tool library is no longer the default long list. Krovos recommends three decision-relevant tools, explains when each is useful, and keeps the complete retirement library behind an explicit browse action. Readiness actions and decision records remain durable but no longer compete with the scenario model on the same screen.

This addendum preserves the existing A+ design grade because it corrects a serious usability weakness in the prior implementation; it does not upgrade the product to production-ready. Authenticated restoration, household behavior, mobile widths, keyboard/screen-reader operation, calculation boundaries, and beta comprehension remain release gates.

## July 23 household-model correction

The first guided-workspace implementation had unintentionally removed the material staggered-retirement calculation and therefore did not justify the 99 score stated below. The correction restores the original differentiator:

- both current ages and both target retirement ages are converted to calendar timing;
- each scenario projects the shared portfolio to the first retirement;
- the existing shared bridge-period engine then applies continuing household income, retirement spending, the portfolio funding gap, growth, and the second retirement;
- each scenario shows bridge coverage, portfolio used during the transition, and portfolio remaining at withdrawal start;
- if a withdrawal-start age is not supplied, the second retirement is used instead of silently collapsing the bridge to zero;
- saved plan inputs restore, recorded FIRE and Life Graph facts prefill, and the member is told which saved facts were used.

Retirement now earns **97/A+ product design**. The two-point reduction from the earlier stated 99 reflects missing controlled evidence, not a missing core concept: keyboard and screen-reader operation, 200 percent zoom, reduced motion, extreme calculation boundaries, authenticated two-account household permissions, cross-device restoration, and novice beta comprehension are still release gates. The grade must return to 99 only after those gates pass with evidence.

**Date:** July 22, 2026  
**Current product-design score:** 97/A+
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
2. Authenticated test-user evidence is still required for persistence, cross-device restoration, spouse handling, mobile layout, keyboard/screen-reader behavior, and correction after changed facts.
3. The Investment Priority calculation needs scenario walkthroughs covering ages 50 and 60–63, multiple employers, HSA ineligibility, pre-tax IRA pro-rata exposure, self-employment, and partner-specific opportunities.

## Next upgrade sequence

1. Review the remaining Core hubs against the same evidence, persistence, burden-relief, and correction standard.
2. Run the controlled founder test-account matrix after all Core upgrades are complete.
3. Confirm the 99/A+ product-design score with verified behavior before treating it as a production-readiness score.
