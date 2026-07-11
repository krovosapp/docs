# Krovos Backup SOP

Standing procedure for regularly exporting all Krovos work to Google Drive so nothing is solely dependent on GitHub, Supabase, or Vercel access continuing.

**Version:** 1.0 | **Created:** 2026-07-10 | **Review trigger:** Any change to the repository structure (new repos, archived repos, renamed repos) or a change in which services Krovos depends on.

---

## What Needs Backing Up

### 1. GitHub Repositories (3 active repos)

| Repo | What it contains |
|---|---|
| krovosapp/krovos | Main product: Next.js app, all page/component/API code, Supabase migrations, TaxEngine, proxy, PWA config |
| krovosapp/docs | Mintlify documentation site: all 17 Life Phase Guides, navigation engine reference, product decisions, open items backlog |
| krovosapp/krovos-os | Internal business operations: AI agent role templates, approved email copy, narrative engine, marketing methodology reference |

A git clone or zip export of each repo captures everything committed to the default branch. It does not capture unpushed local changes -- push before backing up.

### 2. Supabase Database

Two things must be exported separately:

**Schema:** The table structure, RLS policies, functions, and triggers. This is the blueprint for rebuilding the database.

**Data:** The actual rows: user profiles, Life Graph records, life_gaps_submissions, waitlist entries, net_worth_snapshots, email_schedule, payday_checkins, life_phase_guides, guide_content, user_guide_purchases. The schema alone is insufficient to recover from a data loss event.

Export both together in one session so they match.

### 3. Environment Configuration

The app cannot run without the following environment variables (these are set in Vercel and must be documented separately from the code). **Never write actual secret values into any backup file.** Document the variable names only, so a rebuild knows what credentials to regenerate:

```
NEXT_PUBLIC_SUPABASE_URL
NEXT_PUBLIC_SUPABASE_ANON_KEY
SUPABASE_SERVICE_ROLE_KEY
ANTHROPIC_API_KEY
STRIPE_SECRET_KEY
STRIPE_WEBHOOK_SECRET
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY
RESEND_API_KEY
CRON_SECRET
KIT_API_KEY (if in use)
```

> **Security callout:** Do not include actual secret values in any backup file that will be stored in Google Drive, emailed, or shared in any way. Google Drive is not a secrets vault. If you ever need to store actual credentials, use a dedicated password manager (1Password, Bitwarden, etc.) or a secrets management service. The backup SOP covers documenting that these variables exist, not storing their values.

Keep a separate, password-manager-only note with the current values for each variable, updated any time a key is rotated.

---

## Recommended Cadence

**Trigger-based (recommended for now):** Run a backup after any session that includes significant changes -- a major feature build, a database migration, a guide content update, or anything you would be upset to lose. For most active build sessions, that means once per session.

**Minimum floor:** At least once per week during active development, even if no single session felt significant. Small changes accumulate.

**Automation note:** This SOP is a human-triggered process. There is no automated export script currently in place. If you want to build a scheduled export (cron job, GitHub Action, or Supabase scheduled function that writes exports to Google Drive), that is a separate build task -- flag it in krovos-open-items-and-backlog.md when ready to prioritize it.

---

## Step-by-Step Backup Checklist

Run through this in order. Total time for a full backup: approximately 15-20 minutes.

### Step 1: Push any pending local changes to GitHub

Before exporting anything, make sure all local work is committed and pushed. Open Terminal and run:

```bash
cd ~/krovos && git status
cd ~/krovos-os && git status
cd ~/docs && git status
```

If any repo shows uncommitted changes, commit and push them before continuing. A backup of the GitHub zip does not capture local-only work.

### Step 2: Export each GitHub repo as a zip

For each of the three repos, do the following (repeat three times):

1. Go to the repo on GitHub (github.com/krovosapp/krovos, then /krovos-os, then /docs)
2. Click the green **Code** button
3. Click **Download ZIP**
4. Save the file -- GitHub names it something like `krovos-main.zip`. Rename it to include today's date: `krovos-main-2026-07-10.zip`

Repeat for krovos-os and docs.

### Step 3: Export the Supabase database

**Note on pg_dump and Supabase CLI:** Free-tier Supabase projects do not expose a
direct PostgreSQL connection string for pg_dump, and the Supabase CLI requires Pro-tier
database access for `supabase db dump`. Do not attempt to install either tool for this
purpose on the Free tier -- they will not work. Use the dashboard methods below instead.
This was confirmed on 2026-07-11 (first backup execution).

**Schema export (Free-tier method -- confirmed working):**
1. Go to supabase.com and open the Krovos project
2. Go to **Database > Schema Visualizer** in the left sidebar
3. Click **Copy as SQL** (top right of the visualizer)
4. Paste the output into a text file and save as `supabase-schema-YYYY-MM-DD.sql`

**Data export (Free-tier method -- confirmed working):**
1. Go to **Table Editor**
2. For each critical table (profiles, life_graph, life_gaps_submissions, waitlist,
   net_worth_snapshots, email_schedule, payday_checkins, life_phase_guides, guide_content,
   user_guide_purchases, documents):
   - Open the table
   - Click the **Export** button (CSV)
   - Save with a descriptive name: `life_graph-YYYY-MM-DD.csv`

Critical tables listed in priority order -- if time is short, export profiles and
life_graph first as these contain the most irreplaceable user data.

Note: If the project is ever upgraded to Supabase Pro, automated daily backups with
point-in-time recovery become available. Verify they are enabled after any plan upgrade.

### Step 4: Organize in Google Drive

Create a dated folder in Google Drive with this structure:

```
Google Drive/
  Krovos Backups/
    2026-07-10/
      repos/
        krovos-main-2026-07-10.zip
        krovos-os-main-2026-07-10.zip
        docs-main-2026-07-10.zip
      database/
        krovos-db-schema-2026-07-10.sql
        life_graph-2026-07-10.csv
        profiles-2026-07-10.csv
        [other table exports]
      notes.txt   (optional: note what changed in this session worth highlighting)
```

If a "Krovos Backups" folder does not exist yet, create it now at the top level of your Google Drive. Keep one dated folder per backup session. Do not delete old backups -- storage is cheap and recovery scenarios are unpredictable.

### Step 5: Verify the backup

Spot-check before closing out:

- Open one of the zip files and confirm it is not empty and contains the expected folder structure
- Open one of the CSV exports and confirm it has actual data rows, not just headers
- Note the date of the backup in a running log (a simple Google Doc called "Backup Log" works fine: date, what changed, any notes)

---

## Branch Maintenance Note

As of 2026-07-10, two feature branches exist in krovosapp/krovos (`feature/guide-delivery` and `feature/net-worth-tracker`) that are fully merged into main and serve no remaining purpose. GitHub zip exports default to the main branch only. If you want to preserve these branch histories explicitly, download them separately by switching the branch selector before downloading the zip, or rely on the full git history being preserved in the main branch (all commits from both branches are in `git log` on main).

---

## When to Update This SOP

Review and update this document whenever:

- A new repository is added to the krovosapp organization
- A repository is archived or deleted
- A new critical table is added to the Supabase database that should be included in exports
- A new environment variable is added that would be required for a from-scratch rebuild
- Automation is built to replace any manual step in this checklist

This document lives at `docs/product/krovos-backup-sop.md` in the krovosapp/docs repo.
