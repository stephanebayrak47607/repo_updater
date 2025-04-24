<p align="center">
  <img src="https://img.shields.io/badge/version-1.2.0-blue?style=for-the-badge" alt="Version" />
  <img src="https://img.shields.io/badge/platform-macOS%20%7C%20Linux-blueviolet?style=for-the-badge" alt="Platform" />
  <img src="https://img.shields.io/badge/shell-Bash%204.0+-purple?style=for-the-badge" alt="Shell" />
  <img src="https://img.shields.io/badge/license-MIT-green?style=for-the-badge" alt="License" />
</p>

<h1 align="center">ru</h1>
<h3 align="center">Repo Updater</h3>

<div align="center">
  <img src="ru_illustration.webp" alt="ru - Repo Updater: Keep all your GitHub repositories synchronized with a single command">
</div>

<p align="center">
  <strong>A beautiful, automation-friendly CLI for synchronizing GitHub repositories</strong>
</p>

<p align="center">
  Keep dozens (or hundreds) of repos in sync with a single command.<br/>
  Clone missing repos, pull updates, detect conflicts, and get actionable resolution commands.
</p>

<p align="center">
  <em>Pure Bash with no string parsing of git output. Uses git plumbing for reliable status detection.<br/>
  Meaningful exit codes for CI. JSON output for scripting. Non-interactive mode for automation.</em>
</p>

---

<p align="center">

```bash
curl -fsSL "https://raw.githubusercontent.com/Dicklesworthstone/repo_updater/main/install.sh?ru_cb=$(date +%s)" | bash
# You can omit the `?ru_cb=...` once installed; it's just a cache-buster for the installer fetch.
```

**Or via Homebrew (macOS/Linux):**

```bash
brew install dicklesworthstone/tap/ru
```

</p>

---

## 🤖 Agent Quickstart (JSON/TOON)

**Use structured output in agent contexts.** stdout = data, stderr = diagnostics, exit 0 = success.

```bash
# Sync all repos (machine-readable)
ru sync --json

# Preview without changes
ru sync --dry-run --json

# Status only (no fetch)
ru status --no-fetch --json

# Machine-readable CLI documentation
ru robot-docs                   # All topics as JSON
ru robot-docs commands          # Command/flag reference
ru robot-docs quickstart        # Getting started guide
ru robot-docs examples          # Usage examples
ru robot-docs exit-codes        # Exit code reference
ru robot-docs formats           # Output format details
ru robot-docs schemas           # JSON schemas for command outputs
ru --schema                     # Shortcut for ru robot-docs schemas
```

## 🤖 Ready-made Blurb for AI Agents

> [!IMPORTANT]
> **Copy the blurb below to your project's `AGENTS.md`, `CLAUDE.md`, or `.cursorrules` file for AI agent integration with ru.**

````markdown
## ru Quick Reference for AI Agents

Syncs GitHub repos to local projects directory (clone missing, pull updates, detect conflicts).

```bash
ru sync                    # Sync all repos
ru sync --dry-run          # Preview only
ru sync -j4 --autostash    # Parallel + auto-stash
ru status --no-fetch       # Quick local status
ru list --paths            # Repo paths (stdout)
```

**Automation:** `--non-interactive --json` (structured→stdout, human→stderr).

**Exit:** 0=ok | 1=partial | 2=conflicts | 3=system | 4=bad args | 5=interrupted (`--resume`)

**Critical:**
- Never create worktrees/clones in projects dir → use `/tmp/`
- Never parse human output → use `--json`
````

---

## 🎯 The Primary Use Case: Keeping Your Projects Directory in Sync

**The scenario:** You work across multiple machines, contribute to dozens of repositories, and your local `/data/projects` directory needs to stay synchronized with GitHub. Manually running `git pull` in each directory is tedious and error-prone.

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Your Repos     │     │  repos.d/       │     │       ru        │
│  on GitHub      │────▶│  public.txt     │────▶│     sync        │
│  (47 repos)     │     │  private.txt    │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                                                        │
         ┌──────────────────────────────────────────────┤
         ▼                    ▼                         ▼
