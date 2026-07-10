# Life Gaps — Original Build Spec
**Document type:** Historical build record
**Status:** Feature fully built and live at /life-gaps as of July 2026
**Note on email architecture:** The Path A / Path B email sequences described in this spec represent the original design. The live product uses the unified email architecture documented in CLAUDE.md (Groups A/B/C with Soap Opera sequences). All other functionality in this spec matches the live implementation.

---

## 1. What You Are Building

Life Gaps is a lead magnet at /life-gaps. It asks 8 questions, detects the user's life phase and active modifiers, and surfaces a personalized set of gap paragraphs that name the specific things the person is carrying right now that they may not have fully articulated.

No authentication required. Anyone can access it. It is the first touchpoint most users will have with Krovos before joining the waitlist.

Life Gaps is not a simplified Life Profile. It collects life situation signals and returns recognition, not financial analysis.

Conversion path: 8 questions, personalized gaps, feel understood, join waitlist or request results by email. Both paths start a distinct email sequence.

---

## 2. Files and Routes

- Primary route: `app/life-gaps/page.tsx`
- Question component: `app/components/life-gaps/QuestionFlow.tsx`
- Results component: `app/components/life-gaps/ResultsPage.tsx`
- Gap content library: `lib/life-gaps/gap-content.ts`
- Gap detection logic: `lib/life-gaps/gap-logic.ts`
- Email submission API: `app/api/life-gaps/submit/route.ts`

### Supabase Migration

```sql
CREATE TABLE IF NOT EXISTS life_gaps_submissions (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  first_name TEXT,
  email TEXT,
  answers JSONB NOT NULL,
  detected_phases TEXT[] NOT NULL,
  active_modifiers TEXT[] NOT NULL,
  email_path TEXT CHECK (email_path IN ('waitlist','results_only','none')),
  waitlist_joined BOOLEAN DEFAULT FALSE,
  results_sent BOOLEAN DEFAULT FALSE,
  unsubscribed_at TIMESTAMPTZ,
  resend_contact_id TEXT
);
ALTER TABLE life_gaps_submissions ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Allow anon insert" ON life_gaps_submissions
  FOR INSERT TO anon WITH CHECK (true);
GRANT SELECT, INSERT, UPDATE, DELETE
  ON public.life_gaps_submissions TO service_role;
```

---

## 3. Question Flow

### Display Pattern

One question per screen. Full viewport. Midnight Slate background. Questions animate in from right, out to left. Progress dots bottom center, Mist Blue completed, Lantern Gold current. KROVOS wordmark top center. Back link top left Mist Blue. Continue button bottom right Lantern Gold. Mobile first, 390px primary target, minimum 48px tap targets.

### Q1 — Name
- Label: What should we call you?
- Type: Text input, first name only
- Required: Yes
- Pre-fill: `family.first_name`

### Q2 — Current Situation
- Label: What best describes your situation right now?
- Type: Multi-select tiles, select all that apply
- Options: W-2 employment | Self-employed or freelance | Running a business | Between jobs | Student | Stay-at-home | Retired
- Pre-fill: `financial.life_phase_data.primary_situation` + `also_true`

### Q3 — Relationship Status
- Label: What is your relationship status?
- Type: Multi-select tiles, select all that apply
- Options: Single | Engaged | Married or partnered | Divorced or separated | Widowed
- Pre-fill: `financial.life_phase_data.relationship_status`

### Q4 — Household
- Label: Which of these apply to your household?
- Type: Multi-select tiles, select all that apply
- Options: I have children or dependents | I am expecting or planning for a child | I am caring for an aging parent or family member | None of these
- Pre-fill: `family.has_children` + `family.expecting_baby` + `family.has_aging_parent`

### Q5 — Housing
- Label: What is your current housing situation?
- Type: Multi-select tiles, select all that apply
- Options: I rent | I have a mortgage | I own my home outright | I live with family | Other
- Pre-fill: `family.housing_type`

### Q6 — Partner Finances
- Label: How do you manage finances?
- Type: Multi-select tiles, select all that apply
- Options: I manage finances independently | Together, fully combined | Together, mostly separate | My partner is on a work or immigration visa
- Pre-fill: `family.has_partner` + `family.money_style` + `family.partner_visa`

Note: Q6 is context-aware based on Q3 relationship status (implemented in live product):
- Version A (partnered): partner finances question
- Version B (single): financial situation question
- Version C (divorced/widowed): rebuilding question

