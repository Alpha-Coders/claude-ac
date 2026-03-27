---
description: Activity context for a contributor
allowed-tools: Read, Bash
argument-hint: [github-username] [90d|30d]
---

Generate activity context for contributor "$ARGUMENTS".

**Step 1 — Parse arguments**

Extract the GitHub username from $ARGUMENTS. If a time window is also specified (e.g. `alice 90d`), use it. Default window: 90 days.

If no username is provided, ask the manager which contributor to look at.

**Step 2 — Load config**

Read `config.json` for the repo list and project name. Read `memory.md` to check if the contributor is in the Team section (role, any notes).

**Step 3 — Fetch GitHub data**

Use GitHub MCP. The API returns at most 100 results per page — **paginate until the window is fully covered**.

For each repository, paginate through closed and open PRs filtered by author, stopping once `created_at` falls before the window start. For active contributors on a 90-day window, expect 1–3 pages depending on their volume.

Collect across all pages:
- PRs authored by the contributor in the window: title, created_at, merged_at, state, files changed, html_url
- Reviews submitted by the contributor: use the reviews endpoint per PR to find reviews where the reviewer login matches
- PR numbers of PRs they authored, to check review response times (time from review submitted to next commit)

**Step 4 — Generate context**

Follow the `contributor-context` skill to produce a factual, neutral activity summary. Apply project memory (work stream names, repo descriptions) to make the output more readable — say "auth refactor work stream" not "branch auth-v2-token-fix".

**Step 5 — Display**

Present the output in the format defined by the `contributor-context` skill, including the disclaimer at the bottom about what the data does not capture.
