# Zone 7: Initiative Context Gathering

## Responsibility
For each approved Initiative, read the full description, all Epic descriptions, and any linked Google Docs. Build the narrative context used in the Executive Summary and Extra Context sections.

## Input
- `state/approval.json`
- `state/initiatives.json`
- `state/epics_verified.json`
- `guidelines/jira-config.md` (Google Doc IDs if configured)

## Output
- **File:** `state/initiatives_with_context.json`
- **Schema:** `state-schemas/initiatives_with_context.json`

## Execution Steps

### Step 1: Verify Approval
Read `state/approval.json`. If `phase2_ready != true` → FAIL, Zone 6 must complete first.

### Step 2: For Each Approved Initiative

```
1. Fetch full Initiative: getJiraIssue(key, expand: "description,comments")
   - Read description thoroughly
   - Extract: what it is, goals, expected outcomes

2. Fetch ALL Epics under this Initiative:
   JQL: project = {PROJECT_KEY} AND parent = {INITIATIVE_KEY} AND issuetype = Epic
   (not just the verified ones — get ALL epics for full picture)

3. For each Epic: read description

4. If Initiative description contains Google Doc links → read them via Google Workspace MCP

5. Build 3 narrative pieces:
   a. "3-line explanation" (for report header)
   b. "What is it?" (for Extra Context — transformation story)
   c. "Why does it matter?" (for Extra Context — concrete impact)
```

### Step 3: Write State File
```json
{
  "sprint_id": <id>,
  "initiatives": [
    {
      "key": "PROJ-XXX",
      "summary": "...",
      "description_raw": "...",
      "three_line_explanation": "...",
      "what_is_it": "...",
      "why_it_matters": "...",
      "epics_all": [
        { "key": "...", "summary": "...", "status": "Done/In Progress/Planned" }
      ],
      "linked_docs_read": ["doc_id_1"]
    }
  ],
  "gathered_at": "ISO8601"
}
```

### Step 4: Confirm to User
```
✅ Initiative context gathered:
   [N] Initiatives read
   [N] Epics read
   [N] linked docs read

State saved: state/initiatives_with_context.json
Ready for Zone 8?
```

## Zone Trigger Prompt
```
You are in Zone 7: Initiative Context Gathering.

1. Verify state/approval.json shows phase2_ready = true
2. For each approved Initiative: fetch full description + all Epics
3. Read any linked Google Docs from Initiative descriptions
4. Write 3-line explanation, "What is it?", "Why it matters" for each Initiative
5. Write state/initiatives_with_context.json
6. Confirm to user

Read: workflow/zones/zone7-initiative-context.md
DO NOT proceed to Zone 8 automatically.
```

## Exit Criteria
- [ ] `state/initiatives_with_context.json` exists
- [ ] Every Initiative has `three_line_explanation`, `what_is_it`, `why_it_matters`
- [ ] All Epics under each Initiative are listed with status
