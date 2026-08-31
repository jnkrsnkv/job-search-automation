# SETUP — step by step (manual route)

Order matters. Don't skip steps.

> Easier: instead of doing this by hand, ask Claude to walk you through it — see
> `README.md`, "Setting up your own workspace". Claude follows `ONBOARDING.md` and
> helps at every step (interviewing you for filters, building your source list and
> LinkedIn alerts, filling in the files). This file is the same process if you want
> to do it yourself.

---

## 1. Connectors

Connect to Claude (via claude.ai → Settings → Connectors, or `claude mcp` for
Claude Code):

- **Gmail** — the mailbox where job alerts will arrive.
- **Notion** — the workspace where the databases will live.

Check that the model has browser access: `WebFetch` (built in) and, ideally,
Claude-in-Chrome. `WebFetch` alone works too, but some sources behind JS / anti-bot
protection won't load with it — see `reference/tool-access-playbook.md`.

---

## 2. Notion databases

Create two databases per `reference/notion-schema.md`:

1. **Candidates** — staging. The run drops new items here; you triage them.
2. **Jobs** — the main tracker. Only what you've approved lands here.

Copy the ID of each database (from the database page URL) — you'll need them for the
dashboard and for `CLAUDE.md`.

Give the Notion integration write access to both databases.

Create a saved **view** in the Candidates database for the dashboard: filter
`Review status = To review`, sort by date added. The dashboard reads this view in
view mode rather than running a SQL query (SQL mode is metered and hits a workspace
limit on plans below Enterprise) — see `reference/dashboard-notes.md`.

---

## 3. filter-criteria.md

Open `templates/filter-criteria.md` and fill it in for yourself:

- **Hard red flags** — what you always exclude, no discussion (industries,
  jurisdictions, employer types).
- **Green flags** — what raises relevance.
- **Tracks** — 3–6 role directions that fit you, each with search keywords. This is
  the core of the filter.
- **Location and work mode** — your base, what counts as acceptable (remote /
  hybrid / on-site, by region).
- **Priority** — rules for when a candidate is "High", when lower.

See `examples/filter-criteria.example.md` for the shape.

---

## 4. sources.md

Open `templates/sources.md` and write in your list. For each source:

- name, URL, which method Claude uses to read it, current status.

Split into three parts: **automated** / **needs recon** / **out of scope** (with a
reason — ToS forbids scraping, login required, technically unreachable).

**About LinkedIn.** On-site job search, post search, and personal profiles — do not
automate (the ToS explicitly forbids automated collection, and it risks your
account). But **LinkedIn email job alerts are fine**: that's reading an official
email in your inbox, not accessing the site. Set them up at linkedin.com/jobs → save
a search → enable the alert.

See `examples/sources.example.md`.

---

## 5. Lay out the files

Put these in the working folder Claude reads at the start of every session
(for Cowork — the workstation folder; for Claude Code — the project root or
`.claude/`):

- `CLAUDE.md` (filled)
- `filter-criteria.md`
- `sources.md`
- `MEMORY.md`

In `CLAUDE.md` fill in: your project identity, the Notion database IDs, paths to
`filter-criteria.md` / `sources.md`, and the email alert addresses in the Step 0
table.

---

## 6. Dashboard

See `reference/dashboard-notes.md`. Short version: ask Claude to build an artifact
that reads the Candidates database **through the saved view** (`mode: "view"`, not a
SQL query), shows cards, and gives two buttons per card — "Add to tracker" and
"Reject". Give it the `view://` URL, the database IDs, and the property names from
your schema.

---

## 7. Email alerts

1. Set up alerts at the sources (LinkedIn Jobs, industry job boards with
   newsletters, aggregators like Working Nomads, etc.).
2. Confirm the subscription via the link in the first email (Claude won't click it
   for you — it will only flag it).
3. Write the exact sender address of each alert into **two** sections of
   `CLAUDE.md`: the "Step 0" table and the allowed list in "Email alert handling".
4. Verify the address on the first real email — the sender is often not what you'd
   expect (`hello@…`, not `noreply@domain`).

---

## 8. Schedule

- **Cowork:** create a scheduled task; in the prompt, explicitly: "read `CLAUDE.md`
  in full, do Step 0 first, then the main loop, send the report per the checklist."
- **Claude Code:** a `/schedule` routine or cron with the same prompt.
- Frequency: every 2–3 days. More often if you have many sources with daily flow.

---

## 9. First run

Trigger it manually: "Do a full source run per `CLAUDE.md`."

Check:

- The Candidates database has new records with status **"To review"** (not blank —
  see the known bug in `reference/dashboard-notes.md`).
- Each has "Priority" and "Rationale" filled in.
- The report contains the Step 0 block (one line per email address) and a
  line-by-line status for every source.
- The dashboard shows the new candidates and the buttons work.

From here the system runs on schedule; every few days you work through the
dashboard.
