# Gotchas

> This repository is a static GitHub Pages site (`dbl-facts.com`) serving a single HTML audit report. There is no build system, no application code, and no dependencies. Most of the entries below concern deployment mechanics and maintenance traps specific to this setup.

---

### Commits to `main` are immediately live in production

GitHub Pages auto-deploys from the `main` branch with no staging step and no CI gate. Any push — including a merge from a PR — goes live at `dbl-facts.com` within seconds.

**Why this exists:** The repo was created as a quick static-page host, not a deployed application. There is no workflow file in `.github/` configuring a separate deploy step.

**How to avoid:** Draft changes on a feature branch and review the raw HTML before merging. There is no rollback mechanism other than reverting the commit and waiting for Pages to redeploy.

---

### Deleting `CNAME` silently breaks the custom domain

The file `CNAME` (containing `dbl-facts.com`) is the only thing binding the GitHub Pages deployment to the custom domain. It looks like a throwaway config file but is load-bearing infrastructure.

**Why this exists:** GitHub Pages reads `CNAME` at deploy time to configure DNS routing. If the file is absent the site reverts to the `avantageusa.github.io/dbl-facts` default URL with no error visible in the repository.

**How to avoid:** Do not delete or gitignore `CNAME`. If the domain ever changes, update `CNAME` and the DNS `CNAME` record together.

---

### Google Analytics tracking ID is hardcoded in two separate places

`index.html` loads GA via `gtag.js?id=G-DWM7HTNPST` in the `<script src>` tag **and** calls `gtag('config', 'G-DWM7HTNPST')` separately. Changing the property ID requires updating both occurrences; updating only one silently continues loading the wrong property.

**Why this exists:** Standard gtag snippet pattern — the ID must appear in both the loader URL and the config call. There is no constant or variable extracting it.

**How to avoid:** Search for `G-DWM7HTNPST` (not just `gtag`) when changing the Analytics property.

---

### All report metrics are baked into the HTML — there is no data layer

Every figure in the audit table (`28,334 clicks`, `2,359 users`, conversion counts, etc.) is a hardcoded string in `index.html`. The same numbers also appear in the prose paragraphs below the table. If a metric needs to be corrected, it must be updated in at least two places in the file and the values must be kept consistent manually.

**Why this exists:** The report was authored as a one-time static document, not a live dashboard.

**How to avoid:** When editing a number in the table, search `index.html` for the same value to find all prose references. Numbers such as `28,334` and `2,359` each appear in both the `<td>` cells and the analysis text.

---

### Coding standards in `.cursor/rules/coding-standards.mdc` mostly do not apply here

The standards file mandates TypeScript strictness, React hook rules, mutation testing (`npm run stryker`), and plan/review artifacts in `docs/`. None of these apply — this repo has no TypeScript, no React, no `package.json`, and no test suite. The file was bootstrapped from a canonical org-wide template.

**Why this exists:** The bootstrap script (`add-ai-rules.sh` in the parent workspace) copies the standards file into every repo in the org regardless of stack.

**How to avoid:** Treat the standards as aspirational scaffolding for any future code added to this repo. AI agents following the standards verbatim (e.g., requiring a plan document before editing `index.html`) are over-applying them — use judgment.

---

### Stale remote branch may cause confusion

`origin/chore/add-ai-coding-standards` exists on the remote with a divergent commit carrying the same message as the `main` tip (`chore: add AI coding standards rule files and docs scaffolding`). It was not deleted after the bootstrap PR was merged.

**Why this exists:** The bootstrap PR merged the docs scaffolding commit; the feature branch was not pruned afterward.

**How to avoid:** Run `git remote prune origin` or delete the branch on GitHub if it causes confusion in branch listings.

---

*This file was AI-drafted as a starting point. Refine, correct, and expand. Add new traps as they bite — that's the compounding loop.*

