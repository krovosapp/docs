# Krovos Backup Log

Record of every manual backup execution per krovos-backup-sop.md.
Update this file immediately after each backup run.

---

## 2026-07-11 -- First execution

**Performed by:** Claude Code (session initiated by Christine Smith)
**Trigger:** Five-area audit found zero prior backup executions. Immediate backfill.
**Staging location:** ~/krovos-backup-2026-07-11/

### Repo zips exported

| File | Size | Contents |
|---|---|---|
| krovos-app-2026-07-11.zip | 1.2 MB | krovos app repo, excluding .git, node_modules, .next |
| docs-2026-07-11.zip | 216 KB | docs repo, excluding .git |
| krovos-os-2026-07-11.zip | 220 KB | krovos-os repo, excluding .git, node_modules, .next |

All repos were confirmed clean (no uncommitted changes, fully pushed to origin) before zipping.

### Environment variables reference

| File | Size | Contents |
|---|---|---|
| environment-variables-reference.md | 1.3 KB | Variable names only, no values. Notes CRON_SECRET rotation needed. |

### Supabase export

**NOT COMPLETED in this session.** Supabase CLI and pg_dump were not available in the
session environment. The Supabase export (schema + data) must be done manually.

**Manual steps required (Christine):**
1. Log into supabase.com > select the Krovos project
2. Go to Settings > Database > Download backup (or use the Supabase CLI: `supabase db dump`)
3. Export schema: Project Settings > Database > Schema (download the SQL file)
4. Export data: use the table editor's export function for each critical table, or use
   `supabase db dump --data-only` via CLI if installed locally
5. Critical tables to export: profiles, life_graph, life_gaps_submissions, waitlist,
   net_worth_snapshots, email_schedule, payday_checkins, life_phase_guides, guide_content,
   user_guide_purchases, documents
6. Save both schema.sql and data.sql to the ~/krovos-backup-2026-07-11/ folder
7. Then drag the entire folder to Google Drive per the SOP folder structure

### Google Drive upload

**NOT COMPLETED -- Christine's manual action.** Per the SOP, everything in
~/krovos-backup-2026-07-11/ should be uploaded to:

```
Google Drive/
  Krovos Backups/
    2026-07-11/
      krovos-app-2026-07-11.zip
      docs-2026-07-11.zip
      krovos-os-2026-07-11.zip
      environment-variables-reference.md
      supabase-schema-2026-07-11.sql     (after manual export)
      supabase-data-2026-07-11.sql       (after manual export)
```

### Status

- [x] krovos app repo zipped
- [x] docs repo zipped
- [x] krovos-os repo zipped
- [x] Environment variable names documented
- [ ] Supabase schema exported (manual step needed)
- [ ] Supabase data exported (manual step needed)
- [ ] Uploaded to Google Drive (manual step needed)

### Notes

krovos-os had one unpushed local commit (4e760df -- README update) and one untracked
stale draft file (marketing-methodology-master.md). Both were resolved before the backup:
commit pushed to origin, draft deleted after confirming it was a duplicate subset of
content/marketing-methodology.md with zero unique content. All three repos were clean
before zipping.

---

## How to add an entry

Copy the template below at the top of the log (after the header), fill in date and details.

```
## YYYY-MM-DD

**Performed by:**
**Trigger:**
**Staging location:**

### Exported
- [ ] krovos-app-YYYY-MM-DD.zip (size)
- [ ] docs-YYYY-MM-DD.zip (size)
- [ ] krovos-os-YYYY-MM-DD.zip (size)
- [ ] Supabase schema
- [ ] Supabase data
- [ ] Uploaded to Google Drive
```
