# Architecture

## What This Is

`dbl-facts` is a single-page static audit report published at **dbl-facts.com** via GitHub Pages. It documents a campaign performance discrepancy between metrics DBL (a media-buying partner) reported and what Avantage actually tracked: DBL claimed 28,334 clicks over June 25–August 15 2025; Avantage's analytics recorded 2,359 sessions—a ~92% gap. The site exists to present this evidence to stakeholders. There is no backend, no build step, and no dynamic behavior beyond a Google Analytics page-view beacon.

---

## Components

This repo has one deliverable and no code architecture to speak of.

| Component | File | Role |
|---|---|---|
| Audit report | `index.html` | The entire site: HTML + embedded CSS + GA snippet |
| Custom domain | `CNAME` | Routes GitHub Pages to `dbl-facts.com` |
| Coding standards | `.cursor/rules/coding-standards.mdc` | Canonical rule file loaded by Cursor and Claude Code |
| AI context loaders | `CLAUDE.md`, `AGENTS.md` | Wire `coding-standards.mdc` into Claude Code / other AI agents |
| Docs scaffolding | `docs/{plans,code-reviews,knowledge}/README.md` | Templates for plans, reviews, and living knowledge docs |

---

## Domain Objects

The report centers on one comparison table; there are no data types or schemas in code.

| Metric | DBL Reported | Avantage Actual |
|---|---|---|
| Total Clicks | 28,334 | 2,359 |
| Accounts Created | 6 | 3 |
| Verified Accounts | 5 | 3 |
| Deposits | 1 | 0 |

**Key concepts** the document reasons about:
- **Attribution** — UTM-tagged links associating sessions to the DBL source
- **Discrepancy** — delta between claimed (partner) and measured (Avantage) metrics
- **Traffic quality** — broken redirects, missing UTMs, or synthetic/bot traffic as candidate explanations

---

## Important File Locations

| Path | Purpose |
|---|---|
| `index.html` | Entire site content; self-contained, no external assets except GA |
| `CNAME` | GitHub Pages domain binding (`dbl-facts.com`) |
| `.cursor/rules/coding-standards.mdc` | Coding standards (edit here, not per-repo copies) |
| `CLAUDE.md` | Imports `coding-standards.mdc` for Claude Code sessions |
| `AGENTS.md` | Equivalent pointer for other AI agents |
| `docs/knowledge/README.md` | Guidelines for ARCHITECTURE.md, GOTCHAS.md, and decision records |
| `docs/plans/README.md` | YAML-frontmatter plan workflow for AI-assisted work |
| `docs/code-reviews/README.md` | Artifact template for AI code reviews |

---

## Data / Request Flow

1. Push to `main` → GitHub Pages auto-deploys `index.html`.
2. Browser requests `dbl-facts.com` → GitHub Pages serves `index.html` directly (no CDN config in repo).
3. Page load fires `gtag('config', 'G-DWM7HTNPST')` — the only runtime side-effect.
4. No API calls, no state, no JavaScript beyond the GA snippet.

---

## What Does Not Exist Here

- No `package.json`, no bundler, no TypeScript, no React.
- No CI/CD workflow files (`.github/workflows/`).
- No environment variables or secrets.
- No backend or database.

The `coding-standards.mdc` file contains conventions for React, TypeScript, hooks, and testing—these apply if the repo ever grows beyond its current static form, not to the current codebase.

---

*This file was AI-drafted as a starting point. Refine, correct, and expand — the goal is a living document, not a one-shot deliverable.*

