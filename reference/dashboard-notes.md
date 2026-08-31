# Review dashboard

## What it does

- Reads the "Candidates" database, shows records with status **"To review"** as
  cards: title, company, priority, rationale, link, deadline.
- Two buttons per card:
  - **"Add to tracker"** → creates a record in the "Jobs" database (status
    `Found`), sets the candidate's `Review status = Added to tracker`.
  - **"Reject"** → sets the candidate's `Review status = Rejected` (the record is
    not deleted — it stays as a decision history).

The point: the run only proposes, the human always decides, one click.

---

## Important: read via a saved VIEW, not a SQL query

The `notion-query-data-sources` tool has two modes:

- **SQL mode** (`WHERE …`) — **metered**, and hits the workspace limit on plans
  below Enterprise. A dashboard that runs SQL on every open and every "Refresh"
  eventually gets `workspace has reached the usage limit for Query Data Source` and
  stops loading.
- **View mode** (`mode: "view"`, `view_url: "view://<id>"`) — runs a saved view of
  the database instead of SQL. **Not metered on any plan.**

**So the dashboard must read the candidate list via view mode.**

Setup:

1. In the "Candidates" database, create a saved view: filter `Review status = To
   review`, sorted by date added (newest first). Name it clearly, e.g.
   "Dashboard — To review".
2. Get its `view://` URL: `notion-fetch` on the **database ID** (not the data
   source) → the `<views>` block in the response → copy the right `view://<id>`.
3. When building the dashboard, tell Claude: read the list via
   `notion-query-data-sources` with `mode: "view"` and that `view_url`, **not** via
   SQL.

The "Add to tracker" / "Reject" buttons use `notion-create-pages` /
`notion-update-page` — those aren't subject to this limit, leave them alone.

---

## How to get the dashboard

The dashboard is an artifact (an HTML/JS page) Claude generates against your schema.
Create the saved view first (above), then ask:

> "Build a dashboard artifact. Read the candidate list from my Notion 'Candidates'
> database via `notion-query-data-sources` in `mode: "view"`,
> `view_url: "view://<...>"` — NOT via a SQL query. Show cards, with 'Add to
> tracker' and 'Reject' buttons per card. 'Add to tracker' creates a record in the
> 'Jobs' database (ID `<...>`, status 'Found') and marks the candidate 'Added to
> tracker'. 'Reject' marks it 'Rejected'. The properties are: <list from
> notion-schema.md>."

Rebuild it when you change the schema.

---

## If the dashboard breaks anyway

Don't edit `Review status` by hand directly in Notion. Changing the status in
staging and creating the record in "Jobs" are **two separate actions** — only the
"Add to tracker" button does both. Change the status by hand and no record appears
in "Jobs", and it's easy to miss.

Instead: tell Claude in chat which candidates to approve/reject, and it records
them through both steps at once. (This rule is also in `templates/CLAUDE.md`.)

---

## Known bug: blank status after a bulk write

After a bulk write of candidates, some records have a **blank** "Review status"
instead of "To review" — and the dashboard doesn't show them (the view filters by
"To review"). The run must, after any bulk write, run a check query `WHERE "Review
status" IS NULL` over the fresh pages and backfill the missed ones. This is in
`templates/CLAUDE.md`.

---

## Access

The artifact works with your Notion through the connector. Don't publish its link —
it's tied to your databases.
