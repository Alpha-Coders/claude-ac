---
name: project-pulse
description: >
  This skill should be used when showing an overview of all repositories in a GitCustos
  project, checking health across repos at a glance, or getting a quick status summary.
  Triggers on "pulse", "all repos", "repo overview", "status across repositories",
  "which repos need attention", or any request for a quick multi-repo summary.
version: 0.1.0
---

## Purpose

Project Pulse is a quick-glance health view of all repositories in the project. It gives the manager a per-repo status check without generating a full briefing.

## Output Per Repository

For each repository, produce:

1. **Health indicator** — ✅ On track / ⚠️ Needs attention / 🔴 Stalled
2. **One-line status summary** — A single specific sentence about the most important thing happening right now
3. **Key numbers** — Open PRs | Blocked PRs | Merges this week

## Health Indicator Logic

| Indicator | When to use |
|-----------|-------------|
| ✅ On track | PRs moving through review at a normal pace, no significant blockers |
| ⚠️ Needs attention | 1–2 PRs blocked or at risk, review backlog building, or throughput noticeably below recent average |
| 🔴 Stalled | No merges in 5+ days with open PRs, OR 3+ PRs blocked simultaneously, OR key work stream with no activity for 3+ days |

Blocked PR definition: no reviewer assigned for 2+ days, reviewer unresponsive for 3+ days, failing CI with no action taken, or unresolved merge conflict.

## One-Line Summary Rules

Write one specific, informative sentence. Reference actual work streams, numbers, or people when relevant.

**Good:**
- "Steady progress on auth refactor; 1 PR waiting for a reviewer."
- "Active sprint: 5 PRs in review, 2 merged today."
- "Search v2 PR stalled in review for 3 days."
- "Quiet week — 3 small fixes merged, no active work streams."

**Bad:**
- "Things are progressing well."
- "The team is working on several PRs."

## Data Required Per Repository

- All open PRs and their review state (reviewer assigned, CI status, days open)
- PRs merged in the last 7 days
- Project memory (for team context, vacation, bots)

Do not fetch full PR details (files, comments) for Pulse — this is a lightweight view. Fetch just what's needed for counts and health assessment.

## Output Format

Present as a markdown table followed by any urgent notes:

```
| Repository    | Health | Status                                    | Open | Blocked | Merged/wk |
|---------------|--------|-------------------------------------------|------|---------|-----------|
| acme/api      | ✅     | Auth refactor on track, 2 PRs in review.  | 5    | 0       | 8         |
| acme/frontend | ⚠️    | Search v2 PR stalled in review 3 days.    | 3    | 1       | 2         |
```

If any repository is 🔴 Stalled, add a brief note below the table pointing to `/briefing` for the full picture.

If only one repository is configured, skip the table and display a short paragraph instead.
