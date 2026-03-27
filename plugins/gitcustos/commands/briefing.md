---
description: Generate the daily briefing for a project
allowed-tools: Read, Write, Bash
---

Generate the daily briefing for this workspace's GitCustos project.

**Step 1 — Load context**

Read:
- `config.json` — get the project name, list of repositories, and QA label. If this file doesn't exist, tell the manager to run `/setup` first.
- `memory.md` — load team, work streams, and notes
- The most recent file in `briefings/` — for delta comparison (note its date)

**Step 2 — Fetch GitHub data**

Use GitHub MCP tools to fetch data. If the GitHub MCP is not connected, stop and tell the manager to connect it via Cowork's connector settings before continuing.

For each repository in the config, fetch:
- All open PRs with: title, body/description, author, labels, draft status, review state, CI/check status, days since opened, files changed count, time since last activity
- Reviews on each open PR: reviewer, date, decision (approved / changes requested / commented / pending)
- PRs merged in the last 7 days (title and body needed if ticket extraction is configured)
- Any open PRs with merge conflicts or failing CI

**Step 3 — Generate the briefing**

Follow the `daily-briefing` skill to produce a three-section briefing:
- **What's happening now** — work streams with progress and people
- **What needs attention** — AI-identified situations requiring possible manager action, each with context explaining why it matters
- **What changed since last time** — delta vs. the previous briefing (or note if this is the first)

Apply project memory throughout: respect vacations, ignore bots, use canonical work stream names.

**Step 4 — Save and display**

Save the completed briefing to `briefings/{YYYY-MM-DD}.md` (today's date). Then display the full briefing in the conversation.

**Step 5 — Update memory**

After the briefing, automatically update `memory.md`. No confirmation needed — apply all updates silently, then append a brief "Memory updated" note at the end of the response listing what changed. One line per change.

Two types of updates to apply:

1. **Watch section** — this is the priority:
   - Remove items that are now resolved
   - Update unresolved items with their current state (e.g. "now 4 days without a reviewer")
   - Add new items for anything flagged in "What needs attention" that warrants follow-up next time

2. **Everything else** — new contributors, inferred work streams, completed work streams, bots spotted. Follow the `project-memory` skill's full guidance on what qualifies.
