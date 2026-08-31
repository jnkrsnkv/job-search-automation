# ONBOARDING — walk the user through setup

**This is an instruction for Claude, not for a human.** The user has cloned this
repository and asked for help deploying the system for themselves. Take them through
the stages, one at a time, skipping nothing.

The first prompt the user should send you is in `README.md`, "Setting up your own
workspace".

---

## How to run this

- Work **strictly through the stages below, in order.** Don't jump ahead, even if
  the user says "just do it all."
- Each stage: (1) briefly explain what and why; (2) ask the stage's specific
  questions; (3) wait for the answer; (4) do your part — create/fill a file;
  (5) show the result, wait for "ok"; (6) mark the stage done, move on.
- Track progress in `SETUP-PROGRESS.md` in the user's working folder — a checklist
  of stages with statuses, plus data collected along the way (database IDs, alert
  addresses). Update it after each stage. If the session breaks, read it on the
  next run and continue from the unfinished stage.
- Put filled files (`CLAUDE.md`, `filter-criteria.md`, `sources.md`, `MEMORY.md`)
  in the **user's working folder** (the root of their workstation / project), NOT
  in the template folder. The template stays a reference.
- Don't invent the substance for the user — their tracks, red flags, sources.
  Offer options, but they decide.
- The user may not know terms (ATS, staging, boolean). Explain as you go, plainly.

## Before you start — read the repository

Read: `README.md`, `SETUP.md`, `reference/tool-access-playbook.md`,
`reference/email-alerts-pattern.md`, `reference/notion-schema.md`,
`reference/dashboard-notes.md`, all 4 files in `templates/`, both examples in
`examples/`. Make sure you understand the whole flow: sources → run → Notion
staging DB → dashboard → main tracker.

Then give the user: a **short summary** (what this is, how it works, 4–5 sentences)
plus a **personalized setup plan** (the stages below, flagging what will need
action from them personally — sign-ups, clicks in third-party interfaces, waiting
for emails). Wait for them to confirm they're ready to start.

---

## Stage 1. Prerequisites

Goal: the environment is ready.

- Check access to the **Gmail** connector (e.g. `list_labels`). If missing — stop
  onboarding, explain how to connect Gmail to Claude, ask them to come back.
- Check access to the **Notion** connector (e.g. `notion-search`). Same.
- Check browser access (`WebFetch` and/or Claude-in-Chrome).
- Ask: is there a **separate Gmail** for job alerts? Recommended. If not — explain
  why (the archiving rules assume an inbox with newsletters only) and let them
  choose: create one now, or use an existing one carefully.

---

## Stage 2. Notion databases

Goal: two databases created, their IDs recorded in `SETUP-PROGRESS.md`.

1. Explain the purpose of the two databases (Candidates — staging; Jobs — main
   tracker) per `reference/notion-schema.md`.
2. Ask which workspace / under which page to create them.
3. Try to create both databases through the Notion connector with all properties
   from the schema. If the connector can't create databases or properties of the
   needed types — give the user a step-by-step "add property X of type Y with
   values Z", one at a time, and wait for confirmation.
4. Ask the user to confirm in the page's Connections settings that the integration
   has write access to both databases.
5. Grab both database IDs from the URL, record them.
6. **Create a saved view in the Candidates database** for the dashboard: filter
   `Review status = To review`, sort by date added (newest first), name it e.g.
   "Dashboard — To review". Get its `view://` URL via `notion-fetch` on the
   database ID (the `<views>` block in the response). Record it — needed in
   Stage 7. Why a view specifically — see `reference/dashboard-notes.md` (SQL mode
   is metered and hits a limit; view mode is free).

Checkpoint: both databases visible through the connector, properties in place, view
created and its URL recorded.

---

## Stage 3. filter-criteria.md — interview

Goal: a filled `filter-criteria.md` in the working folder.

Interview in blocks, not all at once:

1. **What do you do / what do you want to do?** → propose 3–6 tracks with
   keywords, show `examples/filter-criteria.example.md` as the format, confirm
   track priority.
2. **What do you exclude entirely?** (industries, jurisdictions, employer types)
   → hard red flags.
3. **What, conversely, pulls you in?** → green flags.
4. **Where are you based, what will you consider?** (remote / hybrid / on-site by
   region; open to relocation?) → the location section.
5. **Is there a formal requirement you don't have** that's often listed as
   mandatory (a degree, citizenship, a licence)? → the minimum-requirements rule.
6. **Any dream companies** you'd ignore location/level for? → the green zone.

Assemble it into `filter-criteria.md` following `templates/filter-criteria.md`. Show
it in full, wait for edits and "ok".

---

## Stage 4. sources.md — interview

Goal: a filled `sources.md`.

1. Ask: **which job sources do you check by hand right now?** (company sites,
   Telegram channels, job boards, newsletters, LinkedIn). Collect the full list.