### Q7 — Coming Up
- Label: Is anything coming up in the next one to three years?
- Type: Multi-select tiles, select all that apply
- Options: Getting married | Buying a home | Selling a home | Having or adopting a child | Changing jobs | Launching a business | Going back to school | A significant medical expense | Moving to a new city | Moving abroad | An immigration milestone | Nothing major planned
- Pre-fill: `financial.life_phase_data.selected_events`

### Q8 — Top Concern
- Label: What concerns you most about your future right now?
- Type: SINGLE-select only. All other questions are multi-select. This one is not.
- Options: Not having enough saved | Paying off debt | Retirement | Affording a home | Supporting my family | Healthcare costs | Job security | Starting or growing a business | Something else
- Pre-fill: `financial.life_phase_data.financial_concerns[0]`

---

## 4. Email Gate

After Q8, before results, show an interstitial. Email is OPTIONAL. Results show regardless.

- Headline: We found several gaps worth sharing.
- Subtext: Enter your email to save a copy. Or skip to see your results now.
- Fields: First name (pre-filled from Q1) + Email address
- Primary button: Show my Life Gaps
- Skip link: Skip and show my results (smaller, below button)

---

## 5. Loading State

After the gate, before results. Lines fade in sequentially. Total duration approximately 2.5 seconds.

1. Reading your situation...
2. Identifying your life phase...
3. Finding your gaps...
4. Your Life Gaps are ready.

---

## 6. Results Page Structure

Six sections in this exact order:

**Section 1 — Complexity Signal (conditional)**
Show when 3 or more modifiers are active. Before the phase opening.

**Section 2 — Phase Opening**
2-3 sentences for the detected primary phase. From `PHASE_OPENINGS` in gap-content.ts. Use first name from Q1 if provided.

**Section 3 — Section Headline**
Single line: "Here is what we found." Fraunces SemiBold, Lantern Gold.

**Section 4 — Gap Cards**
First card always open. All others start collapsed showing only headline and hook. Tap to expand.
- Card headline: Bold, Lantern Gold, visible collapsed
- Card hook: One sentence, Mist Blue, visible collapsed
- Card body: Full paragraph, Soft Fog, revealed on expand
- Card background: #242C3D
- Max total gaps: 12. Max per modifier set: 3. Order by Section 8 priority rules.

**Section 5 — Transition Paragraph**
These are the gaps Krovos found from eight questions. Inside Krovos, this becomes specific to you, your complete picture, your numbers, your timeline, your life.

**Section 6 — Two CTAs**

PRIMARY (Path A, joins waitlist):
- Headline: Be first in when we launch.
- Subtext: The first 100 members lock in Founding Member pricing at $15 per month for life. After that: $19 per month.
- Fields: First name (pre-filled) + Email. Button: Join the waitlist, Lantern Gold bg, Midnight Slate text.

SECONDARY (Path B, results copy):
- Label: Want a copy of your Life Gaps to reference later?
- Field: Email only. Button: Send my results, outlined Soft Fog.
- Subtext: No spam, ever. Unsubscribe anytime.

FOOTER LINE:
Krovos was built to close these gaps, with full context about your complete situation, not just what you shared here.

---

## 7. Visual Design

| Element | Value |
|---|---|
| Background | #1B2230 Midnight Slate, all screens |
| Primary text | #E9EEF2 Soft Fog |
| Accent / CTA / Headlines | #F6C56A Lantern Gold |
| Hooks / Info | #7FA6BB Mist Blue |
| Card background | #242C3D |
| Heading font | Fraunces SemiBold |
| Body font | Inter Regular |
| Wordmark | KROVOS, Inter ExtraBold 800, all caps, 0.12em letter-spacing, Soft Fog |
| Selected tile | Lantern Gold border, subtle gold tint |
| Unselected tile | Mist Blue border, transparent |
| Mobile | 390px primary. 48px min tap targets. |

---

## 8. Gap Detection and Ordering Logic

Build in `lib/life-gaps/gap-logic.ts`. Input: answers object. Output: ordered array of modifier keys.

### Primary Phase Detection

| Situation | Phase |
|---|---|
| W-2 only, no partner, no children | EARLY_CAREER |
| W-2 with partner or children | ESTABLISHED |
| Self-employed or Running a business | SELF_EMPLOYED |
| Between jobs | IN_TRANSITION |
| Stay-at-home | STAY_AT_HOME |
| Student | STUDENT |
| Retired | APPROACHING_RETIREMENT |
| 3+ modifiers active | Override with NAVIGATING_COMPLEXITY opening |

