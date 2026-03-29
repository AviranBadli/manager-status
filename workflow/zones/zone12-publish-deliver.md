# Zone 12: Publish & Deliver

## Responsibility
Create a new Google Doc from the approved report and deliver the link to the user.

## Input
- `state/report_approved.json`
- `state/report_draft.md`
- `guidelines/jira-config.md` (user email, timezone)

## Output
- `state/report_draft.md` (frontmatter updated with Google Doc URL)
- **File:** `state/delivery.json`
- Local artifact: `artifacts/reports/status-sprint-{N}-{date}.md`

## Execution Steps

### Step 1: Verify Approval
Read `state/report_approved.json`. If `ready_to_publish != true` → FAIL, Zone 11 must complete first.

### Step 2: Show Final Confirmation Before Creating Doc

```
📋 Creating Google Doc
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Title: "Weekly Status - Sprint [N] - [Date]"
Content: [N] characters

⚠️ This will create a NEW Google Doc.
Confirm? (yes / no)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Do NOT call Google API until user says "yes".**

### Step 3: Create Google Doc

Use Google Workspace MCP to create a new doc:
- Title: `Weekly Status - Sprint [N] - [Date]`
- Body: full report content formatted as HTML
- **NEVER overwrite existing docs — always NEW**

### Step 4: Copy to Artifacts

Copy the approved report to:
```
artifacts/reports/status-sprint-{N}-{date}.md
```

Update frontmatter:
```yaml
---
status: published
sprint: N
google_doc_url: https://docs.google.com/document/d/{id}/edit
published_at: ISO8601
---
```

### Step 5: Write Delivery State
```json
{
  "sprint_id": <id>,
  "google_doc_url": "https://docs.google.com/document/d/.../edit",
  "google_doc_title": "Weekly Status - Sprint N - [Date]",
  "artifact_path": "artifacts/reports/status-sprint-N-YYYY-MM-DD.md",
  "delivered_at": "ISO8601"
}
```

### Step 6: Confirm to User
```
✅ Workflow Complete!

Google Doc: https://docs.google.com/document/d/.../edit
Title: "Weekly Status - Sprint [N] - [Date]"
Local: artifacts/reports/status-sprint-[N]-[date].md

Share the Google Doc link with your manager.
```

## Zone Trigger Prompt
```
You are in Zone 12: Publish & Deliver.

1. Verify state/report_approved.json shows ready_to_publish = true
2. Show final confirmation before creating any Google Doc
3. After "yes": create a NEW Google Doc (never overwrite)
4. Copy report to artifacts/reports/
5. Write state/delivery.json
6. Give user the Google Doc link

Read: workflow/zones/zone12-publish-deliver.md
🚨 One final "yes" confirmation required before creating the Google Doc.
🚨 ALWAYS create a NEW doc — never edit existing ones.
```

## Exit Criteria
- [ ] `state/delivery.json` exists with `google_doc_url`
- [ ] Local artifact saved to `artifacts/reports/`
- [ ] User received Google Doc link
