# Status Generate Skill

Generate a complete weekly status report for your manager by discovering active JIRA work bottom-up, gathering context from Calendar, Slack, and Google Docs, then assembling a structured report.

All output must be in English.

---

## Input

`$ARGUMENTS` — Sprint number (e.g., `17`). If not provided, ask the user which sprint to report on.

---

## Step 1 — Load Configuration and Date

Read these files silently before doing anything:
- `guidelines/status-guidelines.md` — critical rules, methodology, common mistakes
- `guidelines/status-report-format.md` — output format reference
- `skills/status-report-template.md` — canonical section structure
- `guidelines/jira-config.md` — your Cloud ID, Project Key, Sprint ID mapping, Slack channel, Google Doc IDs

Call `mcp_time_get_current_time` with your configured timezone. Store the date for the report header.

If `guidelines/jira-config.md` does not exist, ask the user to create it from `guidelines/config-example.md` before proceeding.

---

## Step 2 — Resolve Sprint ID

From `guidelines/jira-config.md`, look up the Sprint ID for the requested sprint number.

**Sprint ID ≠ Sprint Number.** Never use the sprint number directly as the JIRA sprint ID.

If the sprint number is not in the config, ask the user to add it:
```
⚠️ Sprint [N] ID is not in your config.

Ask Cursor: "What is the Sprint ID for Sprint [N] in project [PROJECT_KEY]?"
Then add it to guidelines/jira-config.md and re-run.
```

---

## Step 3 — Discover Active Initiatives (Bottom-Up ONLY)

**Critical methodology: NEVER assume an Initiative is active just because it exists. Only include Initiatives that have actual Stories or Subtasks in this sprint.**

### Phase A — Find All Stories and Subtasks in Sprint

Run these JQL queries in parallel:
```
# All Stories in sprint
project = {PROJECT_KEY} AND sprint = {SPRINT_ID} AND issuetype = Story ORDER BY status DESC

# All Subtasks in sprint
project = {PROJECT_KEY} AND sprint = {SPRINT_ID} AND issuetype = Sub-task ORDER BY status DESC
```

### Phase B — Trace Each Story Up to Its Initiative

For each Story/Subtask found:
1. Get the parent Epic key from `fields.parent.key`
2. Fetch the Epic: `getJiraIssue(epicKey, expand: "changelog,comments")`
3. Get the Initiative from Epic's parent: `fields.parent.key`

Build a deduplicated list of active Initiatives.

**Validation:** For each Initiative found, verify it has at least 1 Story or Subtask in the sprint. If 0 Stories → exclude from report.

---

## Step 4 — Full Analysis Per Initiative

For each active Initiative, run Phase B (context) and Phase C (status) in parallel:

### Phase B — Go UP for Context

```
1. Fetch full Initiative: getJiraIssue(initiativeKey, expand: "changelog,comments")
   - Read description thoroughly (multiple times if needed)
   - If linked docs in description → read them via Google Workspace MCP

2. Find ALL Epics under this Initiative:
   JQL: project = {PROJECT_KEY} AND parent = {INITIATIVE_KEY} AND issuetype = Epic

   For each Epic:
   - Status: Done ✅ / In Progress 🔄 / Backlog 📋
   - Read Epic description

3. BUILD the Initiative explanation immediately (store for later):
   a. 3-line explanation for Executive Summary and Deep Dive header
   b. "What is it?" paragraph for Extra Context
   c. "Why does it matter?" paragraph for Extra Context
```

### Phase C — Go DOWN for Story Status

For each Story in sprint (under this Initiative):
```
1. Get ALL Subtasks of this Story:
   JQL: project = {PROJECT_KEY} AND parent = {STORY_KEY} ORDER BY status DESC

2. For each Subtask: read description, check status

3. Count: X Done ✅ / Y In Progress 🔄 / Z Backlog 📋

4. Build detailed status line:
   "X/Y complete: ✅ [done items]. 🔄 Now working on: [in progress]. 📋 Backlog: [backlog]"
```

---

## Step 5 — Gather Supplemental Context

Run these in parallel:

**Calendar (last 7 days):**
```
get_events(user_google_email: {USER_EMAIL}, time_min: [7 days ago], time_max: [today], max_results: 50)
```
Look for: decisions made in meetings, blockers raised, milestone events.

**Slack Team Channel (last 7 days):**
```
conversations_history(channel_id: {TEAM_CHANNEL_ID}, limit: 100)
```
For each message with `thread_ts` → read the full thread.
Look for: blockers, unblocked items, decisions, risks mentioned.

**GitHub (if repos configured):**
Check recent PRs merged or in review. Note significant completions.

**Google Docs (if IDs configured):**
Scan key planning/design docs for recent updates relevant to this sprint.

---

## Step 6 — Determine Initiative Status

For each Initiative, assign one status based on the evidence:

| Status | When to use |
|---|---|
| 🟢 On track | Work progressing as planned, no blockers |
| 🟡 Pending | Waiting on external dependency or decision |
| 🟠 Delayed | Behind schedule with a new expected date |
| 🔄 In Progress | Active work, status neutral |

**NEVER invent new statuses. Only use the 4 above for TL;DR/Executive Summary.**

Base status on JIRA data, NOT on assumptions or inferences.

---

## Step 7 — Assemble the Report

Follow the format from `skills/status-report-template.md` exactly.

**Section order:**
1. Header (date)
2. Sprint Info
3. TL;DR (one line per Initiative)
4. Executive Summary (per Initiative + Management Takeaway)
5. Deep Dive (full Epic + Story breakdown per Initiative)
6. Highlights
7. In Progress
8. Risks / Blockers
9. Next Steps
10. Extra Context (Initiative explainers)

**Quality rules:**
- Every Story needs a 1.5-2 sentence explanation (not just the JIRA title)
- Three Story sections: Completed, In Progress, Backlog — never skip a section
- Management Takeaway must have 3 concrete, actionable bullets
- Extra Context must explain TRANSFORMATION (before vs. after), not just describe
- Status must come from JIRA fields, never invented or inferred

---

## Step 8 — Self-Review (Mandatory)

Before showing the report to the user:

1. **Read the entire report twice**
2. For EACH item ask: "Is this clear? Would the manager understand this without JIRA context?"
3. If unclear → rewrite until you fully understand it
4. Check: Are all Stories covered? Are statuses accurate? Is the TL;DR accurate?

---

## Step 9 — Present to User and Ask About Google Doc

Show the full report in the chat. Then ask:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Status report for Sprint [N] is ready.

Review the report above. When you're satisfied:
- Say "publish" or run /status.publish to create a new Google Doc
- Or edit the content above and I'll incorporate your changes first

Shall I make any changes before publishing?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**NEVER create a Google Doc without explicit user approval.**

---

## Step 10 — Save Draft Artifact

Save the report as a local artifact:
```
artifacts/reports/status-sprint-{N}-{date}.md
```

With frontmatter:
```yaml
---
status: draft
sprint: {N}
created: YYYY-MM-DD
google_doc_url: ""   # filled in after publish
manager: ""          # from config
---
```