### Modifier Keys

| Source | Modifier |
|---|---|
| Q3: Engaged | GETTING_MARRIED |
| Q3: Divorced | DIVORCED |
| Q3: Widowed | WIDOWED |
| Q3: Single | SINGLE |
| Q3: Married or partnered | MARRIED_ESTABLISHED |
| Q4: Has children | HAS_CHILDREN |
| Q4: Expecting | EXPECTING |
| Q4: Aging parent | CAREGIVER |
| Q5: Rents | RENTING |
| Q5: Has mortgage (not buying) | EXISTING_HOMEOWNER |
| Q5: Owns outright | OWNS_OUTRIGHT |
| Q6: Mostly separate | SEPARATE_FINANCES |
| Q6: Partner on visa | PARTNER_VISA |
| Q7: Getting married | GETTING_MARRIED (merge with Q3) |
| Q7: Buying a home | BUYING_HOME |
| Q7: Selling a home | SELLING_HOME |
| Q7: Having or adopting | EXPECTING (merge with Q4) |
| Q7: Changing jobs | CHANGING_JOBS |
| Q7: Launching a business | LAUNCHING_BUSINESS |
| Q7: Going back to school | BACK_TO_SCHOOL |
| Q7: Medical expense | MEDICAL_EXPENSE |
| Q7: Moving city | MOVING_CITY |
| Q7: Moving abroad | MOVING_ABROAD |
| Q7: Immigration | IMMIGRATION |
| Q8: Not enough saved | CONCERN_SAVINGS |
| Q8: Paying off debt | CONCERN_DEBT |
| Q8: Retirement | CONCERN_RETIREMENT |
| Q8: Affording a home | CONCERN_HOME |
| Q8: Supporting family | CONCERN_FAMILY |
| Q8: Healthcare costs | CONCERN_HEALTHCARE |
| Q8: Job security | CONCERN_JOB |
| Q8: Starting or growing a business | CONCERN_BUSINESS |

### Ordering Rules

1. Q7 event modifiers (upcoming, highest immediacy)
2. Q3 and Q4 household modifiers (current daily reality)
3. Q5 housing modifiers
4. Q6 partner and finance modifiers
5. Q8 concern modifier (always last)

Max 3 gaps per modifier set. Max 12 gaps total. Truncate lower-priority sets first. Complexity signal when 3+ modifiers active (excluding primary phase and concern).

---

## 9. Email Handling and Deduplication

Note: The email sequences below describe the original design. The live product uses the unified Group A/B/C email architecture documented in CLAUDE.md. The deduplication logic and database behavior remain accurate.

### Path A — Waitlist
Primary CTA. Add to waitlist table. Start existing 7-email nurture sequence in `lib/waitlist-emails.ts`. Tag in Resend Audience as 'life-gaps' and 'waitlist'. Store `resend_contact_id`.

### Path B — Results Only
Secondary CTA. Add to `life_gaps_submissions` with `email_path = 'results_only'`. Send 4-email Path B sequence via Resend. Tag as 'life-gaps'. Store `resend_contact_id`.

### Path B Sequence (original design)

**Email 1 — Immediate**
Subject: Your Life Gaps
Copy: Open with "Here is what we found." + first name if provided. List top 3 gap headlines with hook. Bridge: "These are the gaps Krovos found from eight questions. Inside Krovos, this becomes specific to you, your complete picture, your numbers, your timeline, your life." Soft CTA to krovos.app. Waitlist CTA with founding member pricing.

**Email 2 — Day 3**
Subject: The gap worth coming back to
Copy: Reference first gap in their ordered set. Go deeper. End: "This is one of the gaps Krovos holds for you, with full context about everything else connected to it. Join the waitlist: krovos.app"

**Email 3 — Day 6**
Subject: What changes when the gaps close
Copy: Peace of mind framing. Fewer open loops. First mention of founding member pricing in this sequence. Soft waitlist CTA.

**Email 4 — Day 10**
Subject: Founding Member spots are filling
Copy: $15/month locked. $19/month after. Direct CTA. Sequence ends after this if no action.

### Deduplication

1. Check waitlist table. If email found: already Path A. Return success. Do nothing.
2. Check `life_gaps_submissions`. If `email_path = 'waitlist'`: already Path A. Return success. If `email_path = 'results_only'` AND current submission is from primary CTA: Upgrade to Path A. Update `email_path`. Set `waitlist_joined = true`. Add to waitlist table.
3. If new to both: insert with correct `email_path`. Start sequence.

