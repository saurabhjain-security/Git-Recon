# GitRecon

**GitHub Organisation Member & Security Reconnaissance Tool**

> Enumerate every member, map every repository, surface every stale branch,
> and run a full security audit — all from a single command.

---

## Table of Contents

- [Overview](#overview)
- [What It Does](#what-it-does)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Authentication](#authentication)
- [Quick Start](#quick-start)
- [All CLI Flags](#all-cli-flags)
- [Features in Detail](#features-in-detail)
  - [Organisation Member Enumeration](#organisation-member-enumeration)
  - [Member Personal Repos](#member-personal-repos)
  - [Organisation Repos](#organisation-repos)
  - [Stale Branch Detection](#stale-branch-detection)
  - [Security Audit](#security-audit)
  - [No-Args Auto Mode](#no-args-auto-mode)
- [Output and Exports](#output-and-exports)
  - [Excel Report](#excel-report)
  - [CSV Export](#csv-export)
  - [JSON Export](#json-export)
- [Token Scopes Reference](#token-scopes-reference)
- [Usage Examples](#usage-examples)
- [Performance](#performance)
- [Troubleshooting](#troubleshooting)

---

## Overview

**GitRecon v4.0** is a Python command-line tool for security teams, engineering managers, and DevSecOps engineers who need a clear picture of their GitHub organisation's members, repositories, and security posture.

Run it with no arguments and it automatically discovers every organisation your GitHub account belongs to, audits all of them, and produces a colour-coded terminal report plus a styled Excel workbook — no configuration needed.

```
Author  : Saurabh Jain
Version : 4.0
```

---

## What It Does

GitRecon performs five tasks per organisation, in order:

| Step | What happens |
|------|-------------|
| **1. Members** | Lists every admin and regular member with full profile data (email, company, location, follower count, join date) |
| **2. Member Repos** | Enumerates all public repositories personally owned by each member (concurrently) |
| **3. Org Repos** | Lists every repository — public and private — owned by the organisation itself |
| **4. Stale Branches** | Checks every branch across every org repo and flags any with no commit for more than `--stale-days` days (default 180) |
| **5. Security Audit** | Runs 9 security checks against members and org repos; findings are colour-coded by severity and written to the Security Audit sheet |

All data is deduplicated across multiple orgs and exported to a single styled Excel workbook (or CSV / JSON).

---

## Prerequisites

- Python **3.9** or newer
- A GitHub account with access to the target organisation(s)
- Either the **GitHub CLI** (`gh`) installed and authenticated, or a **Personal Access Token** set as an environment variable

---

## Installation

**1. Clone or download the script**

```bash
git clone https://github.com/your-repo/gitrecon.git
cd gitrecon
```

**2. Install dependencies**

```bash
pip install -r requirements.txt
```

The `requirements.txt` contains:

```
requests>=2.31.0
pandas>=2.0.0
openpyxl>=3.1.0
rich>=13.7.0
```

**3. Verify the install**

```bash
python gitrecon.py --version
# GitRecon v4.0
```

---

## Authentication

GitRecon resolves your GitHub token in this order — the first one found wins:

### Option 1 — Environment variable (recommended for CI/CD)

```bash
export GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
# or
export GH_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
```

### Option 2 — GitHub CLI (recommended for local use)

```bash
# Install from https://cli.github.com
gh auth login
```

GitRecon will automatically call `gh auth token` to retrieve your token.

### Refreshing scopes

For the full feature set, your token needs specific scopes:

```bash
gh auth refresh -s read:org,admin:org,security_events
```

See [Token Scopes Reference](#token-scopes-reference) for exactly which scope unlocks which feature.

---

## Quick Start

```bash
# Scan all orgs your account belongs to (auto mode — runs full security audit)
python gitrecon.py

# Scan a specific org
python gitrecon.py --orgs acme-corp

# Scan two orgs and include the security audit
python gitrecon.py --orgs acme-corp devops-team --security

# Members only — skip all repo and branch enumeration
python gitrecon.py --orgs acme-corp --members-only

# Output as JSON instead of Excel
python gitrecon.py --orgs acme-corp --output-format json
```

---

## All CLI Flags

### Targets

| Flag | Description |
|------|-------------|
| `--orgs / -o ORG [ORG ...]` | One or more org logins to scan. **Omit entirely** to auto-discover all orgs for the authenticated user. |

### Scope

| Flag | Default | Description |
|------|---------|-------------|
| `--include-forks` | off | Include forked repositories in member repo results |
| `--members-only` | off | Fetch the member list only — skip repo enumeration, branch checks, and security audit |
| `--since YYYY-MM-DD` | none | Only return member repos pushed on or after this date |
| `--stale-days N` | `180` | Number of days of inactivity before a branch is considered stale |
| `--security` | off | Run the full security audit. Always enabled when `--orgs` is omitted. |

### Output

| Flag | Default | Description |
|------|---------|-------------|
| `--outdir DIR` | `github_recon_out` | Directory where the report file is written |
| `--output-format` | `xlsx` | Export format: `xlsx`, `csv`, or `json` |
| `--no-export` | off | Terminal output only — no files written to disk |

### Behaviour

| Flag | Default | Description |
|------|---------|-------------|
| `--workers N` | `8` | Number of concurrent threads for repo and branch fetching |
| `--timeout N` | `30` | HTTP timeout per API request in seconds |
| `--quiet` | off | Suppress the ASCII art banner (useful in CI pipelines) |
| `-v / --verbose` | off | Enable debug-level logging to stderr |
| `--version` | — | Print the version number and exit |

---

## Features in Detail

### Organisation Member Enumeration

GitRecon fetches every member of the target org(s) — both admins and regular members — with a full profile pull for each:

- GitHub login and display name
- Email address (if public)
- Company and location
- Number of public repos and followers
- Account creation date
- Admin vs member role

Member profiles are **cached in memory**, so if a user belongs to multiple orgs their profile is only fetched once, not once per org.

---

### Member Personal Repos

For each member, GitRecon enumerates all public repositories in their **personal namespace** (not org-owned). Results include:

- Repository name and URL
- Primary language
- Star and fork counts
- Last push date and creation date
- Whether the repo is a fork or archived

Fetching is done **concurrently** using a thread pool — all members in an org are queried in parallel, cutting total runtime by 5–8× compared to serial requests.

Use `--since YYYY-MM-DD` to filter to recently active repos. Use `--include-forks` to include forked repos (excluded by default to keep results focused on original work).

---

### Organisation Repos

Separately from member repos, GitRecon also lists every repository owned by **the organisation itself** — both public and private:

- Visibility (Public / Private)
- Language, stars, forks, and open issue count
- Default branch name
- Topics
- Last push date and creation date
- Archived and fork flags

This is a distinct data set from member repos. An org repo is owned by the org entity; a member repo is owned by an individual's GitHub account.

---

### Stale Branch Detection

For every org repo, GitRecon checks every branch and compares the last commit date against the current date. A branch is **stale** if it has had no commit activity for more than `--stale-days` days (default 180).

The Stale Branches sheet in the report includes:

| Column | Description |
|--------|-------------|
| Org | Organisation name |
| Repo | Repository the branch belongs to |
| Branch | Branch name |
| Last Commit | ISO date of the most recent commit |
| Days Inactive | How many days since the last commit |
| Last Author | Name of the last person who committed |
| Commit SHA | Abbreviated SHA of the last commit |
| Repo URL | Direct link to the repository |

Rows in the Excel report are colour-coded: **red** for branches inactive more than 365 days, **amber** for 181–365 days.

Branches are checked concurrently per repository, so even orgs with dozens of repos and hundreds of branches complete in a reasonable time.

---

### Security Audit

The security audit runs 9 checks and produces findings sorted by severity (Critical → High → Medium → Low → Info):

| Check | Severity | What it looks for |
|-------|----------|-------------------|
| **Member without 2FA** | Critical | Members with two-factor authentication disabled (requires `admin:org` scope) |
| **No Branch Protection** | High | Default branch has no protection rules — force-push and direct commits to main are allowed |
| **Dependabot Alerts Disabled** | High | Dependency vulnerability alerting is turned off on the repo |
| **No License** | Medium | No LICENSE file present — legal use of the code is undefined |
| **Potentially Sensitive Public Repo** | Medium | Public repo name contains keywords like `infra`, `deploy`, `prod`, `credentials`, `staging`, etc. |
| **High Admin-to-Member Ratio** | Medium | More than 50% of members are admins — increases blast radius of a compromised account |
| **No Description** | Low | Repo has no description — ownership and purpose are unclear |
| **No Topics** | Low | Public repo has no topics — difficult to classify or discover |
| **Archived Repo with Open Issues** | Low | Repo is archived but still has open issues that contributors cannot resolve |

Findings are printed as a colour-coded terminal table after each org scan and written to the **Security Audit** sheet in the Excel report.

The audit **automatically runs** in [no-args auto mode](#no-args-auto-mode). Pass `--security` to force it when targeting specific orgs with `--orgs`.

---

### No-Args Auto Mode

Running `python gitrecon.py` with no arguments triggers **auto mode**:

1. GitRecon calls `/user/orgs` to discover every organisation the authenticated user belongs to.
2. It scans **all of them** — members, member repos, org repos, stale branches, and security audit.
3. A summary table is printed at the end covering all orgs combined.

This is the fastest way to get a complete picture of your entire GitHub presence.

```bash
python gitrecon.py
```

If no organisations are found, a clear error is shown alongside the command to refresh the required scope.

---

## Output and Exports

### Excel Report

The default output is a single `.xlsx` file written to `github_recon_out/`. The filename is timestamped: `github_recon_2025-06-01T14-30.xlsx`.

The workbook has **6 sheets**:

| Sheet | Contents |
|-------|----------|
| **Summary** | One row per org — member count, repo counts, stale branch count, finding counts, elapsed time |
| **Members** | Deduplicated member list across all scanned orgs with full profile data |
| **Member Repos** | All personal repos of all members (deduplicated, personal namespace only) |
| **Org Repos** | All repos owned by the org(s) — public and private |
| **Stale Branches** | All branches inactive beyond the stale threshold, sorted by staleness descending |
| **Security Audit** | All security findings sorted by severity |

All sheets include:

- Dark blue header row with white bold text
- Freeze panes on row 1 so headers stay visible when scrolling
- Auto-filter enabled on all columns so you can sort and filter without leaving Excel
- Per-row colour coding relevant to the sheet (red/amber staleness on Stale Branches; red/amber/blue/green severity on Security Audit; grey for archived repos on Org Repos)
- Calibrated column widths to avoid truncation

### CSV Export

Pass `--output-format csv` to produce six separate `.csv` files in the output directory — one per sheet.

```bash
python gitrecon.py --orgs acme-corp --output-format csv
```

Files produced:
```
github_recon_out/
  github_recon_<timestamp>_summary.csv
  github_recon_<timestamp>_members.csv
  github_recon_<timestamp>_member_repos.csv
  github_recon_<timestamp>_org_repos.csv
  github_recon_<timestamp>_stale_branches.csv
  github_recon_<timestamp>_security.csv
```

### JSON Export

Pass `--output-format json` to produce a single `.json` file with all six data sets as top-level keys.

```bash
python gitrecon.py --orgs acme-corp --output-format json
```

Output structure:

```json
{
  "generated": "2025-06-01T14:30:00+00:00",
  "summary": [ ... ],
  "members": [ ... ],
  "member_repos": [ ... ],
  "org_repos": [ ... ],
  "stale_branches": [ ... ],
  "security_findings": [ ... ]
}
```

---

## Token Scopes Reference

| Scope | Required for |
|-------|-------------|
| `read:org` | Listing private members. Without this, only public members are returned and the member count will be incomplete. |
| `admin:org` | 2FA check — without this the 2FA finding is skipped and an Info note is written in the Security Audit sheet instead. |
| `security_events` | Dependabot vulnerability alert status check on each repo. |

**Check your current scopes:**

```bash
gh auth status
```

**Add missing scopes:**

```bash
gh auth refresh -s read:org,admin:org,security_events
```

When using a Personal Access Token, select the scopes above when generating the token at **GitHub → Settings → Developer settings → Personal access tokens**.

---

## Usage Examples

```bash
# Full audit of everything your account can see (no-args mode)
python gitrecon.py

# Scan a single org
python gitrecon.py --orgs acme-corp

# Scan multiple orgs in one run
python gitrecon.py --orgs acme-corp devops-team infra-team

# Run with security audit on named orgs
python gitrecon.py --orgs acme-corp --security

# Adjust the stale branch threshold to 90 days
python gitrecon.py --orgs acme-corp --stale-days 90

# Only show repos pushed since 1 Jan 2024
python gitrecon.py --orgs acme-corp --since 2024-01-01

# Include forked repos in member results
python gitrecon.py --orgs acme-corp --include-forks

# Members list only — no repos, no branches, no audit
python gitrecon.py --orgs acme-corp --members-only

# Export as JSON
python gitrecon.py --orgs acme-corp --output-format json

# Export as CSV files
python gitrecon.py --orgs acme-corp --output-format csv

# Write report to a custom directory
python gitrecon.py --orgs acme-corp --outdir /tmp/audit-results

# Terminal output only — no files written
python gitrecon.py --orgs acme-corp --no-export

# CI-friendly — suppress banner, token from env, JSON output
GITHUB_TOKEN=ghp_xxx python gitrecon.py --orgs acme-corp --output-format json --quiet

# Increase parallel workers for large orgs
python gitrecon.py --orgs acme-corp --workers 16

# Debug mode — print all API calls and errors to stderr
python gitrecon.py --orgs acme-corp -v
```

---

## Performance

GitRecon uses `concurrent.futures.ThreadPoolExecutor` for the two most API-intensive steps:

**Member repo fetching** — all members in an org are queried in parallel. For an org with 50 members this reduces the repo-fetch phase from ~50 serial API calls to roughly 6–7 parallel batches at the default `--workers 8`, cutting that phase down to roughly one-eighth of the serial time.

**Stale branch checking** — all org repos are checked in parallel. Each branch requires a commit-detail API call, so concurrency here makes a significant difference on orgs with many repos.

The default `--workers 8` is conservative and stays well within GitHub's rate limit. For large organisations you can safely raise it to `--workers 16` or `--workers 20`.

**Rate limits** are handled automatically. If the API returns a `429` or a rate-limit `403`, GitRecon reads the `X-RateLimit-Reset` header and sleeps until the window resets before retrying. Network errors retry with exponential back-off (2s → 4s → 8s → up to 60s maximum).

A pre-flight rate limit check is displayed at startup, showing remaining calls and the exact reset time. If fewer than 100 calls remain, a warning is printed before scanning begins so you can decide whether to proceed or wait.

---

## Troubleshooting

**`No organisations found for this account`**
Your token is missing `read:org` scope. Run `gh auth refresh -s read:org` and try again.

**`Auth failed (401)`**
The token is invalid or expired. Run `gh auth login` or set a fresh `GITHUB_TOKEN` environment variable.

**`2FA check skipped` in the Security Audit sheet**
This is expected when your token has `read:org` but not `admin:org`. Run `gh auth refresh -s admin:org` to enable the 2FA check.

**Stale branch detection is slow**
Each branch requires one extra API call for commit detail. For orgs with many large repos, raise `--workers` to `16` or higher to parallelise more aggressively, or use `--stale-days` with a smaller value to limit which branches are checked.

**Member repos not showing**
If `--include-forks` is not set, forked repos are excluded by default. Also verify that `--since` is not filtering out repos that were last pushed before your chosen date.

**The Excel file opens but the sheets are empty**
This can happen if `pandas` or `openpyxl` is outdated. Run `pip install --upgrade pandas openpyxl` and retry.

**`gh auth token timed out`**
The GitHub CLI took more than 10 seconds to respond. Try running `gh auth status` manually, or switch to the `GITHUB_TOKEN` environment variable approach instead.
