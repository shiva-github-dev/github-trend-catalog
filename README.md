# GitHub Trend Catalog

A living, open catalog of GitHub repositories, bifurcated by **who it's for**: tech, marketing, legal, operations, or everyone (generic). Designed to be read and updated by LLM assistants (opencode, Claude Code, etc.) so recommendations stay fresh instead of being frozen at training time.

- **Data (source of truth):** [`data/catalog.json`](data/catalog.json)
- **Skill instructions:** [`SKILL.md`](SKILL.md) — install into `~/.config/opencode/skills/github-trend-catalog/` (or `.claude/skills/`)
- **Last updated:** 2026-10-28

## How it works

1. An LLM skill fetches live trending repos from [trendshift.io](https://trendshift.io/) (daily + weekly + topics), [GitHub Trending](https://github.com/trending), and optionally Hacker News.
2. Each repo is classified into one primary persona using the taxonomy below.
3. The merged catalog is committed back to this repo (`data/catalog.json` + this README), so state accumulates across sessions and machines.

## Personas

| Persona | What belongs here |
|---|---|
| **tech** | Dev tools, AI/LLM, languages, frameworks, infra, security |
| **marketing** | SEO, social scheduling, analytics, content, email, growth |
| **legal** | Compliance, privacy, contracts, e-signatures, licensing, retention |
| **operations** | Automation, CRM, project mgmt, monitoring, support, finance/IT ops |
| **generic** | Useful to anyone regardless of job: privacy, converters, knowledge, self-hosting |

## Catalog

### Tech

| Repo | ★ | What it does | Why it matters |
|---|---:|---|---|
| [sindresorhus/awesome](https://github.com/sindresorhus/awesome) | 526,155 | Awesome lists about all kinds of topics | Meta-index for discovering niche tool lists |
| [public-apis/public-apis](https://github.com/public-apis/public-apis) | 492,938 | Collective list of free APIs | Fuel for prototypes and integrations |
| [donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer) | 375,920 | Large-scale system design + interview prep | Architecture reference for engineers |
| [microsoft/vscode](https://github.com/microsoft/vscode) | 194,386 | Visual Studio Code | The default code editor for most developers |
| [huggingface/transformers](https://github.com/huggingface/transformers) | 168,278 | Model-definition framework for SOTA ML models | The de-facto library for running LLMs |
| [kubernetes/kubernetes](https://github.com/kubernetes/kubernetes) | 128,581 | Container scheduling and management | Industry-standard infra orchestration |

### Marketing

| Repo | ★ | What it does | Why it matters |
|---|---:|---|---|
| [TryGhost/Ghost](https://github.com/TryGhost/Ghost) | 55,632 | Publishing, memberships, newsletters | Own your content + list without SaaS lock-in |
| [calcom/cal.com](https://github.com/calcom/cal.com) | 49,478 | Scheduling infrastructure | Booking links for demos and lead capture |
| [gitroomhq/postiz-app](https://github.com/gitroomhq/postiz-app) | 39,902 | Agentic social media scheduling | Self-hosted Buffer/Later alternative |
| [umami-software/umami](https://github.com/umami-software/umami) | 39,750 | Privacy-first web/product analytics | GA alternative for growth measurement |

### Legal

| Repo | ★ | What it does | Why it matters |
|---|---:|---|---|
| [paperless-ngx/paperless-ngx](https://github.com/paperless-ngx/paperless-ngx) | 47,201 | Document management: scan, index, archive | Contract/archive retention with OCR search |
| [docusealco/docuseal](https://github.com/docusealco/docuseal) | 18,898 | Open-source DocuSign alternative | Self-hosted e-signatures for agreements |

> Legal/compliance OSS is genuinely sparse. Live refreshes widen the net via trendshift topics (`privacy`, `compliance`, `license`) rather than padding with junk.

### Operations

| Repo | ★ | What it does | Why it matters |
|---|---:|---|---|
| [n8n-io/n8n](https://github.com/n8n-io/n8n) | 208,881 | Workflow automation with native AI, 400+ integrations | The open-source Zapier/Make |
| [grafana/grafana](https://github.com/grafana/grafana) | 77,569 | Observability and data visualization | Metrics/logs/traces dashboards + alerting |
| [makeplane/plane](https://github.com/makeplane/plane) | 61,615 | Open-source Jira/Linear alternative | Project tracking without per-seat pricing |
| [twentyhq/twenty](https://github.com/twentyhq/twenty) | 60,000 | Open-source Salesforce alternative | Sales/CS CRM without enterprise pricing |
| [metabase/metabase](https://github.com/metabase/metabase) | 49,892 | Open-source BI and embedded analytics | Self-serve BI for non-SQL operators |

### Generic (everyone)

| Repo | ★ | What it does | Why it matters |
|---|---:|---|---|
| [awesome-selfhosted/awesome-selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted) | 331,343 | List of self-hostable free software | Master index of self-hostable services |
| [EbookFoundation/free-programming-books](https://github.com/EbookFoundation/free-programming-books) | 400,383 | Freely available programming books | Free upskilling library for any tech-touching role |
| [jlevy/the-art-of-command-line](https://github.com/jlevy/the-art-of-command-line) | 162,617 | Master the command line in one page | One-page CLI reference for technical operators |

## Updating

Ask your LLM assistant:

> "Update the GitHub trend catalog"

It will fetch live sources, merge into `data/catalog.json` (new entrants, star refreshes, big movers), regenerate this README, and commit straight to `main`.

## Field reference (`data/catalog.json`)

| Field | Meaning |
|---|---|
| `full_name` | `owner/repo` (dedup key) |
| `persona` | `tech` \| `marketing` \| `legal` \| `operations` \| `generic` |
| `description` | One-liner (from GitHub or trendshift) |
| `url` | Canonical GitHub URL |
| `stars` | Star count at last refresh |
| `topics` | GitHub/trendshift topic tags |
| `why` | One-clause hook for the persona |
| `first_seen` / `last_seen` | ISO dates — used to detect new entrants vs. decay |
| `origin` | `seed` (curated baseline) or `trend` (came from a live refresh) |
