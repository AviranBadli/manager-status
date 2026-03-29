# Zone 2: Raw Sprint Data Collection

## Responsibility
Fetch ALL Stories and Subtasks that claim to belong to the sprint. No verification yet.

## Input
- **File:** `state/sprint.json`

## Output
- **File:** `state/stories_raw.json`
- **Schema:** `state-schemas/stories_raw.json`

## Execution Steps

### Step 1: Read Sprint State
Read `state/sprint.json`. Fail if missing (Zone 1 must complete first).

### Step 2: Fetch ALL Stories and Subtasks via JQL

```
project = {PROJECT_KEY} AND sprint = {SPRINT_ID} AND issuetype IN (Story, Sub-task) ORDER BY status DESC
```

**CRITICAL:** Use pagination — fetch ALL pages until `isLast = true`. Never cap at 100.

For each Story/Subtask collect:
- `key`, `summary`, `status`, `issuetype`
- `parent.key` and `parent.summary` (Epic link)
- `assignee.displayName`
- `description` (first 500 chars)
- `customfield_10020` (sprint membership — stored raw for Zone 3)

### Step 3: Write State File
```json
{
  "sprint_id": <id>,
  "total_count": N,
  "stories": [ { "key": "...", "summary": "...", "status": "...", ... } ],
  "pagination_info": { "total_pages": N, "final_is_last": true },
  "fetched_at": "ISO8601"
}
```

### Step 4: Confirm to User
```
✅ Raw data collection complete:
   Stories/Subtasks fetched: [N]
   Pages: [N] — complete ✓

⚠️ This is raw data — not yet verified for actual sprint membership.
State saved: state/stories_raw.json
Ready for Zone 3?
```

## Zone Trigger Prompt
```
You are in Zone 2: Raw Sprint Data Collection.

1. Read state/sprint.json for sprint_id and project_key
2. Fetch ALL Stories/Subtasks via JQL (paginate until isLast=true)
3. Collect key, summary, status, parent, assignee, customfield_10020
4. Write state/stories_raw.json
5. Report total count and confirm

Read: workflow/zones/zone2-raw-data-collection.md
Schema: workflow/state-schemas/stories_raw.json
🚨 DO NOT verify customfield_10020 here — that is Zone 3's job.
DO NOT proceed to Zone 3 automatically.
```

## Error Handling
- `state/sprint.json` missing → FAIL, Zone 1 must run first
- Partial pagination → FAIL, must fetch all pages
- Zero results → WARN but continue

## Exit Criteria
- [ ] `state/stories_raw.json` exists
- [ ] `pagination_info.final_is_last = true`
- [ ] `total_count` matches array length
