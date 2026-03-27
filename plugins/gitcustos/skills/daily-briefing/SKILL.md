---
name: daily-briefing
description: >
  This skill should be used when generating a daily briefing, analyzing pull request
  activity for a project, summarizing what the team is working on, identifying what
  needs attention, or comparing current state to a previous snapshot. Triggers on
  "briefing", "what's happening", "what is the team working on", "PR status",
  "what needs attention", "what changed since", or any request for a project status
  update.
version: 0.1.0
---

## Purpose

Generate a structured, natural-language daily briefing from GitHub PR activity. Output is written for a tech manager who wants situational awareness without opening GitHub.

## Core Principle: Synthesis Over Data

Never enumerate raw PR lists. Always group, synthesize, and interpret. The manager sees **insights**, not data.

## Data to Collect

Before generating a briefing, gather from GitHub MCP:

- All open PRs per repository: title, author, labels, draft status, review state, CI status, days since opened, files changed count, last activity timestamp
- Reviews on each PR: who reviewed, when, decision (approved / changes requested / commented / no review yet). For each reviewer, track two separate states: (1) their **current assignment** (are they still a requested reviewer?) and (2) their **review decision** (have they submitted a review, and what was it?). These are independent — an approved reviewer may no longer appear in the requested-reviewers list, which does not mean they were removed.
- Per-PR review summary: total approvals, total change requests, count of pending requested reviewers (assigned but not yet reviewed). Surface these counts wherever a PR's review status is discussed.
- Recently merged PRs (last 7 days)
- PRs with merge conflicts
- PRs with failing CI

Also read:
- Project config (`config.json`) for repo list (with descriptions), QA stage configuration, and ticket settings
- Project memory (`memory.md`) for team, work streams, notes
- Most recent previous briefing from `briefings/` for delta comparison

## Work Stream Inference

Group PRs into work streams using these signals (priority order):

1. Explicit assignment in project memory
2. Branch name prefixes (e.g. `auth-*`, `search-v2-*`)
3. PR title similarity
4. Shared directories in files touched
5. Author clustering

Use canonical work stream names from memory when available. Infer and name new ones when not. After inferring new work streams, note them so memory can be updated.

**Repo descriptions** (from `config.json`) provide useful framing when naming work streams and writing summaries. A PR in the "backend API" repo touching auth endpoints means something different from the same change in the "admin dashboard" repo. Use descriptions to make briefing language more specific and accurate — e.g. "on the backend API" rather than just "in acme/api".

## Output Structure

See `references/briefing-format.md` for full format specification.

**Section A — What's happening now**: One paragraph per active work stream. Cover: what it is, how many PRs, progress, who is involved, what's moving or blocked.

**Section B — What needs attention**: AI-curated items requiring potential manager action. Each item has a bold title and an explanation of **why it matters**. Not mechanical thresholds — use judgment. Common patterns: no reviewer assigned, stalled review, large PR risk, throughput anomaly, failing CI blocking merge.

**Section C — What changed since last time**: Concise delta vs. the previous briefing. Only meaningful changes. Group trivial events (e.g. "3 dependency bumps merged"). If no previous briefing exists, say so and skip the section.

## Watch Items

Before generating, read the `## Watch` section of `memory.md`. For each item:

1. Fetch its current state from GitHub (targeted fetch — just what's needed for that item)
2. Report on it explicitly in the briefing, grouped under a **Watch** heading between sections B and C
3. Use direct language: "Still unresolved — now X days", "Resolved: reviewer assigned yesterday", "Escalating: no response after 5 days"

Never silently skip a watch item. If it's resolved, say so. If it's unchanged, say so. The point is that the manager explicitly asked to be kept informed.

When generating "What needs attention" and an item warrants ongoing follow-up, add it to Watch automatically (see project-memory skill).

## Linking PRs and Tickets

### PR Links (always required)

Every PR number mentioned anywhere in the briefing — inline text, tables, lists, delta sections, watch items — must be a clickable markdown link. Use the PR's `html_url` from GitHub. Format: `[#123](url)`.

Example: "Alice's token refresh PR ([#87](https://github.com/acme/api/pull/87)) has been waiting for a reviewer since Monday."

**Hard rule: never write a bare `#NNN` reference.** Every occurrence of a PR number must be wrapped: `[#NNN](url)`. This applies equally to:
- inline references: `([#2741](https://github.com/acme/app/pull/2741), QA pending, 4 days)`
- table cells: `| [#2745](https://github.com/acme/app/pull/2745) | Spotlight Monitoring | … |`
- list items: `- [#2413](https://github.com/acme/app/pull/2413) — Reactivate strong password`
- delta sections: `- [#2698](https://github.com/acme/app/pull/2698) — Personal Selection Look`

**Before finalizing the briefing:** scan the full output for any `#\d+` pattern. If any is not wrapped in `[#NNN](url)`, fix it before outputting.

### Ticket Links (when configured)

If `config.json` contains a `tickets` block, extract and link ticket references from PR titles and descriptions.

**Extraction:** scan for the pattern `{KEY}-{NUMBER}` where `KEY` is one of the configured `project_keys` (case-insensitive match, uppercase in output). Examples: `PROJ-1234`, `ENG-42`.

**Link construction:** `{base_url}/browse/{TICKET-ID}` — e.g. `https://acme.atlassian.net/browse/PROJ-1234`

**Where to surface tickets:** inline alongside the PR link — `[#87](pr-url) · [PROJ-1234](ticket-url)`. If multiple PRs reference the same ticket, note it once.

**When `tickets` is absent from config:** skip ticket extraction entirely.

## QA Stage Handling

The config may define a QA stage in several ways — or not at all:

- `qa_label`: a single label used across all repos → a PR with this label is in the "Ready for QA" stage
- `qa_labels`: a per-repo map of labels → look up the right label per repo
- Neither field present → the team has no QA stage; omit it from the PR lifecycle and don't reference it in briefings

When QA tracking is configured, use it to determine PR stage and flag PRs that have been in QA for unusually long. When it's absent, the lifecycle goes directly from In Review to Merged/Closed.

## Memory Usage

Always consult project memory before generating. Key rules:
- Do not flag inactivity for team members marked as on vacation
- Use canonical work stream names from memory, not improvised ones
- Do not treat bots or dependabot PRs as team activity — factor them in silently

## After Generating

Save the briefing to `briefings/{YYYY-MM-DD}.md` before displaying it. This enables delta comparison in future briefings.
