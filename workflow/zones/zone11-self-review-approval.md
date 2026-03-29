# Zone 11: Self-Review & Human Approval

## Responsibility
Review the assembled report twice, rewrite any unclear items, present the final report to the user, and get approval before publishing.

## Input
- `state/report_draft.md`

## Output
- `state/report_draft.md` (updated in-place with review fixes)
- **File:** `state/report_approved.json`

## Execution Steps

### Step 1: Self-Review Pass 1

Read the entire report. For each item ask:
> "Would the manager understand this without any JIRA context?"

Flag items that are:
- Too technical (JIRA key without explanation)
- Too vague ("working on improvements")
- Missing context (what is this? why does it matter?)
- Incorrect status (check against stories_detailed.json)

### Step 2: Rewrite Flagged Items

Fix every flagged item. Rewrite in plain business language.

### Step 3: Self-Review Pass 2

Read the entire report again.

Check specifically:
- [ ] TL;DR matches what's in Executive Summary
- [ ] Deep Dive completeness: all 3 sections per Initiative
- [ ] Management Takeaway: 3 concrete, actionable bullets
- [ ] Extra Context: transformation story (before/after), not just description
- [ ] Risks: every risk has Impact + Status
- [ ] No invented statuses (only the 6 approved ones)

### Step 4: Present to User

Show the full report in chat. Then:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Self-review complete (2 passes). Report ready.

Review the report above. When satisfied:
  • "publish" or /status.publish → create Google Doc
  • Describe changes → I'll update and re-present

Shall I make any changes?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Step 5: Handle Edits and Final Approval

If user requests changes → apply, re-display, ask again.

When user approves → write `state/report_approved.json`:
```json
{
  "sprint_id": <id>,
  "approved_at": "ISO8601",
  "report_file": "state/report_draft.md",
  "ready_to_publish": true,
  "user_notes": ""
}
```

## Zone Trigger Prompt
```
You are in Zone 11: Self-Review & Human Approval.

1. Read state/report_draft.md
2. Self-review TWICE — rewrite any unclear items
3. Present the full report to the user
4. Accept edits until user is satisfied
5. Write state/report_approved.json when approved

Read: workflow/zones/zone11-self-review-approval.md
🚨 NEVER show a report without doing self-review first.
🚨 NEVER create a Google Doc without explicit user approval.
DO NOT proceed to Zone 12 automatically.
```

## Exit Criteria
- [ ] Report has been self-reviewed twice
- [ ] User has explicitly approved
- [ ] `state/report_approved.json` exists with `ready_to_publish = true`
