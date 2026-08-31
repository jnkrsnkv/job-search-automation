# Notion schema

Two databases. Create them by hand in Notion, give the integration write access to
both.

---

## Database 1 — "Candidates" (staging)

The run drops new items here. You triage them via the dashboard.

| Property | Type | Values / note |
|---|---|---|
| Name | Title | Job title |
| Company | Text | |
| Link | URL | To the job itself (not an aggregator, if possible) |
| Source | Select | LinkedIn (job alert), <TG channel>, <career page>, Working Nomads, … |
| Track | Select (or Multi-select) | Values = your tracks from `filter-criteria.md` |
| Priority | Select | `High`, `Medium`, `Maybe` |
| Review status | Select | `To review`, `Added to tracker`, `Rejected` |
| Rationale | Text | First line: `<Priority> — <reason>`; then the track rationale |
| Deadline | Date | If known. Mandatory for tenders |
| Entry type | Select | `Job`, `Tender` — only if you also track tenders |
| Added | Created time | Automatic |

**The dashboard filters by `Review status = To review`.** Blank-status records are
invisible — hence the check step after a bulk write (see `tool-access-playbook.md`
→ Notion).

**Saved view for the dashboard.** Create a view in this database with the filter
`Review status = To review` and sorted by date added. The dashboard must read it
via `mode: "view"`, not a SQL query — SQL mode is metered and hits a limit on plans
below Enterprise, view mode is free on any. Detail — `reference/dashboard-notes.md`.

---

## Database 2 — "Jobs" (main tracker)

Your working application tracker. Structure it however suits you; the minimum the
dashboard needs to create a record from a confirmed candidate:

| Property | Type | Note |
|---|---|---|
| Name | Title | |
| Company | Text | |
| Link | URL | |
| Status | Select | Initial value when created from a candidate — e.g. `Found` |
| Source | Select | Carried over from the candidate card |
| Application deadline | Date | From the candidate's "Deadline" |

Add whatever else you need for your process (date applied, contact, interview
stage, post-interview notes, etc.) — the run and the dashboard don't touch those
fields.

---

## Dedupe

A cross-database query (`UNION` over two data sources) requires the Enterprise plan.
The run does **two separate queries** — over "Candidates" and over "Jobs" — and
compares links on its side. For LinkedIn, compare by the job ID in
`jobs/view/<id>`, not the full URL.

Those run queries are SQL mode, which is metered. There are only a few per run (2),
so they don't hit the limit. What hits the limit is the **dashboard**, if it reads
the list via SQL on every open — so it reads via a view (see above).

---

## Database IDs

After creating them, copy each database's ID from the URL and put it:

- in `CLAUDE.md` (so the run knows where to write and where to dedupe);
- in the dashboard code.