┌─────────────────┐  ┌─────────────────┐     ┌─────────────────┐
│  Clone Missing  │  │  Pull Updates   │     │  Report Status  │
│  (8 new repos)  │  │  (34 updated)   │     │  (2 conflicts)  │
└─────────────────┘  └─────────────────┘     └─────────────────┘
                                                        │
                                                        ▼
                                             ┌─────────────────┐
                                             │  Actionable     │
                                             │  Resolution     │
                                             │  Commands       │
                                             └─────────────────┘
```

**The workflow:**

1. **Configure once** — Add repos to `~/.config/ru/repos.d/public.txt`
2. **Run `ru sync`** — Everything happens automatically
3. **Review conflicts** — Get copy-paste commands to resolve issues

```bash
# On any machine, sync all 47 of your repos
ru sync

# Output:
# → Processing 1/47: mcp_agent_mail
#   ├─ Path: /data/projects/mcp_agent_mail
#   ├─ Status: behind (0 ahead, 3 behind)
#   └─ Result: ✓ Updated (2s)
#
# ╭─────────────────────────────────────────────────────────────╮
# │                    📊 Sync Summary                          │
# │  ✅ Cloned:     8 repos                                     │
# │  ✅ Updated:   34 repos                                     │
# │  ⏭️  Current:    3 repos                                    │
# │  ⚠️  Conflicts: 2 repos (need attention)                    │
# │  Total: 47 repos processed in 2m 34s                        │
# ╰─────────────────────────────────────────────────────────────╯
```

**Comparison:**

| Without ru | With ru |
|------------|---------|
| `cd` into each of 47 directories and `git pull` | One command syncs everything |
| Forget which repos exist locally vs remotely | Automatically clones missing repos |
| Wonder if your local branch diverged | Clear status: behind, ahead, diverged, conflict |
| Google the right git commands for conflicts | Copy-paste resolution commands provided |
| Manual process breaks when network fails | Meaningful exit codes for scripting |

---

## Table of Contents

- [Ready-made Blurb for AI Agents](#-ready-made-blurb-for-ai-agents)
- [The Primary Use Case](#-the-primary-use-case-keeping-your-projects-directory-in-sync)
- [Why ru Exists](#-why-ru-exists)
- [Highlights](#-highlights)
- [Quickstart](#-quickstart)
- [Commands](#-commands)
- [Configuration](#-configuration)
- [Repo List Format](#-repo-list-format)
  - [Path Collision Detection](#path-collision-detection)
- [Sync Workflow](#-sync-workflow)
  - [Parallel Sync](#parallel-sync)
  - [Network Timeout Tuning](#network-timeout-tuning)
  - [Resuming Interrupted Syncs](#resuming-interrupted-syncs)
- [Git Status Detection](#-git-status-detection)
- [Conflict Resolution](#-conflict-resolution)
- [Managing Orphan Repositories](#-managing-orphan-repositories)
- [Output Modes](#-output-modes)
- [AI-Assisted Code Review](#-ai-assisted-code-review)
  - [Priority Scoring Algorithm](#priority-scoring-algorithm)
  - [Session Drivers](#session-drivers)
    - [ntm Integration](#ntm-named-tmux-manager-integration)
  - [Claude Code Integration](#claude-code-integration)
  - [Git Worktree Isolation](#git-worktree-isolation)
  - [GitHub Actions Execution](#github-actions-execution)
  - [Review Policies](#review-policies)
  - [GraphQL Batch Querying](#graphql-batch-querying)
  - [Rate-Limit Governor](#rate-limit-governor)
  - [Global Backoff Coordination](#global-backoff-coordination)
  - [Quality Gates](#quality-gates)
  - [Session Health Monitoring](#session-health-monitoring)
- [Agent-Driven Sweep](#-agent-driven-sweep)
  - [Prerequisites](#agent-sweep-prerequisites)
  - [Basic Usage](#basic-usage)
  - [Preflight Checks](#preflight-checks)
  - [Security Guardrails](#security-guardrails)
  - [Per-Repository Configuration](#per-repository-configuration)
  - [State Management](#state-management)
  - [Troubleshooting Agent Sweep](#troubleshooting-agent-sweep)
- [AI Sync](#-ai-sync)
- [Dependency Updates](#-dependency-updates)
  - [Supported Package Managers](#supported-package-managers)
  - [Update Workflow](#update-workflow)
  - [Filtering Dependencies](#filtering-dependencies)
- [Exit Codes](#-exit-codes)
- [Architecture](#-architecture)
  - [NDJSON Results Logging](#ndjson-results-logging)
  - [Portable Locking](#portable-locking)
  - [Work-Stealing Queue](#work-stealing-queue)
  - [Path Security Validation](#path-security-validation)
  - [Retry with Exponential Backoff](#retry-with-exponential-backoff)
- [Design Principles](#-design-principles)
- [File Denylist System](#️-file-denylist-system)
- [Testing](#-testing)
- [Troubleshooting](#-troubleshooting)
- [Environment Variables](#-environment-variables)
- [Dependencies](#-dependencies)
- [Security & Privacy](#-security--privacy)
- [Uninstallation](#-uninstallation)
- [Contributing](#-contributing)
- [License](#-license)

---

## 💡 Why ru Exists

Managing a large collection of GitHub repositories presents unique challenges:

| Problem | Why It's Hard | How ru Solves It |
|---------|---------------|------------------|
| **Too many repos to update manually** | 47 repos × `cd` + `git pull` = wasted time | One command syncs everything |
| **New repos on GitHub not cloned locally** | No way to detect missing repos automatically | Compares list to local directory, clones missing |
| **Diverged branches are confusing** | "Already up to date" vs actual divergence | Git plumbing detects ahead/behind/diverged states |
| **Dirty working trees block pulls** | Errors with uncommitted changes | Clear warnings + resolution commands |
| **Different clone strategies per repo** | Some need SSH, some HTTPS, some specific branches | Flexible repo spec syntax (`repo@branch`) |
| **Need automation in CI** | Interactive prompts break scripts | `--non-interactive` mode, JSON output, exit codes |
| **Public vs private repos** | Different auth requirements | Separate lists, automatic `gh` CLI integration |

ru brings order to your projects directory. It's the tool you wish existed every time you've thought "I should really update all my repos."

---

## ✨ Highlights

<table>
<tr>
<td width="50%">

### Zero-Setup Installation
One-liner installer handles everything:
- Checksum verification by default
- Auto-installs to `~/.local/bin`
- Detects missing `gh` CLI and prompts
- XDG-compliant configuration

</td>
<td width="50%">

### Automation-Grade Design
Built for scripting and CI from day one:
- Meaningful exit codes (0-5)
- `--json` mode for structured output
- `--non-interactive` for unattended runs
- `--dry-run` to preview changes

</td>
</tr>
<tr>
<td width="50%">

### Git Plumbing, Not String Parsing
Reliable status detection:
- `git rev-list --left-right` for ahead/behind
- `git status --porcelain` for dirty detection
- Never parses "Already up to date" text
- Locale-independent, version-safe

</td>
<td width="50%">

### Beautiful Terminal UI
Powered by [gum](https://github.com/charmbracelet/gum) with ANSI fallbacks:
- Styled progress indicators
- Boxed summary reports
- Color-coded status (green/yellow/red)
- Works without gum installed

</td>
</tr>
<tr>
<td width="50%">

### Subcommand Architecture
Clean CLI with focused commands:
- `sync` — Clone and pull repos
- `status` — Show status without changes
- `init` — Create configuration
- `add` — Add repo to list
- `doctor` — System diagnostics

</td>
<td width="50%">

### Conflict Resolution Help
Actionable commands for every issue:
- Dirty working tree? Stash/commit/discard options
- Diverged branches? Rebase/merge/push options
- Auth failed? Token/login instructions
- Copy-paste ready commands

</td>
</tr>
<tr>
<td width="50%">

### Parallel & Resumable Syncs
Efficient handling of large repo collections:
- `--parallel N` for concurrent operations
- Worker pool with portable coordination
- `--resume` to continue interrupted syncs
- State tracking for reliable restarts

</td>
<td width="50%">

### Orphan Repository Management
Keep your projects directory clean:
- `ru prune` detects orphan repositories
- `--archive` for non-destructive cleanup
- Layout-aware directory scanning
- Respects custom-named repos in config

</td>
</tr>
</table>

---

## ⚡ Quickstart

### Installation

#### Recommended: Homebrew (macOS/Linux)

```bash
brew install dicklesworthstone/tap/ru
```

This method provides:
- Automatic updates via `brew upgrade`
- Dependency management
- Easy uninstall via `brew uninstall`

#### Windows: WSL2

`ru` is a bash script, so there is no native Windows package (no Scoop or winget
manifest). On Windows, install it inside WSL2 using either the Homebrew command
above or the install script below.

#### Alternative: Install Script

```bash
curl -fsSL "https://raw.githubusercontent.com/Dicklesworthstone/repo_updater/main/install.sh?ru_cb=$(date +%s)" | bash
```

**If you suspect CDN caching (stale installer):**
```bash
curl -fsSL "https://raw.githubusercontent.com/Dicklesworthstone/repo_updater/main/install.sh?ru_cb=$(date +%s).$$" | bash
```

**How cache-busting works:**

GitHub's raw content is served through a CDN that caches aggressively. The `?ru_cb=...` query parameter ensures fresh content:

| Technique | Cache Bypass |
|-----------|-------------|
| `$(date +%s)` | Unix timestamp (changes every second) |
| `$(date +%s).$$` | Timestamp + PID (unique per invocation) |
| `RU_CACHE_BUST_TOKEN=xyz` | Custom token override |

**Installer self-refresh mechanism:**

When the installer runs via `curl | bash`, it performs automatic self-refresh to ensure you always get the latest version:

```
┌─────────────────────────────────────────────────────────────────┐
│                   Installer Self-Refresh                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │  Detect piped   │◀─────── stdin is not a TTY?
                    │  execution      │
                    └────────┬────────┘
                             │ yes
                             ▼
                    ┌─────────────────┐
                    │  Download fresh │◀─────── Fetch latest install.sh
                    │  installer      │         to temp file
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │  Validate       │◀─────── Check #!/usr/bin/env bash
                    │  shebang        │         header is present
                    └────────┬────────┘
                             │ valid
                             ▼
                    ┌─────────────────┐
                    │  Re-exec with   │◀─────── RU_INSTALLER_REFRESHED=1
                    │  fresh copy     │         prevents infinite loop
                    └─────────────────┘
