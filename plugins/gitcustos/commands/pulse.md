---
description: Show all repositories at a glance
allowed-tools: Read, Bash
---

Generate the Project Pulse: a quick-glance health overview of all repositories in this workspace's GitCustos project.

**Step 1 — Load config**

Read `config.json` to get the project name and list of repositories. If it doesn't exist, tell the manager to run `/setup` first.

Read `memory.md` for team context.

**Step 2 — Fetch GitHub data**

Fetch GitHub data via the GitHub MCP (lightweight — no need for full PR details). If the GitHub MCP is not connected, stop and tell the manager to connect it via Cowork's connector settings.

For each repository, fetch:
- Count of open PRs
- Which PRs have no reviewer assigned or have been waiting for review for 2+ days (blocked)
- Count of PRs merged in the last 7 days
- Any PRs with failing CI or merge conflicts

**Step 3 — Assess each repository**

Follow the `project-pulse` skill to determine per repository:
- Health indicator: ✅ On track / ⚠️ Needs attention / 🔴 Stalled
- One-line status summary (specific, not generic)
- Counts: open PRs, blocked PRs, merged this week

Apply memory context when assessing (e.g. don't penalize a repo because a team member is on vacation).

**Step 4 — Display**

Present as a table:

| Repository | Health | Status | Open | Blocked | Merged/wk |

If any repository is 🔴 Stalled, add a brief note below pointing to `/briefing` for the full picture.

Offer to generate a full briefing with `/briefing`.
