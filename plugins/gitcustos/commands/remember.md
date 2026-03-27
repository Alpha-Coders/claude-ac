---
description: Add or correct something in project memory
allowed-tools: Read, Write, Edit, Bash
argument-hint: [something to remember about your project]
---

Update the project memory with this information: "$ARGUMENTS"

**Step 1 — Load memory**

Read `memory.md`. If it doesn't exist, tell the manager to run `/setup` first.

**Step 2 — Parse the information**

Identify where the new information belongs:

- **Team section** — anything about a person (role, vacation, part-time status, contractor, bot, etc.)
- **Work Streams section** — anything about a group of work, a feature, a milestone, which branches or repos are included
- **Notes section** — anything else: label conventions, branches to ignore, naming rules

**Step 3 — Update memory**

Apply the information:
- If adding new information, insert it in the appropriate section with consistent formatting
- If correcting existing information, update the entry in place
- Never delete entries without an explicit instruction to do so

**Step 4 — Confirm**

Tell the manager exactly what was added or changed, in one or two sentences. For example: "Got it. Added Alice on vacation until March 10 under Team." or "Updated: the billing-v2 work stream now includes PRs from the payments repo."

If the update implies the current briefing may be stale, mention it and offer to regenerate with `/briefing`.
