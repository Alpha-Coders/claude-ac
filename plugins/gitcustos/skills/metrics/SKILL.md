---
name: metrics
description: >
  This skill should be used when analyzing cycle time, PR throughput, review speed,
  or flow bottlenecks. Triggers on "metrics", "cycle time", "time to merge", "throughput",
  "how fast", "where is the bottleneck", "TTM", "time to market", "flow analysis",
  or any request to understand the speed and efficiency of the contribution process.
version: 0.1.0
---

## Purpose

Analyze the PR contribution flow to surface cycle time metrics, throughput trends, and bottlenecks. The goal is to help the manager understand *where time is going* and *whether things are improving or degrading* — not to judge individuals.

Always fetch fresh data from GitHub. Do not derive metrics from stored briefing files.

## Time Window

Default to the last 30 days unless the manager specifies otherwise. Accept: 7d, 14d, 30d, 90d.

Fetch all PRs merged within the window, plus currently open PRs for queue depth.

## Metrics to Compute

For each metric, compute per-repository and aggregate across all repos. Show both.

### Throughput
- PRs merged per week (total and by repo)
- Trend: compare first half vs second half of the window — is it accelerating or slowing?

### Cycle Time Breakdown
For each merged PR, compute time spent in each stage:

| Stage | How to measure |
|-------|---------------|
| Open → First review | Time from PR created_at to first review submitted_at |
| First review → Approved | Time from first review to approval (or last review if no approval) |
| Ready for QA → Merged | Time from QA label applied to merged_at (if QA configured) |
| Total | PR created_at to merged_at |

Report medians, not averages. Outliers skew averages badly.

Express durations as hours when under 24 hours (e.g. "6h"), and as days with one decimal when 24 hours or more (e.g. "1.5 days"). Never show "0.3 days" when "7h" is clearer.

### Queue Depth
- How many PRs are currently open and how long have they been open?
- Median age of open PRs
- PRs open longer than the median cycle time (these are likely stuck)

### Review Participation
- How many unique reviewers are active?
- Is review load concentrated on a few people? (flag if >50% of reviews come from one person)

## Bottleneck Identification

After computing the breakdown, identify where the most time is spent:

- If **Open → First review** is longest: review assignment or reviewer availability is the bottleneck
- If **First review → Approved** is longest: back-and-forth in review is the bottleneck (large PRs? unclear requirements?)
- If **QA stage** is longest: QA capacity or process is the bottleneck
- If **queue depth** is high relative to throughput: PRs are piling up faster than they're merging

State the bottleneck plainly. One sentence: "Most time is lost waiting for a first review — median 3.2 days out of a 5.1 day total cycle."

## Trend Commentary

If the window is 30+ days, split into two halves and note direction:
- "Cycle time improved from 7.2 to 4.8 days over the period."
- "Throughput dropped from 8 to 5 PRs/week in the second half — coincides with 4 PRs stuck in review."

## Output Format

```
## Metrics — {Project Name}
{Window: last 30 days, {date} to {date}}

### Throughput
{N} PRs merged — {N}/week average
Trend: {accelerating / stable / slowing}

### Cycle Time (median, merged PRs)
| Stage                  | Median  |
|------------------------|---------|
| Open → First review    | Xh or X.X days |
| First review → Approved | Xh or X.X days |
| Ready for QA → Merged  | Xh or X.X days (if applicable) |
| Total                  | X.X days |

### Queue
{N} open PRs — median age {X} days
{N} PRs older than median cycle time (potentially stuck)

### Bottleneck
{One-sentence identification of the primary bottleneck}

### Trend
{2-3 sentences on direction of travel}
```

## What Not to Do

- Do not name individuals in the metrics output — this is flow analysis, not performance evaluation
- Do not compute per-author metrics here — that belongs in `/contributor`
- Do not present raw numbers without interpretation — always say what the number means
