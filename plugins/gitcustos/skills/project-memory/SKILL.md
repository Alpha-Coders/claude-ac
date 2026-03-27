---
name: project-memory
description: >
  This skill should be used when reading, updating, or consulting the GitCustos project
  memory. Triggers on "project memory", "remember", "update memory", "who is on the team",
  "work streams", "add a note", "correct that", "Alice is on vacation", or whenever
  context about a project is needed that GitHub data alone cannot provide.
version: 0.1.0
---

## Purpose

Project memory is a persistent, human- and AI-editable knowledge base that augments GitHub PR data with context GitHub cannot provide: team roles, work stream goals, naming conventions, project-specific vocabulary, and manager corrections.

## Memory File Location

```
memory.md
```

## Memory Structure

```markdown
# Project Memory — {Project Name}

## Team
{Name} — {Role}. {Optional notes: part-time, on vacation until X, contractor, etc.}

## Work Streams
**{Work Stream Name}** — {What it is and its goal}. PRs: `{branch-pattern}` in {repos}. Target: {date or milestone}.

## Notes
- {Anything affecting how PR data should be interpreted: label conventions, branch naming, bots to ignore, excluded branches, etc.}

## Watch
- [ ] {Item to follow up on in the next briefing — what to check and why}
```

## The Watch Section

`## Watch` is a checklist of items to explicitly follow up on in the next briefing. It is the bridge between briefings — a way to say "don't let this drop."

**Who adds to Watch:**
- The AI adds an item automatically when flagging something in "What needs attention" that isn't immediately resolvable (e.g. a stalled PR, an unassigned reviewer, a growing review backlog)
- The manager can add items manually via `/remember watch ...`

**Format:** each item is a markdown checkbox with a short description of what to check and optionally why it matters.

```
- [ ] Alice's token-refresh PR — still no reviewer assigned as of today
- [ ] Search v2 review — Bob hasn't responded to Carol's change requests
- [ ] Review throughput — 4 PRs waiting, check if backlog is clearing
```

**How the briefing uses Watch:**
- At the start of generation, read the Watch section
- For each item, explicitly check its current state from GitHub data
- Report on every watch item in the briefing, even if nothing changed ("Still no reviewer on Alice's PR — now 4 days")
- After the briefing, update the Watch section: remove resolved items, keep and update unresolved ones

## Reading Memory

Always read memory before generating briefings, answering questions about a project, or assessing PR activity. Memory context overrides default AI assumptions.

Key things memory resolves:

| Memory entry | Behavior change |
|---|---|
| Team member on vacation | Do not flag their inactivity as unusual |
| Bot/automated account listed | Do not treat as a team member; factor in silently |
| Work stream definition | Use canonical name and scope from memory, not inference |
| Repo-specific label | Apply per-repo QA label when determining PR stage |
| Excluded branch | Do not flag or surface PRs from that branch |

## Updating Memory

When the manager makes a correction or provides information (via `/remember`, `/ask`, or conversation):

1. Read the current `memory.md`
2. Identify the correct section (Team, Work Streams, or Notes)
3. Add or edit the relevant entry — update in place, do not delete existing context
4. Write the file back
5. Confirm to the manager exactly what was added or changed

Never delete entries without explicit instruction. If correcting an entry, update it in place and preserve surrounding context.

## AI-Initiated Updates (Auto, After Briefing)

After generating a briefing, automatically update memory with anything significant learned. Apply updates without asking — then report what changed at the end of the response.

**What qualifies for auto-update:**

| Signal | Memory update |
|--------|---------------|
| PR authors not yet in the Team section | Add them with inferred role (e.g. "contributor") |
| A work stream inferred from branch names/PR titles that isn't in memory | Add it with name, description, and branch pattern |
| A work stream that has fully merged (all PRs closed/merged) | Mark it as completed with the date |
| A recurring reviewer pattern (same person reviews same area) | Note it under the relevant team member or work stream |
| A bot or automated account active on PRs | Add it to Notes as "automated, ignore" |
| A situation flagged in "What needs attention" that isn't immediately resolvable | Add a `- [ ]` item to Watch |
| A Watch item that is now resolved | Remove it from Watch |
| A Watch item still unresolved | Update its description with the latest state (e.g. "now 4 days") |

**What does NOT qualify for auto-update:**

- Anything the manager has already explicitly set (don't overwrite corrections)
- Transient state (e.g. "Alice has 2 PRs open this week") — that belongs in briefings, not memory
- Inferences you're not confident about — when uncertain, skip it rather than pollute memory with noise
- Vacation or absence — never infer this automatically; only record when the manager says so explicitly

**Format for auto-updates:**

Keep additions minimal and consistent with existing memory style. One line per team member, one paragraph per work stream. Do not reformat or reorganize existing content when adding new entries.

## Memory vs. Briefings

Memory is **persistent context** (team, work streams, conventions). Briefings are **time-stamped snapshots** of state. Memory informs briefings; briefings do not overwrite memory. They serve entirely different purposes.
