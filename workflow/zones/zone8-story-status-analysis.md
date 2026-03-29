# Zone 8: Story Status Analysis

## Responsibility
For each verified Story in the sprint, get full details and all subtasks. Build the complete story-level status data used in the Deep Dive section.

## Input
- `state/stories_verified.json`
- `state/epics_verified.json`

## Output
- **File:** `state/stories_detailed.json`
- **Schema:** `state-schemas/stories_detailed.json`

## Execution Steps

### Step 1: Read State Files

### Step 2: For Each Verified Story

```
1. Fetch full Story: getJiraIssue(key, expand: "description,comments")
   - Read description (used for the 1.5-2 sentence explanation)
   - Get assignee

2. Get ALL Subtasks:
   JQL: project = {PROJECT_KEY} AND parent = {STORY_KEY} ORDER BY status DESC
   
   For each subtask: key, summary, status

3. Count subtasks: X Done / Y In Progress / Z Backlog/Todo

4. Write the 1.5-2 sentence explanation:
   - Read the Story description
   - Write in plain language the manager understands (no JIRA jargon)
   - Explain WHAT was done/is being done and WHY it matters
```

### Step 3: Classify Each Story

| Category | Condition |
|---|---|
| `completed` | Story status = Done |
| `in_progress` | Story status = In Progress or In Review |
| `backlog` | Story status = To Do / Open / Backlog |

### Step 4: Write State File
```json
{
  "sprint_id": <id>,
  "stories": [
    {
      "key": "PROJ-XXX",
      "summary": "...",
      "status": "Done",
      "category": "completed",
      "assignee": "...",
      "explanation": "1.5-2 sentence plain-language explanation",
      "epic_key": "PROJ-YYY",
      "initiative_key": "PROJ-ZZZ",
      "subtasks": {
        "total": 5,
        "done": 3,
        "in_progress": 1,
        "backlog": 1,
        "items": [ { "key": "...", "summary": "...", "status": "..." } ]
      }
    }
  ],
  "by_category": {
    "completed": ["PROJ-A", "PROJ-B"],
    "in_progress": ["PROJ-C"],
    "backlog": ["PROJ-D"]
  },
  "analyzed_at": "ISO8601"
}
```

### Step 5: Confirm to User
```
✅ Story analysis complete:
   Completed:   [N] stories
   In Progress: [N] stories
   Backlog:     [N] stories

State saved: state/stories_detailed.json
Ready for Zone 9?
```

## Zone Trigger Prompt
```
You are in Zone 8: Story Status Analysis.

1. Read state/stories_verified.json and state/epics_verified.json
2. For EACH verified story: fetch full details + all subtasks
3. Write 1.5-2 sentence plain-language explanation per story
4. Classify: completed / in_progress / backlog
5. Write state/stories_detailed.json
6. Confirm to user

Read: workflow/zones/zone8-story-status-analysis.md
🚨 Every story needs an explanation — never leave it as just the JIRA title.
DO NOT proceed to Zone 9 automatically.
```

## Exit Criteria
- [ ] `state/stories_detailed.json` exists
- [ ] Every verified story has an `explanation` field
- [ ] Subtask counts are populated
- [ ] `by_category` totals match total story count
