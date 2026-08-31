# Filter criteria

A working digest for fast filtering during source runs. Fill it in for yourself.

---

## Hard red flags (exclude immediately, no exceptions)

<!-- FILL IN. Example categories: -->

- Company registered in <jurisdiction you exclude> or paying taxes there.
- Industries you won't work in (e.g. gambling/betting, tobacco, alcohol, weapons,
  exploitation of vulnerable groups).
- <yours>
- If a company's status on these points can't be verified from open sources —
  **don't exclude automatically**, but write "not verified" in the notes.

## Market red flags (don't block, note them)

- Fuzzy area of responsibility with no clear owner/manager.
- "You will wear many hats" with no structure.
- A cult of overwork and heroics.
- A role about "growth" where nobody can name a metric.
- <yours>

## Green flags (raise relevance)

<!-- FILL IN. What makes a job noticeably more interesting to you. -->

- <company type / field>
- <product type / approach>
- <yours>

---

## Tracks and keywords

The core of the filter. 3–6 role directions that fit you, each with search
keywords. Order = priority.

<!-- FILL IN. Example: -->

| Track | Priority | Search keywords |
|---|---|---|
| <track 1 name> | 1 | keyword, keyword, keyword |
| <track 2 name> | 2 | keyword, keyword |
| <track 3 name> | 3 | keyword, keyword |

**Look at the content of the job, not the title.** A role may be named differently
but describe exactly what's in your tracks — and vice versa, a matching title
doesn't mean a matching substance.

**Doesn't fit by actual experience:** roles requiring what you clearly don't have
(a deep technical stack, years of formal people management, a PhD in a narrow
area). Not a red flag, but don't record such jobs — low match.

**Formal minimum requirements — a separate filter.**
<!-- If you lack a formal requirement that's often listed as mandatory (a degree,
citizenship, a licence, N years of strictly defined experience): -->
- Listed as **mandatory** (minimum / required) → **don't record**, note in the
  report on one line that it was filtered out on a formal requirement.
- The same listed as **desirable / a plus** → record it, noting in the notes what's
  formally missing.
- Unclear whether mandatory or desirable → record with a "clarify" note.

**Role level is not a cutoff** (if you're deliberately willing to step down for a
strong company). Record it if the company is a green flag. Don't record if the role
is below your level **and** the company is unremarkable — otherwise staging fills
with ordinary junior roles.

---

## Location and work mode

<!-- FILL IN for your base. Example logic: -->

- **<your city / country>** — all modes: on-site, hybrid, remote.
- **<your wider region>** — hybrid or remote only. Don't record on-site roles
  outside your home base (relocation is / is not on the table).
- **<farther / other continents>** — remote only by default (on-site/hybrid need a
  visa / work authorization).
- If the work mode isn't stated explicitly — **don't exclude automatically**, write
  "work mode not verified" in the notes.
- Language: for roles facing a local audience in <a country with another language>,
  assume the local language is required by default if your level isn't enough —
  write "likely requires <language>, verify".

### Geographic skew — how to fix it

If the database tilts toward one region (usually the one with more
English-language jobs), and those jobs are mostly on-site — don't try to "search
more in your city", **raise the weight of remote jobs across your wider region**.
The filter removes noise but doesn't create local jobs.

---

## "Green zone" — priority organizations (waiving location/level limits)

To focus the flow, you can keep a list of organizations for which the location
rules and the role-level rule **don't apply** (record regardless of location or
confirmed remote).

**Who's in the green zone:**

1. **A category** (no explicit list) — <describe the type, e.g. "any media company
   or NGO in your field">.
2. **An explicit list** of specific employers you personally prioritize. Extended
   only on your explicit say-so.
   - <company>
   - <company>

**What's waived:** the location/work-mode rule entirely; the limit on role level
and formal experience.

**What still gets checked even in the green zone:**

- Hard red flags — always exclude.
- A match with at least one track — the green zone doesn't waive "the job is
  actually about what you want to do".
- Priority per the normal rules.
- In "Rationale" — a `[green zone]` tag at the start and what exactly was waived
  (e.g. `[green zone] on-site, remote not confirmed`).

## Outside the green zone — High priority only

For all other sources/companies: record only candidates with **High** priority into
staging. "Medium" and "Maybe" outside the green zone — don't record (they clutter
the dashboard). If a job still looks interesting — mention it on a line in the run
report, without a card in Notion.

---

## Application priority

The "Priority" select field in the Candidates database, three values:

- **High** — priority track 1–2 + green-flag company + no red flags + the job is
  fresh. A small applicant count (if visible) — an argument for High.
- **Medium** — matches a track, but no strong green flag, or track 3+.
- **Maybe** — a tangential match, an unverified red flag, or the role needs
  substantial stretching.

First line of "Rationale": `<Level> — <reason>`, then the track rationale on a new
line. Examples:

- `High — top track, green-flag company, job is fresh, red flags not verified`
- `Maybe — company not verified, match only on the role title`

Don't invent a number-percent ("72% match") — false precision.

---

## Freshness check (mandatory before recording)

- Compare the publication date / application deadline against today's date.
- Deadline clearly passed → don't record.
- Public Telegram channels (`t.me/s/...`) sometimes serve a stale page — if the
  dates in posts are weeks/months older than today, re-check with a direct repeat
  request before recording anything from there.
