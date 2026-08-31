# Email job alert handling ("Step 0")

Why it's a separate hard block and how to run it.

---

## The problem this solves

Email alerts get skipped systematically. "A source" in the agent's head means
career pages and Telegram channels; emails in the inbox get forgotten, an
unprocessed backlog piles up, and you have to remind it by hand every time. A
verbal "check everything" and an end-of-run checklist don't help — not built into
the process.

**The fix:** an explicit first step of the run with a ready-made table of
`from:… in:inbox` queries (in `CLAUDE.md`), and a line-by-line report per address at
the end, without which the run counts as unfinished.

---

## `in:inbox` = "unprocessed"

"Unprocessed emails" for a source = `from:<address> in:inbox`. That's more reliable
than filtering by date: it doesn't depend on when the last run was, and it doesn't
break if a run was skipped or ran twice. An empty result = the source is done.

This only works if the email **leaves the inbox** (is archived) after processing.
Hence the discipline below.

---

## Order for one source

1. Run `from:<address> in:inbox` → list of threads.
2. Parse each thread: extract jobs, filter against `filter-criteria.md`, dedupe
   against both Notion databases.
3. Write the candidates that passed the filter to the Candidates database (status
   "To review").
4. **Only after a successful write** (or after an explicit "nothing to record"
   conclusion) — archive the thread: remove `UNREAD` and `INBOX` labels, add the
   label `job-alerts/processed`.

**Order is critical.** Archive strictly AFTER writing. If a run crashes midway, the
un-archived emails stay in the inbox and get picked up by the next run (correct
behavior). The reverse order = silent loss of jobs.

---

## Strict boundaries — what you may touch

- **Only** emails from addresses on the explicit list in `CLAUDE.md` (the "Email
  alert handling" section). A new source — first add its address there.
- **Never** touch emails not on the list: post notifications, messages from
  recruiters, InMail, any personal correspondence. The person reads those
  themselves.
- **Never** delete emails or mark them spam — archive only, it's reversible. The
  email stays in All Mail, searchable.
- A subscription confirmation ("confirm your email") — don't click it for the
  person, just flag that it arrived.

---

## Format specifics

- **LinkedIn Job Alerts** (`jobalerts-noreply@linkedin.com`) — parse from the
  **plaintext** version (the HTML is 130–180 KB, doesn't fit in context). Split job
  blocks on `View job:` by the **full URL including query string** (with the short
  `jobs/view/<id>` pattern, tracking junk bleeds into the next block). Dedupe by the
  job ID in `jobs/view/<id>`.
  - Work mode (remote/hybrid/on-site) is almost never visible in the emails — only
    city/country. The location rule on LinkedIn data mostly only fires as "work
    mode not verified".
  - Junk lines inside a block (`N connections`, `This company is actively hiring`,
    `Be an early applicant`, `Actively recruiting`) — discard them, otherwise the
    fields shift. Parse by the last three meaningful lines: title / company /
    location.
  - LinkedIn sometimes has **several sender addresses** (e.g. a separate "Jobs You
    Might Be Interested In"). Each is its own row in the Step 0 table.
- **Aggregator digests** (Working Nomads and similar) — plain text HTML, parse as
  simple text. The job link is often a redirect (`newsletter.domain/jobs/go/{id}/{sub}/`)
  — use it as-is, don't guess a direct URL. Dedupe by the (company, title) pair:
  the `job_id` in the link changes between mailings.
- **Tender / consultancy alerts** (if subscribed) — structurally not jobs; if you
  keep them in the same databases, add "Entry type" (Job/Tender) and "Deadline"
  properties (submission windows are short, days). A near deadline — call it out in
  chat, don't count on the person opening the dashboard in time.

---

## Report (mandatory)

One line per row of the Step 0 table:

```
✅ jobalerts-noreply@linkedin.com — 4 threads, 2 recorded
✅ hello@remote-digest.example — 1 thread, 0 recorded
⏭ no-reply@example.org — skipped (no access to the forward)
```

No such block in the run report → the report is invalid.