2. For each, work out together:
   - **Type and method.** Career page — which ATS? Open the careers page, look at
     the domain of the application form: `greenhouse.io`, `lever.co`,
     `myworkdayjobs.com`, `teamtailor.com`, `ashbyhq.com`, `personio`. Pick a
     method per `reference/tool-access-playbook.md`.
   - **Section:** automated / needs recon / out of scope. LinkedIn on-site search,
     aggregators that forbid scraping in their ToS, login-gated sources → out of
     scope. Explain why, explicitly.
3. For companies the user named as interesting but doesn't have a careers URL for —
   help find it (`WebSearch` "<company> careers").
4. Assemble `sources.md`. Leave email alerts as a pointer to Stage 5.
5. Show it, wait for "ok".

---

## Stage 5. LinkedIn job alerts and other newsletters

Goal: alerts set up, exact sender addresses recorded.

The user does this by hand in their own browser — you guide and verify.

### LinkedIn

1. Discuss **which saved searches** they need — driven by the tracks in
   `filter-criteria.md`. For each, propose:
   - a keyword string (LinkedIn understands `OR` and quotes for phrases; complex
     boolean `AND` between groups is unreliable on the free search — several
     simple searches beat one over-engineered one);
   - a location filter + a Remote filter if needed.
2. Walk them through: linkedin.com/jobs → enter the query and filters → "Set alert"
   / save the search → frequency (daily for an active search) → delivery to email.
   The list of saved alerts lives at **linkedin.com/jobs/jam/** (not
   `/jobs/alerts` — that goes to the ordinary search).
3. **Find the real sender address.** Have the user wait for the first email (or the
   "alert created" confirmation). Read it via the Gmail connector, record the exact
   `from:`. For LinkedIn it's usually `jobalerts-noreply@linkedin.com`, but there's
   sometimes a second address (`jobs-noreply@linkedin.com`) — record both.

### Other newsletters (industry boards, aggregators)

1. The user subscribes on the site.
2. The first email is often a subscription confirmation with a link. **You don't
   click it** for the user — you ask them to confirm.
3. After the first real email — read it, record the exact `from:` (often not what
   you'd expect — `hello@…`, not `noreply@domain`).

### Recording

Put all addresses in `SETUP-PROGRESS.md`; they go into two sections of `CLAUDE.md`
in Stage 6.

---

## Stage 6. Assemble CLAUDE.md

Goal: a working `CLAUDE.md` in the working folder.

Take `templates/CLAUDE.md`, fill in:
- **Identity** — from the Stage 3–4 answers (what this project is, what routes
  here, what doesn't).
- Paths to `filter-criteria.md` and `sources.md`.
- **Notion database IDs** from Stage 2.
- **The "Step 0" table** — addresses and methods from Stage 5.
- The allowed address list in "Email alert handling" — the same addresses.

Lay out `CLAUDE.md`, `filter-criteria.md`, `sources.md`, `MEMORY.md` in the working
folder. Show where they are. Wait for "ok".

---

## Stage 7. Dashboard

Goal: a working review artifact.

Per `reference/dashboard-notes.md`, build an artifact against the user's schema:

- read the candidate list via `notion-query-data-sources` in `mode: "view"` with
  the `view_url` from Stage 2 — **NOT via a SQL query** (it would hit the workspace
  limit and stop loading);
- the "Add to tracker" / "Reject" buttons via `notion-create-pages` /
  `notion-update-page` (database IDs from Stage 2, property names from their
  Notion);
- verifying the buttons can wait until Stage 9.

Give them the artifact link, tell them not to publish it.

---

## Stage 8. Schedule

Goal: the run fires by itself.

Ask where the user runs Claude (Cowork / Claude Code / claude.ai) and how they make
recurring tasks. Help create a recurring task with a prompt like:

> Read `<path>/CLAUDE.md` in full. Do Step 0 (email alerts) first, then the main
> loop over every source in `sources.md`. Send the report strictly per the
> checklist in CLAUDE.md.

Frequency: every 2–3 days to start. Explain: the prompt must explicitly require
reading CLAUDE.md in full — otherwise the task tends to "narrow" and skip the
email step.

---

## Stage 9. First run and check

Trigger a full manual run. Check with the user:

- The Candidates database has new records, all with **"Review status" = "To
  review"** (not blank). If blank — fix it (an `IS NULL` query + backfill), record
  in `MEMORY.md` that the bug recurred.
- "Priority" and "Rationale" are filled in per the format.
- The report contains the Step 0 block (one line per address) and a line-by-line
  source status.
- The dashboard shows candidates; both buttons write to Notion (run one through
  "Add to tracker" — check the record appeared in Jobs and the status in
  Candidates changed).

Mark onboarding complete in `SETUP-PROGRESS.md`. Tell the user what's next: open
the dashboard every few days and triage candidates; extend the rules and sources in
`filter-criteria.md` / `sources.md` as things become clearer.

---

## If the user comes back later

Read `SETUP-PROGRESS.md`, tell them which stage they stopped at, continue from
there.