```

This ensures that even if CDN caching serves a stale version initially, the installer will fetch and execute the latest code. The `RU_INSTALLER_REFRESHED=1` environment variable prevents recursion.

To disable self-refresh (for airgapped environments):
```bash
RU_INSTALLER_NO_SELF_REFRESH=1 bash install.sh
```

<details>
<summary><strong>Manual installation</strong></summary>

```bash
# Download script
curl -fsSL https://raw.githubusercontent.com/Dicklesworthstone/repo_updater/main/ru -o ~/.local/bin/ru
chmod +x ~/.local/bin/ru

# Ensure ~/.local/bin is in PATH
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc  # or ~/.bashrc
source ~/.zshrc
```

</details>

<details>
<summary><strong>Installation options</strong></summary>

```bash
# Custom install directory
curl -fsSL .../install.sh | DEST=/opt/bin bash

# System-wide installation (requires sudo)
curl -fsSL .../install.sh | RU_SYSTEM=1 bash

# Install specific version
curl -fsSL .../install.sh | RU_VERSION=1.0.0 bash

# Install from main branch (not recommended for production)
curl -fsSL .../install.sh | RU_UNSAFE_MAIN=1 bash
```

Release installation requires a matching SHA256 entry and either `sha256sum`
or `shasum`. Missing assets, checksums, or verification tools fail installation;
they never select mutable `main` automatically. `RU_UNSAFE_MAIN=1` explicitly
opts into an unverified development script.

The curl-to-Bash bootstrap trusts the HTTPS installer download. Its optional
self-refresh also downloads the installer from mutable `main`; that installer
is not authenticated by the release checksum. Release checksum verification
applies to the installed `ru` asset. Set `RU_INSTALLER_NO_SELF_REFRESH=1` to
keep the installer bytes you downloaded.

</details>

### First Run

```bash
# Initialize configuration
ru init

