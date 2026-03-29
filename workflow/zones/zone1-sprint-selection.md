# Zone 1: Sprint Selection & Resolution

## Responsibility
Define exactly which sprint the workflow operates on and resolve it to a JIRA Sprint ID.

## Input
- User interaction (sprint number or name)
- `guidelines/jira-config.md` (sprint ID mapping, cloud ID, project key)

## Output
- **File:** `state/sprint.json`
- **Schema:** `state-schemas/sprint.json`

## Execution Steps

### Step 1: Read Config
Read `guidelines/jira-config.md` to get: cloud ID, project key, sprint ID mapping.

### Step 2: Ask User for Sprint
```
"Which sprint do you want the status report for?"
```
Accepted formats: "Sprint 15", "15", sprint ID number directly.

### Step 3: Resolve Sprint ID
Look up the sprint number in the config mapping.
If not found → ask user to add it to `guidelines/jira-config.md` first and stop.

### Step 4: Fetch Sprint Metadata from JIRA
Call `getJiraIssue` on any story in the project with `customfield_10020` to extract sprint dates.

Or use the sprint search API if available.

Extract:
- Sprint start date
- Sprint end date
- Sprint status (active / closed / future)
- JIRA internal sprint name

### Step 5: Write State File
Create `state/sprint.json`:
```json
{
  "sprint_name": "Sprint N",
  "sprint_number": N,
  "sprint_id": <jira-id>,
  "project_key": "YOUR_PROJECT",
  "cloud_id": "...",
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "status": "active",
  "jira_name": "...",
  "created_at": "ISO8601"
}
```

### Step 6: Confirm to User
```
✅ Sprint resolved:
   Sprint [N] — ID [sprint_id]
   Dates: [start] → [end]
   Status: [active/closed]

State saved: state/sprint.json
Ready for Zone 2?
```

## Zone Trigger Prompt
```
You are in Zone 1: Sprint Selection & Resolution.

1. Read guidelines/jira-config.md for cloud_id, project_key, sprint ID mapping
2. Ask the user which sprint to report on
3. Look up Sprint ID from config
4. Fetch sprint metadata from JIRA (dates, status)
5. Write state/sprint.json
6. Confirm to user and ask if ready for Zone 2

Read full instructions: workflow/zones/zone1-sprint-selection.md
Use schema: workflow/state-schemas/sprint.json
DO NOT proceed to Zone 2 automatically.
```

## Error Handling
- Sprint not in mapping → ask user to add it, stop
- JIRA API error → retry once, then fail with clear message
- Invalid sprint ID → report error and ask to verify

## Exit Criteria
- [ ] `state/sprint.json` exists with all required fields
- [ ] Sprint ID verified via JIRA
- [ ] User confirmed correct sprint