### Unsubscribe
Include unsubscribe link in every Path B email. On click: set `unsubscribed_at`, remove from Resend Audience, send no further emails.

---

## 10. Life Profile Pre-Fill

When a Life Gaps submission email matches a Krovos account at Life Profile onboarding, hydrate fields from the answers JSONB. Check `life_gaps_submissions` at start of each Life Profile step.

| Life Gaps field | Life Graph target |
|---|---|
| first_name | family.first_name |
| Q2 primary | financial.life_phase_data.primary_situation |
| Q3 relationship | financial.life_phase_data.relationship_status |
| Q4 has_children | family.has_children |
| Q4 expecting_baby | family.expecting_baby |
| Q4 has_aging_parent | family.has_aging_parent |
| Q5 housing_type | family.housing_type |
| Q6 has_partner | family.has_partner |
| Q6 money_style | family.money_style |
| Q7 selected_events | financial.life_phase_data.selected_events |
| Q8 concern | financial.life_phase_data.financial_concerns[0] |

Badge text on pre-filled fields: "From your Life Gaps" (not "Imported" or "Pre-filled from your document").

---

## 11. Complete Gap Content Library

The full gap content library (phase openings, complexity signal, and all modifier gap sets) is the authoritative source of truth in `lib/life-gaps/gap-content.ts`. Do not edit without content review.

All modifier keys with content in the live library:

- GETTING_MARRIED (3 gaps)
- EXPECTING (3 gaps)
- CAREGIVER (3 gaps)
- CHANGING_JOBS (3 gaps)
- BUYING_HOME (3 gaps)
- SELLING_HOME (3 gaps)
- LAUNCHING_BUSINESS (3 gaps)
- BACK_TO_SCHOOL (3 gaps)
- MEDICAL_EXPENSE (3 gaps)
- MOVING_CITY (3 gaps)
- MOVING_ABROAD (3 gaps)
- IMMIGRATION (3 gaps)
- SINGLE (3 gaps)
- MARRIED_ESTABLISHED (3 gaps)
- HAS_CHILDREN (3 gaps)
- RENTING (3 gaps)
- OWNS_OUTRIGHT (2 gaps)
- EXISTING_HOMEOWNER (2 gaps)
- SEPARATE_FINANCES (2 gaps)
- PARTNER_VISA (2 gaps)
- DIVORCED (3 gaps)
- WIDOWED (2 gaps)
- CONCERN_SAVINGS (2 gaps)
- CONCERN_DEBT (2 gaps)
- CONCERN_RETIREMENT (2 gaps)
- CONCERN_HOME (2 gaps)
- CONCERN_FAMILY (2 gaps)
- CONCERN_HEALTHCARE (2 gaps)
- CONCERN_JOB (2 gaps)
- CONCERN_BUSINESS (2 gaps)

Phase openings: EARLY_CAREER, ESTABLISHED, SELF_EMPLOYED, IN_TRANSITION, STAY_AT_HOME, STUDENT, NAVIGATING_COMPLEXITY, APPROACHING_RETIREMENT.

---

## 12. Build Sequence (historical reference)

1. Run SQL migration in Supabase. Confirm table exists.
2. Create `lib/life-gaps/gap-content.ts` with all content.
3. Create `lib/life-gaps/gap-logic.ts` with detection and ordering.
4. Create `app/api/life-gaps/submit/route.ts` with email handling.
5. Create `app/components/life-gaps/QuestionFlow.tsx`.
6. Create `app/components/life-gaps/ResultsPage.tsx`.
7. Create `app/life-gaps/page.tsx` to orchestrate components.
8. Wire pre-fill into Life Profile onboarding.
9. `npm run build`. Fix all TypeScript errors.
10. Live walkthrough. All 8 questions. Submit email. Verify results. Check Supabase row. Check Resend.

### Verification Checklist (all items verified at launch)

- One question per screen with animation
- All questions multi-select except Q8 single-select
- Email gate has functional skip
- Loading sequence before results
- Complexity signal at 3+ modifiers
- Phase opening matches detected phase
- First gap card open, rest collapsed
- Gap ordering follows priority rules
- Primary CTA writes to waitlist and starts Path A
- Secondary CTA writes to life_gaps_submissions and starts Path B
- Deduplication works correctly
- Pre-fill badge reads "From your Life Gaps"
- Mobile 390px renders correctly
- Zero em dashes in rendered content
- Midnight Slate background throughout
- Lantern Gold on CTAs and gap headlines
