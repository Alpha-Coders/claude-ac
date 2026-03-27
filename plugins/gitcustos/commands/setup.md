---
description: Set up or update a GitCustos project
allowed-tools: Read, Write, Bash
---

Set up or update the GitCustos configuration for this workspace.

**Step 1 — Check if config exists**

Check whether `config.json` already exists.
- If yes: load it and tell the manager you're updating it, showing current values
- If no: create a new config

**Step 2 — Collect configuration**

Ask for the following, one at a time. Show current values when updating:

1. **Project name** — "What do you want to call this project? (Used for display in briefings.)"

2. **Repositories** — "Which GitHub repositories should this project monitor? For each one, provide the repo (owner/repo) and a short description of what it is (e.g. customer-facing API, backoffice, mobile app). The description helps GitCustos understand context when generating briefings."
   - Collect repos one at a time or as a list — adapt to how the manager answers
   - Description should be short and plain: "backend API", "React frontend", "admin dashboard", "data pipeline", etc.
   - If updating, show current repos and descriptions and ask what to change

3. **QA stage tracking** — "How does your team signal that a PR is ready for QA?"
   - If they use a label: ask for the label name (suggest `ready-for-qa` as default)
   - If they don't have a QA stage (e.g. PRs go straight to merge): note that and skip
   - Accept free-text — the manager might describe something like "we use a label called `qa` in one repo and `ready-for-qa` in another" — store whatever they say faithfully in the config

4. **Ticket tracker** (optional) — "Do your PRs reference tickets from Jira or a similar tracker? (e.g. PROJ-1234 in the title or description)"
   - If yes: ask for the Jira base URL (e.g. `https://acme.atlassian.net`) and the project key prefixes to look for (e.g. `PROJ`, `ENG`, `BACK` — can be multiple)
   - If no or unsure: skip — ticket linking is disabled

**Step 3 — Write config**

Create `` and `briefings/` if they don't exist.

Write `config.json`. Only include optional fields if configured:

```json
{
  "name": "{project-name}",
  "repositories": [
    { "repo": "owner/repo-a", "description": "backend API" },
    { "repo": "owner/repo-b", "description": "React frontend" }
  ],

  // QA stage — omit entirely if not used
  "qa_label": "{label}",
  // or per-repo:
  "qa_labels": { "owner/repo-a": "ready-for-qa", "owner/repo-b": "qa" },

  // Ticket tracker — omit entirely if not configured
  "tickets": {
    "base_url": "https://acme.atlassian.net",
    "project_keys": ["PROJ", "ENG"]
  }
}
```

**Step 4 — Initialize memory**

If `memory.md` does not exist, create it:

```markdown
# Project Memory — {Project Name}

## Team
<!-- Add team members here: Name — Role. Notes. -->

## Work Streams
<!-- Add active work streams here: **Name** — Description. PRs: `branch-pattern` in repo. -->

## Notes
<!-- Add project-specific notes here: label conventions, bots to ignore, etc. -->

## Watch
<!-- Items to follow up on in the next briefing. Managed automatically by GitCustos. -->
```

If memory already exists, leave it unchanged.

**Step 5 — Confirm**

Tell the manager the project is ready. Suggest running `/briefing` to generate the first briefing, and `/remember` to add team and work stream context.
