# Zone 3: Sprint Membership Verification 🚨 CRITICAL

## Responsibility
Verify each Story ACTUALLY belongs to the sprint using `customfield_10020`. JQL can return historical assignments — this zone filters false positives.

## Input
- **File:** `state/stories_raw.json`
- **File:** `state/sprint.json`

## Output
- **File:** `state/stories_verified.json`
- **Schema:** `state-schemas/stories_verified.json`

## Why This Zone Is Critical
JQL `sprint = X` returns stories that were EVER in the sprint, including ones moved out. `customfield_10020` contains the true current sprint membership. Without this check, the report will include stories that were removed from the sprint.

## Execution Steps

### Step 1: Read State Files
Read `state/stories_raw.json` and `state/sprint.json`.

### Step 2: For Each Story — Verify customfield_10020

For each Story/Subtask, call `getJiraIssue` with `fields: ["customfield_10020", "status", "parent"]`.

Check `customfield_10020`: it contains an array of sprint objects. Verify the target sprint ID appears in this array with status not = "removed".

**Verification logic:**
```
customfield_10020 contains entry where:
  id == sprint_id  AND  state != "removed"
```

If YES → Story is verified ✓  
If NO → Story is historical, exclude from report

### Step 3: Classify Results
- `verified`: stories confirmed in sprint
- `excluded_historical`: stories in JQL but not truly in sprint
- `excluded_other`: any other rejection reason

### Step 4: Write State File
```json
{
  "sprint_id": <id>,
  "verified_count": N,
  "excluded_count": N,
  "stories_verified": [ { "key": "...", "verified": true, ... } ],
  "stories_excluded": [ { "key": "...", "reason": "historical_assignment" } ],
  "verified_at": "ISO8601"
}
```

### Step 5: Confirm to User
```
✅ Sprint verification complete:
   Verified (in sprint):    [N] stories
   Excluded (historical):   [N] stories
   Net reduction: [N] stories removed from analysis

State saved: state/stories_verified.json
Ready for Zone 4?
```

## Zone Trigger Prompt
```
You are in Zone 3: Sprint Membership Verification.

1. Read state/stories_raw.json and state/sprint.json
2. For EACH story: call getJiraIssue with customfield_10020
3. Verify story ID appears in customfield_10020 with state != "removed"
4. Classify: verified vs excluded_historical
5. Write state/stories_verified.json
6. Report counts and confirm

Read: workflow/zones/zone3-sprint-verification.md
Schema: workflow/state-schemas/stories_verified.json
🚨 CRITICAL: Check customfield_10020 for EVERY story. No shortcuts.
DO NOT proceed to Zone 4 automatically.
```

## Error Handling
- `state/stories_raw.json` missing → FAIL
- JIRA API error per story → retry once, mark as unverified if fails again
- Zero verified stories → WARN, ask user to verify sprint config

## Exit Criteria
- [ ] `state/stories_verified.json` exists
- [ ] Every raw story has a verification result
- [ ] `verified_count + excluded_count = raw total_count`
