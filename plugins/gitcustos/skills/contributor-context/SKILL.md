---
name: contributor-context
description: >
  This skill should be used when generating context about a specific contributor's
  activity for performance review preparation, one-on-ones, or team understanding.
  Triggers on "contributor", "what has Alice been working on", "activity for review",
  "performance context", "what did Bob contribute", or any request for a summary of
  one person's GitHub activity over a period.
version: 0.1.0
---

## Purpose

Generate factual, neutral context about a contributor's GitHub activity to support
performance review conversations. This is background material for a manager who
already knows the person — not a score, not a verdict, not a ranking.

The output answers: "What has this person actually been doing, and what patterns are
visible in the data?" The manager brings the judgment. The skill brings the facts.

## Framing Rules

- Never use evaluative language: no "strong", "weak", "good", "poor", "impressive"
- Never compare to other contributors
- Never compute a score or rating
- Always note what the data cannot tell you: complexity, impact, context
- If a metric looks unusual, note it neutrally and suggest the manager ask the person directly

## Time Window

Default to the last 90 days (typical review cycle). Accept any period the manager specifies.

## Data to Fetch

For the specified contributor, across all configured repositories:

1. **PRs authored** — all PRs opened in the window (merged, open, closed without merge)
2. **Reviews submitted** — all PRs they reviewed (use GitHub API: `GET /repos/{owner}/{repo}/pulls/{pull_number}/reviews`, filter by reviewer login)
3. **Work streams** — infer from PR titles, branch names, and project memory which work streams they contributed to

## What to Compute

### Contribution Volume
- PRs opened in the period (merged / still open / closed without merge)
- Files changed across merged PRs (total, gives a rough sense of scope)
- Repositories they were active in

### Work Stream Involvement
- Which work streams did their PRs belong to?
- Were they the primary contributor on any, or supporting?

### Review Activity
- Number of PRs reviewed
- Were they reviewing across different work streams or only their own area?

### Collaboration Patterns
- Who reviewed their PRs most often? Who did they review most often?
- Average time their PRs waited for a first review (from project metrics context)
- Average time they took to respond to review comments (time from review submitted to next commit or comment)

## Output Format

```
## Contributor Context — {Name}
{Window: last 90 days, {date} to {date}}

### Activity Summary
{N} PRs opened ({N} merged, {N} open, {N} closed)
Active in: {repo list with descriptions}

### Work Streams
{List of work streams their PRs touched, with brief note on role}

### Review Participation
{N} PRs reviewed across {N} repos / work streams
{Note if they reviewed outside their own area — signals breadth}

### Collaboration
PRs typically reviewed by: {names of frequent reviewers}
PRs they frequently reviewed: {names of authors}

### Patterns Worth Noting
{1-3 factual observations that might be useful context for a review conversation.
Examples: "PRs tend to be large (median N files) — may indicate work style or task nature."
"Response to review comments was typically fast (median X hours)."
"3 PRs were closed without merging — could be worth discussing context."}

---
_This data covers GitHub PR activity only. It does not capture: quality of contributions,
complexity of problems solved, impact, communication outside PRs, mentoring, or other
forms of work. Use as one input among many._
```

## Sensitive Handling

If the data shows something that could be misread negatively (e.g. few PRs, long cycle times on their PRs, PRs closed without merge), do not speculate on causes. State the fact and add: "Context from the person directly would clarify this."
