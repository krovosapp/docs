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

**COMPLETED.** pg_dump and Supabase CLI were not available in the session environment
(Free-tier Supabase projects do not expose direct PostgreSQL connection strings for
pg_dump). The correct alternate method for Free-tier projects, confirmed and used here:

- **Schema:** Exported via the Supabase Schema Visualizer's "Copy as SQL" output,
  saved as `supabase-schema-2026-07-11.sql`.
- **Data:** Exported per-table as CSV using the Supabase Table Editor's built-in
  export function. One CSV file per critical table: profiles, life_graph,
  life_gaps_submissions, waitlist, net_worth_snapshots, email_schedule,
  payday_checkins, life_phase_guides, guide_content, user_guide_purchases, documents.

**For future backups on Free-tier Supabase:** Do not attempt to install pg_dump or
the Supabase CLI for this purpose. Use Schema Visualizer (schema SQL) and Table Editor
CSV export (data per table) directly in the Supabase dashboard. This is the correct
method for this plan tier and produces a complete, restorable export.

### Google Drive upload

**COMPLETED.** All files uploaded to:

```
Google Drive/
  Krovos Backups/
    2026-07-11/
      krovos-app-2026-07-11.zip
      docs-2026-07-11.zip
      krovos-os-2026-07-11.zip
      environment-variables-reference.md
      supabase-schema-2026-07-11.sql
      supabase-data-[tables]-2026-07-11.csv  (one per table)
```

### Status

- [x] krovos app repo zipped
- [x] docs repo zipped
- [x] krovos-os repo zipped
- [x] Environment variable names documented
- [x] Supabase schema exported (Schema Visualizer "Copy as SQL")
- [x] Supabase data exported (Table Editor CSV per table)
- [x] Uploaded to Google Drive

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
