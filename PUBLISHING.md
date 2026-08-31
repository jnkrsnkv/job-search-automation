# Publishing this to GitHub (for the template author)

This file is for you, not for users. After publishing you can delete it from the
repo or leave it.

---

## 0. What gets published

Publish the **contents of the `_shareable-template/` folder** — so that `README.md`,
`LICENSE`, etc. end up at the repo root. Do not publish the parent workstation
folder (it holds your real `CLAUDE.md` / `sources.md` / `MEMORY.md`).

---

## 1. Fill in your details in 3 places

- **`LICENSE`** — replace `<YOUR NAME OR GITHUB HANDLE>` with what you publish
  under.
- **`README.md`** — replace `<STRIPE LINK>` in the "Support" section.
- **`.github/FUNDING.yml`** — replace `https://buy.stripe.com/REPLACE_WITH_YOUR_LINK`.
  This file adds a "Sponsor" button to the repo header.

If you're not doing a Stripe link yet — just remove the "Support" section from the
README and delete `.github/FUNDING.yml`; add them later.

---

## 2. Check there's nothing private

```
cd _shareable-template
grep -rniE 'your name|your real companies|your city' .   # should be empty
ls -a                                                    # .gitignore present
```

`.gitignore` already ignores `CLAUDE.md`, `filter-criteria.md`, `sources.md`,
`MEMORY.md`, `SETUP-PROGRESS.md` at the root — so if someone (or you) fills them in
inside a fork, they don't leak into the public repo.

---

## 3. Create the repo and push

You need: `git` installed, a GitHub account, and either the `gh` CLI or configured
SSH/token.

### With `gh` (simpler)

```
cd "path/to/_shareable-template"
git init
git add .
git commit -m "Job search automation template v1"
gh auth login          # once, if not already
gh repo create job-search-automation --public --source=. --push \
  --description "Semi-automated job hunting: Claude sweeps your sources and drops candidates into Notion"
```

### Without `gh`

1. On github.com → New repository → name `job-search-automation`, Public, **no**
   "Add README / .gitignore / license" checkboxes (the files already exist).
2. Locally:

```
cd "path/to/_shareable-template"
git init
git add .
git commit -m "Job search automation template v1"
git branch -M main
git remote add origin git@github.com:<handle>/job-search-automation.git
git push -u origin main
```

---

## 4. Set up the Stripe Payment Link (optional)

Done in the Stripe dashboard, not in code:

1. Stripe Dashboard → **Payment links** → **New**.
2. Type — "Customers choose what to pay" (donation) or a fixed amount. Name the
   product clearly, e.g. "Support for job-search-automation".
3. Create → copy the link, of the form `https://buy.stripe.com/xxxxxxxx`.
4. Paste it into `README.md` ("Support" section) and `.github/FUNDING.yml`, commit
   and push:

```
git add README.md .github/FUNDING.yml
git commit -m "Add support link"
git push
```

After that a **Sponsor** button appears in the repo header.

> Setting up the Stripe account and creating the Payment Link is on you — those are
> actions on a payment account, and Claude does not perform them for you.

---

## 5. Polish the repo (helps discoverability)

- **About** (the gear icon top-right on the repo page): a short description +
  topics: `job-search`, `job-hunting`, `notion`, `claude`, `automation`,
  `ai-agent`, `linkedin-jobs`.
- Make sure `README.md` renders cleanly on the landing page. The first paragraph
  and the keyword line matter for search — keep them.
- Optional: enable Issues so people can file bugs / suggestions.

---

## 6. Hand it to someone

A link to the repo plus one line is enough:

> Clone it, open it in Claude Code or Cowork, and ask: "read the README and
> ONBOARDING.md, tell me what this is, and walk me through setup step by step."

Everything else is in `README.md`.
