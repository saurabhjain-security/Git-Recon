<div align="center">

# 🔍 GitRecon

```
      ██████╗ ██╗████████╗    ██████╗ ███████╗ ██████╗ ██████╗ ███╗   ██╗
      ██╔════╝██║╚══██╔══╝    ██╔══██╗██╔════╝██╔════╝██╔═══██╗████╗  ██║
      ██║  ███╗██║   ██║      ██████╔╝█████╗  ██║     ██║   ██║██╔██╗ ██║
      ██║   ██║██║   ██║      ██╔══██╗██╔══╝  ██║     ██║   ██║██║╚██╗██║
      ╚██████╔╝██║   ██║      ██║  ██║███████╗╚██████╗╚██████╔╝██║ ╚████║
       ╚═════╝ ╚═╝   ╚═╝      ╚═╝  ╚═╝╚══════╝ ╚═════╝ ╚═════╝ ╚═╝  ╚═══╝
```

### **GitHub Organisation Member & Security Reconnaissance Tool**
*Enumerate every member, map every repository, detect stale branches, and run a full security audit — all from one command.*

<br>

![Python](https://img.shields.io/badge/Python-3.9%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![GitHub CLI](https://img.shields.io/badge/GitHub_CLI-Supported-181717?style=for-the-badge&logo=github&logoColor=white)
![Rich](https://img.shields.io/badge/Rich-Terminal_UI-7B2FBE?style=for-the-badge)
![Excel](https://img.shields.io/badge/Excel-Export-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white)
![JSON](https://img.shields.io/badge/JSON-Export-F7DF1E?style=for-the-badge&logo=json&logoColor=black)
![Version](https://img.shields.io/badge/Version-4.0-00C853?style=for-the-badge)
![Author](https://img.shields.io/badge/Author-Saurabh_Jain-FF6D00?style=for-the-badge)

<br>

</div>

---

## ✨ What is this?

> **GitRecon** is a single-command Python CLI tool that connects to GitHub, auto-discovers every organisation you belong to, and gives you a complete security and activity picture — members, repos, stale branches, and 9 security checks — all in one run.
>
> Results are printed as a colour-coded Rich terminal table and exported to a fully styled Excel workbook with 6 sheets.
>
> No config files. No tokens to paste. Just:

```bash
python gitrecon.py
```

---

## 🚀 Capabilities

<table>
<tr>
<td width="50%">

### 🔎 Member Intelligence
- 👤 **Full member profiles** — name, email, company, location
- 🛡️ **Role detection** — admin vs regular member
- 📦 **Personal repos** — all public repos per member
- ⚡ **Concurrent fetch** — all members queried in parallel
- 🔗 **Profile caching** — no duplicate API calls across orgs

</td>
<td width="50%">

### 🏢 Organisation Repos
- 🔓 **Public + Private** repos enumerated
- 🌿 **Default branch** name captured per repo
- ⭐ **Stars, forks, open issues** — all included
- 🏷️ **Topics** captured for every repo
- 📁 **Archived flag** tracked and colour-coded

</td>
</tr>
<tr>
<td width="50%">

### 🌿 Stale Branch Detection
- ⏰ **180-day default** threshold (configurable)
- 🔴 **Red** for branches dead > 365 days
- 🟡 **Amber** for branches dead 181–365 days
- 👤 **Last author** and commit SHA captured
- ⚡ **Concurrent** — all repos checked in parallel

</td>
<td width="50%">

### 🔐 Security Audit
- 🚨 **9 security checks** per organisation
- 🔑 **2FA detection** for every member
- 🛡️ **Branch protection** status per repo
- 🤖 **Dependabot alerts** enabled / disabled
- 📊 **Findings sorted** Critical → High → Medium → Low

</td>
</tr>
<tr>
<td width="50%">

### 🎨 Terminal UI
- 🎭 **ASCII art banner** with version and author
- 📊 **Live progress bar** with repo names scrolling
- 🌈 **Colour-coded findings** table per org
- 🃏 **Recon summary** table across all orgs
- ⏱️ **Runtime tracking** in the final panel

</td>
<td width="50%">

### 💾 Export
- 📄 **Excel workbook** — 6 styled sheets, freeze panes, auto-filter
- 📊 **CSV** — 6 separate files, one per sheet
- 🔷 **JSON** — single file, all data sets as top-level keys
- 🕐 **Timestamped filenames** — never overwrites
- 🎨 **Colour-coded rows** by severity / staleness in Excel

</td>
</tr>
</table>

---

## 📋 Requirements

| | Requirement | Version | Notes |
|---|---|---|---|
| 🐍 | Python | 3.9+ | [python.org](https://python.org) |
| 🐙 | GitHub CLI (`gh`) | Any | Only needed if not using `GITHUB_TOKEN` env var |
| 🌐 | Internet access | — | Reaches `api.github.com` |
| 🏢 | GitHub Org membership | At least one | Required for member and repo enumeration |

---

## ⚙️ Installation

### 1️⃣ Install GitHub CLI *(skip if using a token env var)*

```bash
# 🍎 macOS
brew install gh

# 🐧 Ubuntu / Debian
sudo apt install gh

# 🪟 Windows
winget install --id GitHub.cli
```

```bash
gh --version   # verify it's working
```

---

### 2️⃣ Authenticate

**Option A — GitHub CLI (recommended for local use)**
```bash
gh auth login
```
> 💡 Select: **GitHub.com → HTTPS → Login with a web browser**

**Option B — Environment variable (recommended for CI/CD)**
```bash
export GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
# or
export GH_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
```

For full features, refresh your token scopes:
```bash
gh auth refresh -s read:org,admin:org,security_events
```

---

### 3️⃣ Get the script

```bash
# Clone the repo
git clone https://github.com/your-username/gitrecon.git
cd gitrecon

# Or download just the file
curl -O https://raw.githubusercontent.com/your-username/gitrecon/main/gitrecon.py
```

---

### 4️⃣ Install Python dependencies

```bash
pip install -r requirements.txt
```

---

## 📦 requirements.txt

```text
# ┌─────────────────────────────────────────────────┐
# │       GitRecon — Python Dependencies            │
# │       pip install -r requirements.txt           │
# └─────────────────────────────────────────────────┘

rich>=13.7.0       # 🎨 Terminal UI — tables, panels, progress, colours
requests>=2.31.0   # 🌐 GitHub REST API v3 calls
pandas>=2.0.0      # 📊 DataFrame building and Excel / CSV export
openpyxl>=3.1.0    # 📄 Excel .xlsx writer engine (used by pandas)
```

---

## ▶️ How to Run

```bash
python gitrecon.py
```

> ✅ **No arguments needed.** GitRecon auto-discovers your orgs and runs a full security audit automatically.

---

### 🔄 What happens step by step

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  1  🎨  ASCII art banner printed  (v4.0 · Saurabh Jain)         │
│  2  🔑  Token resolved — env var first, gh CLI fallback         │
│  3  ✅  GitHub identity verified and scopes checked             │
│  4  📊  Rate limit pre-flight check displayed                   │
│  5  🏢  All organisations auto-discovered via /user/orgs        │
│                                                                 │   
│  6  🔁  For each organisation:                                  │
│         👤  All members fetched with full profile               │
│         📦  Member personal repos fetched in parallel           │
│         🏢  All org repos fetched (public + private)            │
│         🌿  Stale branches checked concurrently per repo        │
│         🔐  Security audit runs 9 checks per org                │
│                                                                 │
│  7  🌈  Colour-coded security findings table printed            │
│  8  💾  Excel / CSV / JSON report written to disk               │
│  9  📊  Recon summary table across all orgs printed             │
│  10 🃏  Final panel with totals and runtime displayed           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

### 🖥️ Terminal Output Preview

```
╔══════════════════════════════════════════════════════════════════════╗
║   GIT RECON   v4.0   |   GitHub Org Security & Repo Analyser         ║
║               Author: Saurabh Jain                                   ║
╚══════════════════════════════════════════════════════════════════════╝

  ✓  Authenticated as saurabh-jain  (Saurabh Jain)
  ✓  Rate limit: 4,982 / 5,000 remaining  (resets at 15:30:00)
  ✓  Auto-discovered 2 org(s):  acme-corp, devops-team

──────────────────── acme-corp ─────────────────────────────────────────

  ✓  Members — 24 found  (3 admins · 21 members)
  ⠋  acme-corp — api-service  ████████████░░░░  14/24  00:43

  ✓  Member repos — 187 across 24 members
  ✓  Org repos — 31 total  (28 public · 3 private)
  ✓  Stale branches — 8 found  (>180 days inactive)
  ✓  Security audit — 12 finding(s)  (2 Critical  4 High)

╭────────────────────────── Security Findings — acme-corp ─────────────╮
│  #  │ Repo                │ Check                   │ Severity │     │
├─────┼─────────────────────┼─────────────────────────┼──────────┤     │
│  1  │ — org-level         │ Member without 2FA      │ Critical │     │
│  2  │ api-service         │ No Branch Protection    │ High     │     │
│  3  │ infra-scripts       │ Potentially Sensitive   │ Medium   │     │
│  4  │ legacy-app          │ No Description          │ Low      │     │
╰─────────────────────────────────────────────────────────────────────-╯

──────────────────── Recon Summary ─────────────────────────────────────

  ┌──────────────────┬─────────┬──────────────┬───────────┬───────────┬──────────┐
  │ Org              │ Members │ Member Repos │ Org Repos │ Stale Br. │ Findings │
  ├──────────────────┼─────────┼──────────────┼───────────┼───────────┼──────────┤
  │ acme-corp        │      24 │          187 │        31 │         8 │       12 │
  │ devops-team      │      11 │           64 │        19 │         3 │        6 │
  └──────────────────┴─────────┴──────────────┴───────────┴───────────┴──────────┘

╔══════════════════════════════════════════════════════════════════════╗
║  ✓ Complete  ·  Orgs: 2  ·  Members: 35  ·  Findings: 18             ║
║  Member Repos: 251  ·  Org Repos: 50  ·  Runtime: 2m 14s             ║
╚══════════════════════════════════════════════════════════════════════╝

  ✓  Report → github_recon_out/github_recon_2025-06-01T14-30.xlsx
```

---

## 🎛️ CLI Reference

### All flags at a glance

```bash
python gitrecon.py [--orgs ORG [ORG ...]]
                   [--include-forks] [--members-only]
                   [--since YYYY-MM-DD] [--stale-days N]
                   [--security]
                   [--outdir DIR] [--output-format xlsx|csv|json] [--no-export]
                   [--workers N] [--timeout N] [--quiet] [-v]
```

### Targets

| Flag | Description |
|------|-------------|
| `--orgs acme devops` | Scan specific org(s). **Omit entirely** to auto-discover all your orgs. |

### Scope

| Flag | Default | Description |
|------|---------|-------------|
| `--include-forks` | off | Include forked repos in member results |
| `--members-only` | off | Members list only — skip repos, branches, and security audit |
| `--since 2024-01-01` | none | Only member repos pushed on or after this date |
| `--stale-days 90` | `180` | Days of inactivity before a branch is flagged stale |
| `--security` | off | Force security audit when using `--orgs` (always on in auto mode) |

### Output

| Flag | Default | Description |
|------|---------|-------------|
| `--outdir DIR` | `github_recon_out` | Directory to write the report file |
| `--output-format` | `xlsx` | `xlsx` · `csv` · `json` |
| `--no-export` | off | Terminal output only — no files written |

### Behaviour

| Flag | Default | Description |
|------|---------|-------------|
| `--workers N` | `8` | Concurrent threads for repo and branch fetching |
| `--timeout N` | `30` | HTTP timeout per API request (seconds) |
| `--quiet` | off | Suppress the ASCII art banner |
| `-v` | off | Debug logging to stderr |

---

## 🔐 Security Audit — All 9 Checks

| # | Check | Severity | What it catches |
|---|-------|----------|-----------------|
| 1 | **Member without 2FA** | 🔴 Critical | Any member with two-factor auth disabled |
| 2 | **No Branch Protection** | 🟠 High | Default branch allows force-push and direct commits |
| 3 | **Dependabot Alerts Disabled** | 🟠 High | Dependency CVE alerting is switched off |
| 4 | **Potentially Sensitive Public Repo** | 🟡 Medium | Public repo name contains words like `infra`, `prod`, `credentials`, `deploy` |
| 5 | **High Admin-to-Member Ratio** | 🟡 Medium | More than 50% of members are admins |
| 6 | **No License** | 🟡 Medium | No LICENSE file — legal use of the code is undefined |
| 7 | **No Description** | 🟢 Low | Repo ownership and purpose are unclear |
| 8 | **No Topics** | 🟢 Low | Public repo is unclassified and hard to discover |
| 9 | **Archived Repo with Open Issues** | 🟢 Low | Repo is frozen but issues are still open |

> 🔑 The **2FA check** requires `admin:org` scope. Without it, an `Info` note is written instead.
> Run `gh auth refresh -s admin:org` to enable it.

---

## 💾 Excel Report — 6 Sheets

Every run creates a **timestamped Excel file** so old results are never overwritten:

```
github_recon_out/github_recon_2025-06-01T14-30.xlsx
```

| Sheet | Contents |
|-------|----------|
| 📋 **Summary** | One row per org — member count, repo counts, stale branch count, findings, elapsed time |
| 👤 **Members** | Full deduplicated member list — login, name, role, email, company, location, followers |
| 📦 **Member Repos** | All personal repos for all members — language, stars, forks, last push |
| 🏢 **Org Repos** | Every org repo — visibility, language, stars, forks, issues, default branch, topics |
| 🌿 **Stale Branches** | All stale branches — repo, days inactive, last author, commit SHA |
| 🔐 **Security Audit** | All findings — org, repo, check name, severity, detail |

All sheets have **dark blue headers**, **freeze panes**, **auto-filter**, and **colour-coded rows**.

---

## 🌈 Colour Guide

### Terminal

| Colour | Meaning |
|--------|---------|
| 🟢 **Bright Green** | Pass / found / active / success |
| 🟡 **Yellow** | Warning / admin role / weak |
| 🔴 **Red** | Critical / fail / high severity |
| 🔵 **Bright Cyan** | Labels, counts, section headers |
| ⬜ **Dim White** | Secondary info / not applicable |

### Excel — Security Audit Sheet

| Row colour | Severity |
|------------|---------|
| 🔴 Red tint | Critical |
| 🟡 Amber tint | High |
| 🔵 Blue tint | Medium |
| 🟢 Green tint | Low |
| ⬜ Grey | Info |

### Excel — Stale Branches Sheet

| Row colour | Staleness |
|------------|-----------|
| 🔴 Red tint | Inactive > 365 days |
| 🟡 Amber tint | Inactive 181–365 days |

---

## 💡 Usage Examples

```bash
# Full auto-audit — discover all orgs + security audit (no flags needed)
python gitrecon.py

# Scan a single org
python gitrecon.py --orgs acme-corp

# Scan multiple orgs in one run
python gitrecon.py --orgs acme-corp devops-team infra-team

# Security audit on named orgs
python gitrecon.py --orgs acme-corp --security

# Adjust stale branch threshold to 90 days
python gitrecon.py --orgs acme-corp --stale-days 90

# Only repos pushed since 1 Jan 2024
python gitrecon.py --orgs acme-corp --since 2024-01-01

# Include forked repos in member results
python gitrecon.py --orgs acme-corp --include-forks

# Members list only — fastest mode, no repos or branches
python gitrecon.py --orgs acme-corp --members-only

# Export as JSON for pipeline integration
python gitrecon.py --orgs acme-corp --output-format json

# Export as CSV files
python gitrecon.py --orgs acme-corp --output-format csv

# Terminal only — no files written
python gitrecon.py --orgs acme-corp --no-export

# CI pipeline — quiet mode, token from env
GITHUB_TOKEN=ghp_xxx python gitrecon.py --orgs acme-corp --quiet --output-format json

# Boost workers for large orgs
python gitrecon.py --orgs acme-corp --workers 16

# Debug mode
python gitrecon.py --orgs acme-corp -v
```

---

## 🔑 Token Scopes

| Scope | Required for |
|-------|-------------|
| `read:org` | Private member listing — without this only public members are returned |
| `admin:org` | 2FA check — without this the check is skipped |
| `security_events` | Dependabot alert status check |

```bash
# Check current scopes
gh auth status

# Add all required scopes
gh auth refresh -s read:org,admin:org,security_events
```

---

## 📁 Project Structure

```
gitrecon/
│
├── 🐍  gitrecon.py        ← Main script (single file, zero config)
├── 📦  requirements.txt   ← Python dependencies
└── 📖  README.md          ← You are here
```

---

## 📜 Changelog

| Version | What changed |
|---------|-------------|
| **4.0** | 🔐 Security audit (9 checks) · 🌿 Stale branch detection · 🏢 Org repos sheet · 🔑 2FA check · 🤖 Dependabot check |
| **3.0** | ⚡ Concurrent repo fetching · 🔑 GITHUB_TOKEN env var support · 🛡️ Profile caching |
| **2.0** | 📊 Excel export with 6 sheets · 🗂️ CSV and JSON output · 🌿 Member personal repos |
| **1.0** | 🚀 Initial release — member listing and org discovery |

---

## 🛠️ Troubleshooting

<details>
<summary>❌ &nbsp;<strong>gh CLI not found</strong></summary>
<br>

Install the GitHub CLI from [cli.github.com](https://cli.github.com) then run `gh auth login`.

```bash
brew install gh      # macOS
sudo apt install gh  # Ubuntu
```

Or skip the CLI entirely by setting `GITHUB_TOKEN` as an environment variable.

</details>

<details>
<summary>❌ &nbsp;<strong>No organisations found for this account</strong></summary>
<br>

Your token is missing `read:org` scope.

```bash
gh auth refresh -s read:org
```

Then re-run GitRecon.

</details>

<details>
<summary>❌ &nbsp;<strong>Auth failed (401)</strong></summary>
<br>

The token is invalid or expired.

```bash
gh auth login     # re-authenticate via CLI
# or
export GITHUB_TOKEN=ghp_<new-token>
```

</details>

<details>
<summary>🔍 &nbsp;<strong>2FA check skipped in Security Audit</strong></summary>
<br>

Normal — your token has `read:org` but not `admin:org`. Add the scope:

```bash
gh auth refresh -s admin:org
```

</details>

<details>
<summary>⏳ &nbsp;<strong>Script pauses with "Rate limit — sleeping Xs"</strong></summary>
<br>

Completely normal for large orgs. GitHub allows **5,000 API requests/hour** for authenticated users. GitRecon reads the reset time from the `X-RateLimit-Reset` header and waits automatically — no action needed.

</details>

<details>
<summary>🐌 &nbsp;<strong>Stale branch check is very slow</strong></summary>
<br>

Each branch requires one commit-detail API call. For orgs with many repos, raise `--workers`:

```bash
python gitrecon.py --orgs acme-corp --workers 16
```

</details>

<details>
<summary>❌ &nbsp;<strong>ModuleNotFoundError</strong></summary>
<br>

```bash
pip install -r requirements.txt

# If a specific package is still missing
pip install rich requests pandas openpyxl
```

</details>

---

<div align="center">

---

*Made with &nbsp;🐍 Python &nbsp;·&nbsp; 🎨 Rich &nbsp;·&nbsp; 🐙 GitHub REST API v3*

**Author: Saurabh Jain &nbsp;·&nbsp; v4.0**

---

</div>
