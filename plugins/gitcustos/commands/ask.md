---
description: Ask a question about a project
allowed-tools: Read, Write, Edit, Bash
argument-hint: [question about your project]
---

Answer this question about the project: "$ARGUMENTS"

This is the GitCustos conversation interface. The manager is asking a question about their project's PR activity, or making a correction to the AI's understanding.

**Step 1 — Load context**

Read:
- `config.json` — if it doesn't exist, tell the manager to run `/setup` first
- `memory.md`

**Step 2 — Classify the question**

Determine what kind of request this is:

**A. Information question** — The manager wants to know something about current state.
Examples: "What has Bob been working on this week?", "Why is the auth refactor stalled?", "How long has the Search v2 PR been open?"

→ Fetch the relevant PR data via GitHub MCP. Fetch only what's needed to answer the specific question — don't pull all PRs for a narrow query. Answer with specific facts grounded in real data and project memory. Be direct — not "it looks like..." but "the PR has been open for 6 days with no reviewer assigned."

**B. Correction or grouping request** — The manager is telling the AI something is wrong or wants to change how PRs are grouped.
Examples: "Those 3 PRs from Alice are all part of the auth refactor", "Carol is a contractor, not a bot", "Group the billing PRs into one work stream."

→ Apply the correction. Update `memory.md` with the new information. Confirm to the manager exactly what was updated. Offer to regenerate the briefing if it's relevant.

**C. Regeneration request** — The manager wants a fresh briefing.
Examples: "Regenerate the briefing", "Refresh the briefing with these corrections."

→ Run the briefing generation flow (same as `/briefing`).

**Step 3 — Answer**

Keep answers concise and grounded. If GitHub data is needed to answer a question, fetch only what's relevant — don't pull all PRs when answering a narrow question.

Never speculate or extrapolate beyond what the data shows. If you don't have enough information to answer, say so and explain what data would be needed.
