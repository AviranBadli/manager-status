# Zone 6: Human Approval Gate 🚨 CRITICAL

## Responsibility
Show the discovered Initiative list to the user and get explicit approval before proceeding to Phase 2. Prevents expensive work on wrong data.

## Input
- `state/initiatives.json`
- `state/validation_report.json`

## Output
- **File:** `state/approval.json`
- **Schema:** `state-schemas/approval.json`

## Why This Zone Is Critical
Phase 2 makes many API calls (Calendar, Slack, Google Docs, full JIRA deep-dives). If the Initiative list is wrong, all of that work is wasted. Human approval here catches errors before they become expensive.

## Execution Steps

### Step 1: Read State Files
Read `state/initiatives.json` and `state/validation_report.json`.

### Step 2: Present Initiative List to User

```
📋 Phase 1 Complete — Initiative Discovery Summary
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Sprint: [N] | Stories verified: [N]

Discovered Initiatives ([N] total):

1. {PROJ-XXX} "[Initiative Title]"
   Epics: [N] | Stories in sprint: [N]
   Epics: "[Epic 1]", "[Epic 2]"

2. {PROJ-YYY} "[Initiative Title]"
   Epics: [N] | Stories in sprint: [N]
   Epics: "[Epic 1]"

[Standalone Epics (N):]
  • {PROJ-ZZZ} "[Epic Title]" — [N] stories

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Does this look correct?

Options:
  ✅ yes    — proceed to Phase 2 (context gathering + report)
  ❌ no     — describe what's wrong, restart from Zone 1
  🔄 re-verify — re-run from Zone 3 (in case of data issues)
```

### Step 3: Handle Response

**If "yes":** Write `state/approval.json` with `decision: "approved"` and proceed.

**If "no" + description:** Write `state/approval.json` with `decision: "rejected"` and the user's notes. Instruct to restart from Zone 1.

**If "re-verify":** Write `state/approval.json` with `decision: "re-verify"`. Instruct to restart from Zone 3.

### Step 4: Write State File
```json
{
  "sprint_id": <id>,
  "decision": "approved",
  "approved_at": "ISO8601",
  "initiatives_approved": ["PROJ-XXX", "PROJ-YYY"],
  "user_notes": "",
  "phase2_ready": true
}
```

## Zone Trigger Prompt
```
You are in Zone 6: Human Approval Gate.

1. Read state/initiatives.json and state/validation_report.json
2. Present the discovered Initiative list in a clear, readable format
3. Ask: does this look correct? (yes / no / re-verify)
4. Write state/approval.json with the user's decision
5. If approved → confirm Phase 2 is ready
6. If rejected → provide restart instructions

Read: workflow/zones/zone6-human-approval-gate.md
🚨 NEVER proceed to Phase 2 without explicit "yes" from the user.
DO NOT start Phase 2 automatically.
```

## Exit Criteria
- [ ] `state/approval.json` exists
- [ ] `decision` is one of: "approved", "rejected", "re-verify"
- [ ] If approved: `phase2_ready = true`
