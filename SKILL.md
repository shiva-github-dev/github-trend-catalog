---
name: github-trend-catalog
description: Use when the user asks for trending GitHub repositories, open-source tool recommendations, "what's trending on GitHub", repo suggestions by role (tech, marketing, legal, operations), or wants to refresh/update their curated repo catalog from live sources like trendshift.io or GitHub trending.
---

# GitHub Trend Catalog

A living, persona-bifurcated catalog of GitHub repositories stored in a public repo and kept fresh by live fetches. The **data lives in the repo**, not in this prompt — always read `data/catalog.json` as the source of truth before answering.

- **Catalog repo:** `shiva-github-dev/github-trend-catalog`
- **Raw data URL:** `https://raw.githubusercontent.com/shiva-github-dev/github-trend-catalog/main/data/catalog.json`
- **Human-readable view:** `https://github.com/shiva-github-dev/github-trend-catalog` (README is regenerated on every update)

## Personas (bifurcation)

Every repo is filed under exactly one primary persona:

| Persona | Include | Signals |
|---|---|---|
| **tech** | Dev tools, AI/LLM, languages, frameworks, infra, security | topics like `ai-agent`, `ai-coding`, `llm-gateway` |
| **marketing** | SEO, social scheduling, analytics, content, email, growth | topics like `analytics`, `social-media`, `seo` |
| **legal** | Compliance, privacy, contracts, e-signatures, licensing, retention | topics like `privacy`, `compliance`, `e-signature` |
| **operations** | Automation, CRM, project mgmt, monitoring, support, finance/IT ops | topics like `automation`, `crm`, `project-management` |
| **generic** | Useful to anyone: privacy, converters, knowledge, self-hosting, utilities | `self-hosted`, `awesome-list`, converters |

If a repo spans roles, pick the persona the *user's question* implies; otherwise **generic**. A repo appears once in tables but may be mentioned under multiple personas in prose.

## When answering (read path)

1. Fetch `data/catalog.json` from the raw URL above (or the local clone if offline). Never invent star counts or descriptions — if the fetch fails, say so and fall back to whatever was previously shown in the session.
2. If the user asked for "fresh"/"trending"/"update" repos, also run the live refresh workflow below before answering.
3. Present results grouped by persona using the table format, leading with a one-line summary and ending with a freshness note (sources + date).

### Presentation format

```
### Tech
| Repo | ★ / Rank | What it does | Why it matters |
|---|---|---|---|
| owner/name | 1.2k | One-liner | Hook for this persona |
```

If the user named a single persona, only render that section but still classify everything internally.

## Live refresh workflow (write path)

Triggered by "update the catalog", "refresh", "what's new", or any request for current trends:

1. **Read** current `data/catalog.json` (raw URL or local).
2. **Fetch** live sources in order:
   - `https://trendshift.io/` (daily) + `https://trendshift.io/weekly` + `https://trendshift.io/topics`
   - `https://github.com/trending` (+ `/weekly`; language-specific if the user names one)
   - Optionally `https://news.ycombinator.com/` for community discussion signal
3. **Merge** into the catalog:
   - New repos: add with `origin: "trend"`, set `first_seen`/`last_seen` to today, classify persona.
   - Existing repos: bump `last_seen`, refresh `stars`; if a seed/trend repo drops entirely from live feeds for many refreshes, keep it (evergreen) unless the user asks to prune.
   - Skip: sponsor ads, jailbreak/NSFW repos, joke repos, abandoned projects. Prefer ≥~500 stars or a high trendshift rank; topic tags are strong classification signals.
   - **Sparse personas are normal** — legal OSS is thin; widen to privacy/compliance/licensing topics rather than padding with junk.
4. **Regenerate** `README.md` tables from the merged JSON (keep the same section order: Tech, Marketing, Legal, Operations, Generic; sort each table by stars desc). Update the "Last updated" date and per-table star counts.
5. **Commit to main** using the GitHub API (no clone needed). On PowerShell:

   ```powershell
   $msg = "refresh: <N> new repos, top mover <owner/repo>"
   foreach ($path in @("data/catalog.json", "README.md")) {
     $sha = gh api "repos/shiva-github-dev/github-trend-catalog/contents/$path" --jq .sha
     $b64 = [Convert]::ToBase64String([IO.File]::ReadAllBytes($path))
     gh api -X PUT "repos/shiva-github-dev/github-trend-catalog/contents/$path" `
       -f message=$msg -f content=$b64 -f sha=$sha -f branch=main | Out-Null
   }
   ```

   (On bash, use `base64 -w0` instead of the .NET call.) Confirm with the user before pushing if the diff is large (>20 new repos or any deletions).

## Refresh etiquette

- Never fabricate stars, ranks, or descriptions.
- Do not re-fetch the same source twice in one session unless the user explicitly asks.
- Keep a rolling in-session view of what's already been shown to avoid duplicate listings.
- The seed entries (`origin: "seed"`) are evergreen staples; live trends (`origin: "trend"`) are this week's heat. Present them together but you may badge trend entrants as "New" if `first_seen` is within ~7 days.
