# Sources — EXAMPLE (fictional)

The same made-up person: a Go developer, remote EU.

---

## Tier 1 — automated

| Source | URL / address | Method | Status and notes |
|---|---|---|---|
| We Work Remotely — Backend | `weworkremotely.com/categories/remote-back-end-programming-jobs` | `WebFetch`; if empty — Chrome | 12/03, static, fine first try |
| RemoteOK (API) | `remoteok.com/api` | JSON via `curl`/`fetch` | 12/03, returns a JSON array, filter by `position`/`tags` |
| "Golang Jobs" (TG) | `t.me/s/golangjobs` | fetch `t.me/s/`, on failure Chrome | 12/03, active, last post 11/03 |
| Company X careers (Greenhouse) | `boards-api.greenhouse.io/v1/boards/companyx/jobs` | JSON API; if large, filter in-browser | 12/03 |
| Company Y careers (Workday) | `companyy.wd5.myworkdayjobs.com/External` | POST `/wday/cxs/companyy/External/jobs` via `javascript_tool` in a browser | 12/03, GET returns an empty shell |
| LinkedIn Job Alerts | see the "Step 0" table in `CLAUDE.md` | Gmail connector | saved search "Go engineer, EU, remote", daily alert |
| "RemoteBackend Weekly" (newsletter) | `from:digest@remotebackend.example` | Gmail, `from:… in:inbox`, text HTML | confirmed on the first email |

**General-purpose channels:** "Golang Jobs" — almost all on-track, high hit rate,
almost every link is worth opening. If you add a general IT channel — screen
headlines there, open selectively.

---

## Needs recon

| Source | What's wrong | Next step |
|---|---|---|
| Company Z careers | page is a SPA, no jobs in the HTML, no API found | look for `__NEXT_DATA__` or an XHR in the Network tab via a browser |
| "EU Startup Jobs" (TG) | channel exists, last post 2 months ago | check again in a month, don't spend a run on it yet |

---

## Out of scope

| Source | Reason |
|---|---|
| LinkedIn — on-site job search, post search, profiles | ToS forbids automated collection. Email alerts are in Tier 1. |
| AngelList / Wellfound | requires login to view jobs |
| A separate aggregator with "Automated access prohibited" in the footer | explicit ban in the ToS — manual only, or via their email alert |

---

## Tool notes

- 12/03 — `curl` from the sandbox couldn't reach `remoteok.com` (`Tunnel failed`),
  in-browser `fetch` worked. Last time it was the other way. The matrix is
  unstable, try both.
