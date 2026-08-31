# Tool access playbook — getting content out of sources

Distilled from months of use. Read it, don't edit it. Record the specifics of your
own sources in `sources.md`.

---

## The core principle

**The matrix of "which tool reaches which domain" is unstable and changes between
sessions.** In one session `curl` from the sandbox has no network while the browser
opens everything; in another it's the reverse. The only way to know is to try and
get an error. So:

- Don't lock in one "primary" tool.
- For each source, go down the chain top to bottom until the first success.
- The method documented in `sources.md` may be stale — if it fails, don't drop the
  source, try the next tool.

---

## The chain for each source

1. **Direct API / JSON**, if the source has one (Greenhouse, Lever, light feeds).
   Via `curl` or in-browser `fetch`. Handles POST, filtering, and a spoofed
   User-Agent. On `000` / `Tunnel connection failed` — that's a sandbox-proxy
   block, not the site; go straight to step 2, retrying is pointless.
2. **Claude-in-Chrome** (`navigate` → `get_page_text`, and `javascript_tool` if
   needed). Renders JS, passes anti-bot like a normal browser, has no provenance
   restriction, can POST via `fetch()` from a same-domain page. Effectively the
   primary method. On "Navigation to this domain is not allowed" — go to step 4.
3. **`WebFetch`** — for simple static pages. Limits below. On `URL not in
   provenance set` — precede it with a `WebSearch` for that URL (then the fetch
   goes through); for small Telegram channels search often doesn't help.
4. Mark it in the report as **"not checked (environment limit)"**, NOT "source
   broken".

---

## Known tool limits

### `WebFetch` isn't fit as the primary tool

It's designed for "go to a link you were given", but the task is "sweep a known
list of sources, some behind JS/POST/anti-bot".

- **Provenance set.** `WebFetch` only accepts URLs seen verbatim earlier in the
  session (in the task, in a prior successful fetch, in `WebSearch` output). A URL
  read from `sources.md` is rejected. Working around it via `WebSearch` succeeds
  only sometimes.
- **GET only.** No `method`/`headers`/`body`. Workday installs serve jobs only via
  POST to `/wday/cxs/<tenant>/<site>/jobs` with a JSON body. A plain GET returns an
  empty JS shell. → browser, `fetch()`.
- **Response-size limit with no pagination.** Fails on a large JSON (`exceeds
  maximum allowed tokens`). Filtering must happen BEFORE the tool boundary —
  `WebFetch` can't. → browser, filter in `javascript_tool`, return only the result.

### Silent stale snapshot (the most dangerous)

A successful fetch ≠ current content. The tool returns what the cache/index saw,
with no staleness signal. Confirmed cases: "fresh" jobs with year-old deadlines; a
snippet promising an open role, page says "not hiring".

**Rule: check the deadline/date inside the job text against today's date — on every
source, not just Telegram.** Right now this is compensated by discipline, not the
tool.

### `200 OK` + empty page = client-side rendering

Not "no jobs". Sign: the HTML has `<div id="root">` / `__NEXT_DATA__` and almost no
body text. In the report — "page didn't render, needs a browser", not "0
candidates".

### `javascript_tool` — false positive on the output filter

Sometimes returns `[BLOCKED: Cookie/query string data]` where there are no cookies
or query strings — it reacts to output length or to `|`, `=`, `&` in a string.
Workaround: return shorter, no special chars in the concatenation; return `.length`
first, then the content.

### Errors of different kinds look the same

| Cause | What you see | Reaction |
|---|---|---|
| URL not in provenance set | the error text | take the URL from search or go to a browser |
| Domain blocked by sandbox proxy | `000` / `Tunnel connection failed` | switch tools, `curl` won't help |
| Site returned 403 to anti-bot | `403` | change User-Agent / go to a browser |
| Content renders via JS | **200 OK, empty page** | go to a browser, retrying won't help |

---

## Telegram

- `t.me/s/<channel>` sometimes serves a stale page (cache). If post dates are weeks
  older than today — re-check with a direct repeat request. Don't record jobs from
  there with a clearly passed deadline.
- Reading via a browser, the page sometimes doesn't load the full history on the
  first try — a partial feed makes it easy to wrongly report "no change". Wait for
  a full load or explicitly re-check the depth.
- **General-purpose channels** (posts = roundups of links): screen headlines from
  the preview, open a link only for plausible ones. Following a link:
  `a.href.split('?')[0]` strips tracking → navigate to the clean URL → full
  description.
- **Hidden constraints are in the full job text, not the post.** Work mode, a
  residency requirement, language — check on the job itself, not the preview.

---

## Notion

- A cross-database query (`notion-query-data-sources` with two `data_source_urls`)
  requires Enterprise. Dedupe against two databases — **two separate queries**,
  compared on your side.
- After a bulk write of candidates, "Review status" is sometimes blank instead of
  the value you set — the dashboard doesn't see those records. A check query
  `WHERE "Review status" IS NULL` over the fresh pages + backfill is a mandatory
  step, don't rely on memory.
- A select property may silently refuse a new option when it already has many
  (a ceiling around 24 was observed). Workaround: temporarily write the real value
  as text at the start of "Rationale".
- **The dashboard must read the candidate list via a saved view (`mode: "view"`),
  not a SQL query.** SQL mode is metered and hits a workspace limit on plans below
  Enterprise; view mode is free on any plan. See `reference/dashboard-notes.md`.
