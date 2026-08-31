# Sources — status

Your list. Three sections: automated / needs recon / out of scope. For each source,
record the working method and the date it last checked successfully — methods go
stale silently (see `reference/tool-access-playbook.md`).

---

## Tier 1 — automated (part of the run)

<!-- FILL IN. Row format: -->

| Source | URL / address | Method | Status and notes |
|---|---|---|---|
| <Telegram channel> | `t.me/s/<channel>` | fetch `t.me/s/`, on failure Chrome `navigate`+`get_page_text` | <check date>, active, last post <date> |
| <Company career page> | `<url>` | `WebFetch`; if empty — Chrome | <date>, static, loads first try |
| <Company on Greenhouse> | `boards-api.greenhouse.io/v1/boards/<company>/jobs` | JSON API; if the response is large — filter in-browser via `javascript_tool` | <date> |
| <Company on Workday> | `<tenant>.myworkdayjobs.com/...` | POST to `/wday/cxs/<tenant>/<site>/jobs` — only via `javascript_tool` in a browser | <date> |
| Email alerts | see the "Step 0" table in `CLAUDE.md` | Gmail connector, `from:<address> in:inbox` | — |

**About general-purpose Telegram channels** (posts are roundups of job links, not
the job text itself): low hit rate. Screen post/company headlines from the preview
first, open the link only for plausible candidates. Following a link out of a post:
direct `href` reads are often blocked (tracking in the query string) — take
`a.href.split('?')[0]`, then navigate to the clean URL.

---

## Needs recon / unstable (doesn't block launch)

| Source | What's wrong | Next step |
|---|---|---|
| <source> | <API timeout / no fresh-jobs feed found / method unconfirmed> | <what to try> |

---

## Out of scope (deliberately)

| Source | Reason |
|---|---|
| LinkedIn — on-site job search, post search, personal profiles | ToS explicitly forbids automated collection, risks the account. **Email job alerts are in Tier 1** — that's a different action (reading an email, not accessing the site). |
| <aggregator that forbids scraping in its ToS> | Explicit ban on automated collection in the terms of use. Manual check or email alerts only. |
| <site with `Disallow: /` in robots.txt for the needed path> | robots.txt forbids crawling. |
| <private group / source requiring personal credentials> | Requires your personal API keys — a separate setup outside this automation. |

---

## Tool notes (general)

Live in `reference/tool-access-playbook.md`. Here — only the specifics of your own
sources found along the way (which method worked, which stopped, when).
