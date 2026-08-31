# Identity

<!-- FILL IN. One paragraph: what this project is, what routes here, what does NOT. -->

Job search automation. Routes here: checking external sources (Telegram channels,
career pages, ATS APIs, email job alerts) for new openings, filtering against the
tracks and red/green flags in `filter-criteria.md`, maintaining the "Candidates"
staging database and the "Jobs" main tracker in Notion.

Does not route here: <!-- tailoring a CV to a specific job / assessing a job that's
already in the main tracker / whatever else you have alongside this --> .

---

# Workflow

## Reaching sources

Full detail in `reference/tool-access-playbook.md`. Key points:

- **The matrix of "which tool reaches which source" is unstable** and changes
  between sessions. Don't lock in one "primary" tool — go down the chain until the
  first success: Claude-in-Chrome → `WebFetch` → `curl` → mark "not checked
  (environment limit)".
- "Not checked (environment limit)" and "source is broken" are **different
  things**. Almost every "breakage" in history turned out to be one tool being
  unavailable, not the source. Don't write off a source without trying a second
  method.
- **Check freshness on every source, not just Telegram.** Tools silently return a
  cached / search snapshot with no staleness signal. Before recording a candidate,
  compare the deadline/date inside the job text against today's date.
- **`200 OK` + an empty page = probably client-side rendering, not "no jobs".** In
  the report write "page didn't render, needs a browser", not "0 candidates".

## Main loop

### Step 0 — email alerts go FIRST, every run, no exceptions

The run has not started until every query in the table below has been executed.
This is a mandatory first block of every run, wherever it runs (scheduled task or
interactive session). If any query is skipped, the run is incomplete and that is
stated explicitly in the report with a reason.

<!-- FILL IN with your own alert addresses. Take the address from the first real email. -->

| Source | Gmail query (run as-is) | Parsing |
|---|---|---|
| LinkedIn Job Alerts | `from:jobalerts-noreply@linkedin.com in:inbox` | plaintext, split on `View job:` by full URL, dedupe by job ID |
| <!-- industry job board --> | `from:<address> in:inbox` | plaintext / text HTML |
| <!-- aggregator (Working Nomads etc.) --> | `from:<address> in:inbox` | text HTML, dedupe by (company, title) pair |

Order for each source: (1) run the `in:inbox` query → list of threads; (2) parse
each thread per the source's rules; (3) write candidates to Notion; (4) **only
after writing** — archive the email (remove `UNREAD`/`INBOX`, add the label
`job-alerts/processed`). An empty `in:inbox` result = the source is done.

Full parsing and archiving rules — `reference/email-alerts-pattern.md`.

**Why this is a separate hard block:** email alerts get skipped systematically —
"a source" in the agent's head means career pages and Telegram, and the emails get
forgotten. A verbal "check everything" and an end-of-run checklist weren't enough —
what works is an explicit first step with ready-made queries.

### The core flow

**"Repeat run" rule.** If asked to check the sources again — even if the previous
run was a few hours ago the same day — do a full run over all sources as if the
previous one hadn't happened. Don't skip sources on the grounds of "already looked
today." Channels and newsletters post throughout the day unpredictably; the cost of
a missed job is higher than the cost of an extra check.

1. Go through the sources in `sources.md` (the "automated" section), check freshness
   (deadlines/dates against today), filter against the tracks and red/green flags
   in `filter-criteria.md`.
2. **Dedupe** — compare the link against records in both Notion databases
   ("Candidates" and "Jobs") so nothing is recorded twice. For LinkedIn compare by
   the job ID in `jobs/view/<id>`, not the full URL (tracking parameters differ
   every time). A cross-database query in Notion requires Enterprise — do **two
   separate queries** and compare on your side.
3. Write new relevant jobs into the **Candidates** staging database with status
   **"To review"** — never create a record directly in "Jobs". Fill in "Priority"
   and, as the first line of "Rationale", the reason for the priority (rules in
   `filter-criteria.md`).
4. <!-- The user --> opens the dashboard and clicks "Add to tracker" or "Reject"
   on each candidate.
5. On "Add to tracker" — the dashboard creates a record in "Jobs" (status "Found")
   and marks the Candidates record "Added to tracker".
6. On "Reject" — the Candidates record is marked "Rejected" (not deleted — it stays
   as a decision history).

**If the dashboard won't load** (e.g. the Notion SQL query limit): the user does
NOT edit the "Review status" field by hand in Notion. Changing the status in
staging and creating the record in "Jobs" are two separate actions — only the "Add
to tracker" button does both; a manual status edit leaves the card with no record
in "Jobs". Instead the user sends into chat which candidates to approve/reject, and
Claude records them through both steps at once. (The proper fix is to rebuild the
dashboard to read through a saved view — see `reference/dashboard-notes.md`.)

### Mandatory end-of-run checklist

Every run must end with an explicit line-by-line report:

1. **The "email alerts" block (Step 0).** One line per row of the Step 0 table: the
   `from:… in:inbox` query run + number of threads found + number of candidates
   recorded. Format: `✅ <address> — N threads, M recorded` / `⏭ skipped (reason)`
   / `❌ error (what exactly)`. **No such block in the report → the report is
   invalid, the run is not complete.**
2. Every source in `sources.md` (the "automated" section), by name — status:
   ✅ checked (how many new) / ⏭ skipped (with reason) / ❌ error.

A source absent from the report is itself an error signal. Don't send a free-form
report without the checklist.

### Post-bulk-write check

Right after any bulk write of candidates — a check query `WHERE "Review status" IS
NULL` over the just-added pages, and backfill the missed ones. Don't rely on
remembering that the status was set: the "blank status after bulk insert" bug
recurs, and the dashboard filters exactly by "To review", so blank-status records
are invisible.

---

# Resources

| Resource | Read when... |
|---|---|
| `filter-criteria.md` | Before every source run — tracks, red/green flags, keywords. |
| `sources.md` | Full source list with status and per-source technical notes. |
| `reference/tool-access-playbook.md` | How content is retrieved, tool order, gotchas. |
| `reference/email-alerts-pattern.md` | Parsing and archiving email alerts. |
| `reference/notion-schema.md` | Notion database properties. |

---

# Editorial Rules

- Never create a record directly in the "Jobs" database — only via the confirmed
  step in the dashboard.
- If a job's deadline has already passed at the time of checking — don't record it.
- If a hard red flag couldn't be verified — write "not verified" in the notes
  explicitly, don't stay silent.
- "Rationale", first line: `<Priority> — <short reason>`, then on a new line the
  track-based rationale. No number-percent match figure — the three priority
  levels instead.
