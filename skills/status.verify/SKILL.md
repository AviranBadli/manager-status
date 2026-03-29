# Status Verify Skill

Verify that JIRA data is available and correct for a given sprint before generating the full status report. Surfaces configuration errors and data gaps early.

---

## Input

`$ARGUMENTS` — Sprint number (e.g., `17`).

---

## Step 1 — Load Config

Read `guidelines/jira-config.md`. Check that all required fields are present:
- Cloud ID
- Project Key
- Sprint ID for the requested sprint number
- User email

If any are missing → list them and stop.

---

## Step 2 — Test JIRA Connection

Run these queries and report results:

```
# Count Stories in sprint
project = {PROJECT_KEY} AND sprint = {SPRINT_ID} AND issuetype = Story
```

```
# Count Subtasks in sprint
project = {PROJECT_KEY} AND sprint = {SPRINT_ID} AND issuetype = Sub-task
```

---

## Step 3 — Trace One Story to Initiative

Pick the first Story found and trace it upward:
- Story → Epic (`fields.parent.key`)
- Epic → Initiative (`fields.parent.key`)

Report the full chain: `JN-XXX (Story) → JN-YYY (Epic) → JN-ZZZ (Initiative)`

If the chain breaks (no parent Epic, or Epic has no parent Initiative) → warn the user.

---

## Step 4 — Report Results

```
✅ Verification Complete — Sprint [N]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Sprint ID:     {sprint_id}
Stories found: {N}
Subtasks:      {N}

Sample chain:
{STORY_KEY} → {EPIC_KEY} → {INITIATIVE_KEY} ✅

Config check:
  ✅ Cloud ID configured
  ✅ Project Key: {PROJECT_KEY}
  ✅ Sprint ID {N} mapped to {sprint_id}
  ✅ Slack channel configured
  {✅/⚠️} Google Docs configured

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Ready to generate. Run /status-for-manager [N]
```

If anything fails → list the specific errors and how to fix them.