# Add some repos
ru add Dicklesworthstone/mcp_agent_mail
ru add Dicklesworthstone/beads_viewer

# Sync everything
ru sync
```

---

## 🛠️ Commands

```
ru [command] [options]
```

### Available Commands

| Command | Description |
|---------|-------------|
| `sync` | Clone missing repos and pull updates (default) |
| `status` | Show repository status without making changes |
| `init` | Initialize configuration directory and files |
| `add <repo>` | Add a repository to your list |
| `remove <repo>` | Remove a repository from your list |
| `list` | Show configured repositories |
| `doctor` | Run system diagnostics |
| `self-update` | Update ru to the latest version |
| `config` | Show or set configuration values |
| `prune` | Find and manage orphan repositories |
| `review` | AI-assisted code review orchestration |
| `agent-sweep` | Orchestrate AI agents across dirty repos |
| `ai-sync` | Auto-commit dirty repos using AI agents (via ntm) |
| `dep-update` | AI-powered dependency updates across repos |
| `import` | Import repos from GitHub stars or org |
| `robot-docs` | Machine-readable CLI documentation (JSON) |

### Global Options

| Flag | Description |
|------|-------------|
| `--help`, `-h` | Show help message |
| `--version`, `-v` | Show version |
| `--json` | Output JSON to stdout (human output still goes to stderr) |
| `--quiet`, `-q` | Minimal output (errors only) |
| `--verbose` | Detailed output |
| `--non-interactive` | Never prompt (for CI/automation) |

### Command-Specific Options

**`ru sync`**
| Flag | Description |
|------|-------------|
| `--clone-only` | Only clone missing repos, don't pull |
| `--pull-only` | Only pull existing repos, don't clone |
| `--autostash` | Stash changes before pull, pop after |
| `--rebase` | Use `git pull --rebase` instead of merge |
| `--dry-run` | Show what would happen without making changes |
| `--dir PATH` | Override projects directory |
| `--parallel N`, `-j N` | Sync N repos concurrently (default: 1) |
| `--timeout SECONDS` | Network timeout for slow operations (default: 30) |
| `--resume` | Resume an interrupted sync from where it left off |
| `--restart` | Discard interrupted sync state and start fresh |

**Ad-hoc sync:** You can also pass repo URLs directly without adding them to config:
```bash
ru sync owner/repo1 owner/repo2 https://github.com/owner/repo3
```

**`ru status`**
| Flag | Description |
|------|-------------|
| `--fetch` | Fetch remotes first (default) |
| `--no-fetch` | Skip fetch, use cached state |

**`ru add`**
| Flag | Description |
|------|-------------|
| `--private` | Add to private repos list |
| `--public` | Add to public repos list (default) |
| `--from-cwd` | Detect repo from current directory's git remote |

**`ru remove`**
| Flag | Description |
|------|-------------|
| `--private` | Remove from private repos list only |
| `--public` | Remove from public repos list only |
| (none) | Search and remove from all repo lists |

**`ru list`**
| Flag | Description |
|------|-------------|
| `--public` | Show only public repos |
| `--private` | Show only private repos |