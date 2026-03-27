# GitCustos

AI-powered PR situational awareness for tech managers. GitCustos watches GitHub pull request activity and synthesizes it into clear, natural-language briefings — so you always know what the team is working on, what's progressing, what's stuck, and what changed since you last looked. No need to open GitHub.

Each workspace folder is one project. Use separate Cowork workspaces for separate projects.

---

## Setup

### 1. Connect GitHub in Cowork

GitCustos relies on the GitHub connector for all PR data. Before using the plugin, connect GitHub via Cowork's connector settings. This only needs to be done once — the connector is then available to all plugins.

### 2. Configure this project

Run `/setup` to configure the project for this workspace. You'll be asked for:
- A project name (used for display in briefings)
- The GitHub repositories to monitor (format: `owner/repo`, one or more)
- How your team signals a PR is ready for QA — a label, per-repo labels, or nothing if you don't have that stage

### 3. Generate your first briefing

```
/briefing
```

### 4. (Optional) Set up a daily schedule

To receive a briefing automatically each morning, use the `schedule` skill:

> "Set up a daily task at 8am that runs `/briefing`"

---

## Commands

| Command | Description |
|---------|-------------|
| `/briefing` | Generate the daily briefing |
| `/pulse` | Show all repositories at a glance — health, one-line status, key numbers |
| `/setup` | Create or update the project configuration |
| `/ask [question]` | Ask a question about the project or make a correction |
| `/remember [info]` | Add or correct something in the project memory |
| `/metrics [window]` | Analyze cycle time, throughput, and flow bottlenecks (default: 30d) |
| `/contributor [username] [window]` | Activity context for a contributor (default: 90d) |

---

## Skills

| Skill | Purpose |
|-------|---------|
| `daily-briefing` | Synthesizes PR data into the three-section briefing format |
| `project-memory` | Manages the persistent project knowledge base |
| `project-pulse` | Generates the quick-glance per-repository health overview |
| `metrics` | Cycle time breakdown, throughput trends, and bottleneck identification |
| `contributor-context` | Neutral activity context for review conversations |

---

## Project Data

GitCustos stores its files directly in your workspace root:

```
config.json        # project name, repos, QA stage config (optional)
memory.md          # team, work streams, notes — auto-updated after each briefing
briefings/
  YYYY-MM-DD.md    # one file per day
```

You can read and edit these files directly. The memory file in particular is designed to be edited — add team context, correct work stream groupings, note vacations.

---

## Project Memory

The project memory is the key to good briefings. The more context you give it, the better GitCustos understands your team.

Use `/remember` to add information:

```
/remember Alice is on vacation until March 10
/remember The auth-refactor work stream includes PRs from repo-b too
/remember Ignore the legacy-cleanup branch
/remember dependabot PRs are automated — don't flag them
```

GitCustos also updates the memory automatically when it discovers new work streams or contributors. It will always tell you when it does.

---

## GitHub Connector

GitCustos is **read-only**. It never writes to GitHub, creates comments, or modifies PRs.

When connecting GitHub in Cowork, make sure the token or OAuth scope includes `repo` access (or `public_repo` if you only monitor public repositories) so GitCustos can read PR reviews and CI status.
