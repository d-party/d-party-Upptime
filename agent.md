# d-party-Upptime Agent Guide

## Project Overview

d-party-Upptime is the uptime monitoring and public status page for the d-party ecosystem. It continuously checks the availability and response times of three services and publishes the results to a GitHub Pages status site.

Status page: https://d-party.github.io/d-party-Upptime/

## Monitored Services

| Service | URL |
|---------|-----|
| d-party | https://d-party.net |
| d-party Chrome ウェブストア | https://chrome.google.com/webstore/detail/d-party/ibmlcfpijglpfbfgaleaeooebgdgcbpc |
| dアニメストア | https://animestore.docomo.ne.jp/animestore/tp_pc |

## Tech Stack

- **Upptime v1.41.5** — open-source uptime monitor that runs on GitHub Actions + GitHub Pages
- **GitHub Actions** — all automation; no external servers required
- **GitHub Pages** — hosts the generated Sapper static site
- **YAML / JSON / Markdown** — configuration and data storage

## Repository Layout

```
d-party-Upptime/
├── .upptimerc.yml        # PRIMARY config — edit this to change monitored sites
├── .github/
│   └── workflows/        # Auto-generated from .upptimerc.yml — do not edit directly
│       ├── uptime.yml         # Checks uptime every 5 minutes
│       ├── response-time.yml  # Calculates response time stats (daily 23:00)
│       ├── graphs.yml         # Generates PNG graphs (daily 00:00)
│       ├── summary.yml        # Updates README summary (daily 00:00)
│       ├── site.yml           # Builds & deploys GitHub Pages (daily 01:00)
│       ├── updates.yml        # Auto-updates Upptime (daily 03:00)
│       └── setup.yml          # Regenerates workflows on .upptimerc.yml change
├── history/              # YAML files with last check results per service
├── api/                  # JSON files with uptime/response-time metrics
├── graphs/               # Auto-generated PNG charts (day/week/month/year)
└── README.md             # Auto-generated status summary
```

## How It Works

```
Cron trigger (GitHub Actions)
  → Upptime CLI runs HTTP HEAD/GET requests
  → Results stored in /history/*.yml
  → /api/*.json updated with metrics
  → /graphs/*.png regenerated
  → README.md updated
  → Sapper static site built and deployed to GitHub Pages
```

All data is committed back to the repository by the Upptime bot. Commits follow the pattern `[skip ci]` to avoid workflow recursion.

## Configuration

All monitoring configuration lives in `.upptimerc.yml`. To change monitored sites, update that file — the `setup.yml` workflow will automatically regenerate the GitHub Actions workflows.

```yaml
owner: d-party
repo: d-party-Upptime

sites:
  - name: <display name>
    url: <URL to monitor>

status-website:
  baseUrl: /d-party-Upptime
  name: Upptime
```

**Do not manually edit files under `.github/workflows/`** — they are auto-generated from `.upptimerc.yml`.

## Automation Schedule

| Workflow | Schedule | Purpose |
|----------|----------|---------|
| `uptime.yml` | Every 5 minutes | Health check each service |
| `response-time.yml` | Daily 23:00 JST | Compute response time stats |
| `graphs.yml` | Daily 00:00 JST | Generate chart PNGs |
| `summary.yml` | Daily 00:00 JST | Update README |
| `site.yml` | Daily 01:00 JST | Build and deploy status page |
| `updates.yml` | Daily 03:00 JST | Auto-update Upptime framework |

## Data Format

Uptime and response-time metrics are stored as Shield.io-compatible JSON files under `/api/<service-slug>/`:

```
uptime.json / uptime-day.json / uptime-week.json / uptime-month.json / uptime-year.json
response-time.json / response-time-day.json / ...
```

Aggregated summary: `history/summary.json`

## Conventions

- All workflow files are auto-generated — **never edit them directly**.
- To add or remove a monitored site, only edit `.upptimerc.yml`.
- Upptime bot commits use `[skip ci]` and `[upptime]` tags in commit messages.
- Historical data and graphs accumulate in git history over time.
- No database or external infrastructure — fully GitHub-native.
