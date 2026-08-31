# Job Search Automation

A framework for **semi-automated job hunting**. Claude regularly sweeps your list of
sources (Telegram channels, company career pages, ATS APIs, email job alerts),
filters what it finds against your profile, and drops candidates into a Notion
database where you approve or reject each one with a single click.

It is **not "clone and run."** Job hunting is inherently custom: your sources, your
filters, your Notion. This template gives you the structure, the methodology, and
the gotchas already collected over months of real use. Setup takes 2–4 hours.

Keywords: automated job search, job hunting automation, Notion job tracker, AI job
search agent, LinkedIn job alerts, career page monitoring, job board scraper
alternative.

---

## What you need

- **Claude with connectors.** Claude Code, Claude Cowork, or claude.ai with Gmail
  and Notion connected. Essential:
  - **Gmail** connector — reading job alerts;
  - **Notion** connector — writing candidates;
  - browser access for the model — `WebFetch` and/or Claude-in-Chrome, for career
    pages and Telegram.
  - **This does not run on ChatGPT as-is** — it has a different tool model (no
    managed Gmail/Notion connectors, no browser, no scheduling). You can borrow the
    filtering and email-parsing logic, but not the automated source sweep.
- **A Notion account** — the free plan is enough.
- **A separate Gmail** (recommended) — dedicated to job alerts, kept apart from
  personal mail. Every archiving rule in this template assumes that inbox contains
  nothing but newsletters.
- **A source list** — you build it (`templates/sources.md`).
- **A filtering profile** — what you want to do, what you exclude
  (`templates/filter-criteria.md`).

---

## How it works

```
                your source list (sources.md)
                            │
         ┌──────────────────┴───────────────────┐
         │   RUN (Claude, every 2–3 days):      │
         │   1. email alerts (Step 0)           │
         │   2. sweep the other sources         │
         │   3. filter against filter-criteria  │
         │   4. dedupe against both Notion DBs  │
         └──────────────────┬───────────────────┘
                            │  new & relevant
                            ▼
          Notion "Candidates" DB — status "To review"
                            │
                            ▼
        you open the dashboard → per card: "Add to tracker" / "Reject"
                            │
                            ▼
          approved → Notion "Jobs" DB (status "Found")
```

The run fires on a schedule (a Cowork scheduled task / a `/schedule` routine / a
cron in Claude Code) and also on demand when you ask in chat.

---

## Setting up your own workspace

Two ways:

**A. Claude walks you through it (recommended).** Clone the repo, open it in Claude
Code / Cowork, and send something like:

> Read this repository — `README.md` and `ONBOARDING.md`. Tell me in your own words
> what this system is and how it works. Then, if there's a setup guide, walk me
> through it step by step: at each stage explain what we're doing and why, ask me
> the questions you need, help me build my source list and LinkedIn job alerts,
> fill in the files for me, and show me the result before moving on. Don't skip
> ahead.

Claude then follows `ONBOARDING.md`: checks connectors, helps create the Notion
databases, interviews you about your tracks and red flags, works through your
source list, helps you set up and verify email alerts, assembles `CLAUDE.md`,
builds the dashboard, sets the schedule, and does the first run. It tracks progress
in `SETUP-PROGRESS.md` — if you get interrupted, you resume where you left off.

**B. By hand from a checklist.** If you'd rather do it yourself — follow `SETUP.md`.

---

## Setup order (reference)

Full detail is in `SETUP.md` (or Claude walks you through `ONBOARDING.md`). Short
version:

1. Connect the Gmail + Notion connectors to Claude.
2. Create 2 Notion databases per `reference/notion-schema.md`.
3. Fill in `templates/filter-criteria.md` for yourself.
4. Fill in `templates/sources.md` — your own list.
5. Put the filled `CLAUDE.md`, `filter-criteria.md`, `sources.md`, `MEMORY.md` in
   the working folder Claude reads at the start of a session.
6. Build the review dashboard (`reference/dashboard-notes.md`).
7. Set up email job alerts, record their sender addresses in `CLAUDE.md` (Step 0).
8. Set the schedule.
9. Do one manual run and check the result in Notion.

---

## Template files

| File | What it is | Touch? |
|---|---|---|
| `ONBOARDING.md` | Script for Claude: how to walk you through setup step by step. | Claude reads it |
| `SETUP.md` | The same process as a checklist, if you set it up by hand. | You read it |
| `templates/CLAUDE.md` | Working instructions for Claude: the whole workflow. | Fill in placeholders |
| `templates/filter-criteria.md` | Tracks, red/green flags, location and priority rules. | Fill in fully |
| `templates/sources.md` | Source list + status of each. | Fill in fully |
| `templates/MEMORY.md` | Where Claude records decisions/findings as it goes. | Nearly empty at start |
| `reference/tool-access-playbook.md` | How to get content out of sources, which tool to try in what order, gotchas. | Read, don't edit |
| `reference/email-alerts-pattern.md` | Parsing email alerts (Step 0), why it's a hard block. | Read, don't edit |
| `reference/notion-schema.md` | Exact property list for both Notion databases. | Setup instruction |
| `reference/dashboard-notes.md` | What the review dashboard is and how to get one. | Instruction |
| `examples/` | Filled `sources.md` and `filter-criteria.md` with fictional data. | Example |
| `LICENSE` | MIT. | — |
| `PUBLISHING.md` | Instructions for the template author on publishing to GitHub. | Author only; delete later if you want |

---

## Privacy

The template contains nobody's real sources or profile. **Do not commit your filled
`sources.md` or `filter-criteria.md` to a public repo** — keep them in the working
folder only. The included `.gitignore` already covers this.

---

## License

[MIT](LICENSE) — use it freely in your own projects, including modifying and
redistributing. Attribution is not required but appreciated.

## Support

If this template saved you time or helped you land a job, you can say thanks
[with a donation](https://donate.stripe.com/5kQbJ2aY70TJfaeegD2Ji01) — entirely
optional, the project is free either way.