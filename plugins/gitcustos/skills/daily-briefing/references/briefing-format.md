# Daily Briefing Format Reference

## Full Output Template

```
# Daily Briefing — {Project Name}
{Day, Month DD, YYYY}

---

## What's happening now

{One paragraph per active work stream}

---

## What needs attention

{AI-identified items — each with bold title and explanation}

---

## What changed since last time
_Compared to briefing from {previous date}_

{Delta summary}
```

---

## Work Stream Paragraph Template

**{Work Stream Name}** — {1-sentence description of what this is}. {N} PR(s) {in progress / nearing merge / stalled}. {Who is working on what, with linked PRs}. {What is moving, what is blocked, what is waiting and for how long}.

Every PR number, wherever it appears, must be a clickable link: `[#N](url)`. This applies to inline text, table cells, list items, and delta sections — no exceptions. Never write a bare `#NNN`. If tickets are configured, append the ticket link immediately after: `[#N](pr-url) · [KEY-123](ticket-url)`.

### Reviewer State Rendering

When referencing a PR inline, always reflect the current review state — never list a reviewer without their decision state. Use this format:

```
([#N](url), {age}, {review summary})
```

**Review summary rules:**
- If all reviews are approvals and no changes are requested: `approved by {names}` — the PR is ready to merge (pending other gates).
- If there are pending reviewers (assigned but not yet reviewed): `pending: {names}`.
- If there are change requests: `changes requested by {names}`.
- Combine states when mixed: `approved: lronzier · changes requested: nassimfrikha · pending: jsmith`.
- If no reviewer is assigned: `no reviewer`.

**Never write `reviewer: {name}`** — this loses the decision state and creates ambiguity between "assigned but not reviewed yet" and "has already reviewed."

**Examples:**
```
([#2740](url), 4 days, approved: lronzier · pending: nassimfrikha)
([#2741](url), 2 days, changes requested: nassimfrikha)
([#2742](url), 6 days, no reviewer)
([#2743](url), 1 day, approved: lronzier, nassimfrikha)
```

**Table example (What needs attention):**
| PR | Title | Age | Author |
|---|---|---|---|
| [#2745](https://github.com/acme/app/pull/2745) | Spotlight Monitoring | 4 days | olivierTellierKreactive |
| [#2733](https://github.com/acme/app/pull/2733) | Portal Monitoring | 6 days | olivierTellierKreactive |

**List example (What changed):**
- [#2698](https://github.com/acme/app/pull/2698) — Personal Selection Look (florentdouine) — merged

**Example (no tickets):**
> **Auth service refactor** — Migrating from session-based to JWT authentication. 2 of 4 PRs merged. Token refresh logic ([#87](https://github.com/acme/api/pull/87)) is in review with Alice; the migration script ([#91](https://github.com/acme/api/pull/91)) has been waiting for a reviewer since yesterday.

**Example (with tickets):**
> **Auth service refactor** — Migrating from session-based to JWT authentication. 2 of 4 PRs merged. Token refresh logic ([#87](https://github.com/acme/api/pull/87) · [AUTH-42](https://acme.atlassian.net/browse/AUTH-42)) is in review with Alice; the migration script ([#91](https://github.com/acme/api/pull/91) · [AUTH-45](https://acme.atlassian.net/browse/AUTH-45)) has been waiting for a reviewer since yesterday.

**Example:**
> **Search v2** — Rebuilding search with Elasticsearch, led by Bob. Large PR ([#104](https://github.com/acme/api/pull/104), 47 files) has been in review for 3 days. Carol submitted change requests this morning on the query parser; Bob hasn't pushed updates since.

**Example:**
> **Misc / maintenance** — 3 small dependency bumps merged, no active review needed. Dave's flaky-test fix is open with CI green, waiting for a quick look.

---

## What Needs Attention Item Template

**{Short, specific title}** — {Explanation: what is happening, why it matters, what work stream or PR it affects, optional suggested action.}

**Example items:**
> **No reviewer assigned — 2 days** — Alice's token-refresh PR has had no reviewer since it opened. This is blocking the auth refactor from completing on schedule.

> **Large PR with limited review** — Bob's Search v2 PR touches 47 files with no test changes and has been in review for 3 days with only one reviewer engaged. High-risk merge.

> **Throughput drop** — The team merged 12 PRs last week but only 1 so far this week, with 4 PRs waiting for review. Review capacity may be the bottleneck.

> **Stalled work stream** — No activity on the billing integration work stream for 4 days. Last action was Carol's change request on Monday; author hasn't responded.

**Rules for this section:**
- Each item must explain *why* it matters, not just describe what it is
- Suggest an action only when it's obvious (e.g. "assign a reviewer to X")
- Never suggest actions Claude could take — this is read-only. Frame suggestions as things the manager might do.
- Skip items that are explainable by memory context (e.g. vacation, known bot)

---

## What Changed Section Template

```
Since {previous briefing date}:
- {Work stream}: {what changed — what moved forward, what got stuck, what's new}
- {Work stream}: {delta}
- {N} small merges (dependency bumps, minor fixes)
- New PRs opened: {list if meaningful}
- PRs closed without merge: {mention if notable}
```

Skip noise. A meaningful change is: reviewer assigned or removed, review submitted (approval or change request), PR merged, PR newly blocked, CI status changed, new PR opened in an active work stream.

**Reviewer delta rules — do not conflate these:**
- A reviewer who was listed yesterday and submitted an **approval** today → write `{name} approved` — do NOT write "no longer appears" or "removed".
- A reviewer who was listed yesterday and submitted **changes requested** today → write `{name} requested changes`.
- A reviewer who was listed yesterday and is **no longer assigned** (without having reviewed) → write `{name} removed as reviewer`.
- A reviewer **newly assigned** → write `{name} assigned as reviewer`.

Cross-reference the review decision from the GitHub reviews list before describing any change in reviewer presence. Never infer removal from absence alone.

---

## Health Assessment for Project Pulse

When generating a one-line health status and indicator for the Project Pulse view:

| Indicator | Label | When to use |
|-----------|-------|-------------|
| ✅ | On track | Work is progressing, review cycle healthy, no significant blockers |
| ⚠️ | Needs attention | 1–2 situations that could slow things down, not yet critical |
| 🔴 | Stalled | Multiple PRs blocked, throughput significantly below normal, key work stream inactive 3+ days |

**One-line summary examples:**
- "Steady progress on auth refactor; 1 PR waiting for a reviewer."
- "Quiet week — 3 small fixes merged, no active work streams."
- "Search v2 PR stalled in review for 3 days; throughput below average."
- "Active sprint: 5 PRs in review, 2 merged today."

Avoid vague summaries. Be specific about what's actually happening.
