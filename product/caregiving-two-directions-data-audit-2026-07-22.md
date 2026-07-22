# Caregiving: Two Directions Data Audit

**Date:** July 22, 2026  
**Status:** Investigation complete. Implementation requires founder approval and the prerequisites below.

## Proposed outcome

Add an equal-weight view inside Caregiving that shows two independent facts:

1. money currently directed toward the user's children's future; and
2. money currently directed toward a parent's care.

The view must not combine the numbers into a burden score, rank the two responsibilities, infer an emotional state, or recommend an automatic reallocation.

## Existing detection data

### Children

`life_graph.family.children` is an array of child records. Current records include `id`, `name`, `dob`, `relationship`, `expecting`, and `documents`. This is sufficient to detect at least one existing dependent after excluding records marked `expecting` where appropriate.

### Eldercare

The current model has more than one eldercare signal:

- `life_graph.family.has_aging_parent`, a direct boolean saved in the Life Profile;
- `life_graph.family.aging_parent_note`, a free-text note;
- `life_graph.family.aging_parent`, an older or report-derived object referenced elsewhere with `name`, `age`, `condition`, `current_monthly_cost`, `projected_memory_care_cost`, and `long_term_care_insurance`;
- Caregiving guide-plan activity, including a saved Caregiver Retirement Impact result; and
- local Caregiver Cost Tracker activity.

The direct detection rule should use `has_aging_parent === true` or a populated `aging_parent` object. Tool use can strengthen the signal, but should not be the only way a caregiver qualifies.

## Existing child-side numbers

- `life_graph.financial.investment_data.college_529` stores one household-level total across all 529 accounts.
- Child records do not currently store a per-child 529 balance, target, or monthly contribution.
- The 529 Projection Calculator accepts child age, current balance, and monthly contribution, but it does not save its result to `guide_plans` or another account-backed table.
- No durable history currently records monthly changes to child-directed contributions.

Therefore, the app can detect children and can display a total 529 balance, but it cannot honestly produce the proposed monthly "going to your kids' future" bar or a per-child trend from current durable data.

## Existing parent-care numbers

### Caregiver Cost Tracker

This is the strongest source conceptually. Each expense has a date, amount, category, payer, and sharing participants, and the tool already calculates totals by calendar month. However, the entire ledger is stored only under the browser-local key `krovos_caregiver_ledger`. It is not queryable from Supabase, does not follow the member across devices, and can be lost when browser storage is cleared.

### Care Options Comparison

This tool calculates monthly out-of-pocket scenarios for in-home care, assisted living, and skilled nursing care. It currently does not save its inputs or outputs to `guide_plans`.

### Caregiver Retirement Impact Calculator

This tool does save one current result in `guide_plans.tool_results`, including baseline retirement value, value with caregiving, gap, percentage gap, extra years, and monthly increase. It models the caregiver's retirement impact, not monthly dollars paid toward the parent's care. The shared save hook replaces the prior result for the same tool route, so it does not preserve a time series.

### Life Graph eldercare object

When present, `family.aging_parent.current_monthly_cost` and `projected_memory_care_cost` provide current or scenario amounts. The newer Life Profile UI saves `has_aging_parent` and a note, not these structured cost fields, so the object cannot be assumed to exist for every caregiver. It also has no history.

## Trend conclusion

The parent-care ledger contains dated history locally, but there is no durable, account-backed time series for either side. The proposed trend must not be presented as complete until tracking is added. A snapshot-only release would not satisfy the requested Life Labor precedent.

## Required prerequisites

1. Move the Caregiver Cost Tracker ledger from browser-only storage to an RLS-protected, account-backed table. Preserve category, date, amount, payer, and sharing metadata. Provide a one-time import of existing local entries after the member confirms it.
2. Save the Care Options Comparison result to `guide_plans` or a dedicated scenario table if it should qualify as an eldercare signal. Keep scenarios distinct from actual paid costs.
3. Add an account-backed child-future contribution source. At minimum, store the household's current monthly 529 contribution and effective date. If Krovos needs per-child bars later, store beneficiary linkage rather than dividing a household total by assumption.
4. Preserve dated snapshots or derive monthly totals from dated transactions on both sides.
5. Normalize eldercare detection around `has_aging_parent` while supporting the existing `aging_parent` object during migration.

## Recommended implementation order

1. Build durable Caregiver Cost Tracker persistence and local-data import.
2. Add a small, explicit child-future contribution record with an effective date. Do not require ownership of New Parent or College Planning.
3. Backfill detection compatibility for both eldercare field shapes.
4. Add the two equal-weight panels and factual sentence inside Caregiving.
5. Add trend lines only after at least two dated periods exist. Until then, label the view as a current snapshot and explicitly state that a trend will appear after another period is recorded.

## Decision required

Approve or revise the prerequisites and implementation order before any UI or detection logic is built.
