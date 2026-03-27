---
description: Analyze cycle time and flow bottlenecks
allowed-tools: Read, Bash
argument-hint: [30d|90d|7d]
---

Analyze the PR contribution flow metrics for this project.

**Step 1 — Load config**

Read `config.json` for the repo list and QA stage configuration. If it doesn't exist, tell the manager to run `/setup` first.

**Step 2 — Determine time window**

If $ARGUMENTS specifies a window (7d, 14d, 30d, 90d), use it. Default: 30 days.

**Step 3 — Fetch GitHub data**

Use GitHub MCP. The API returns at most 100 results per page — **paginate until the window is fully covered**.

For merged PRs: fetch page 1, check the oldest `merged_at` in the results. If it is still within the window, fetch page 2. Continue until the oldest `merged_at` on the page falls before the window start date, or there are no more pages. Discard any PRs with `merged_at` outside the window.

For active repos this typically means 2–4 pages for a 90-day window. Do not stop at page 1.

For each repository, collect across all pages:
- All PRs merged within the window: created_at, merged_at, author, files changed, number
- Reviews on each merged PR: reviewer, submitted_at, state
- QA label application timestamps (if QA is configured): use the issue events API to find when the label was applied
- All currently open PRs: created_at, number, review state (single page is fine here)

**Step 4 — Compute and analyse**

Follow the `metrics` skill to compute throughput, cycle time breakdown by stage, queue depth, review concentration, and bottleneck identification.

**Step 5 — Display**

Present the output in the format defined by the `metrics` skill. End with a plain-language bottleneck statement and trend direction.
