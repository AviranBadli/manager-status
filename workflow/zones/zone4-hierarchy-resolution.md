# Zone 4: Hierarchy Resolution 🚨 CRITICAL

## Responsibility
Build the full Story → Epic → Initiative hierarchy. Verify each Epic actually has Stories in the sprint (not just a parent link). Discover the authoritative list of active Initiatives.

## Input
- **File:** `state/stories_verified.json`

## Output
- **File:** `state/epics_verified.json`
- **File:** `state/initiatives.json`
- **Schema:** `state-schemas/epics_verified.json`, `state-schemas/initiatives.json`

## Why This Zone Is Critical
A Story may have an Epic parent that belongs to an Initiative — but other Epics under that Initiative may have no work in the sprint. Zone 4 verifies each Epic has actual verified sprint work before including its Initiative.

## Execution Steps

### Step 1: Build Epic Map
From `stories_verified.json`, extract all unique Epic keys from `parent.key`.

For each Epic: call `getJiraIssue` to get:
- Epic summary, status
- Epic's parent (Initiative key + summary)

### Step 2: Verify Each Epic Has Sprint Work
For each Epic:
```
JQL: project = {PROJECT_KEY} AND parent = {EPIC_KEY} AND issuetype = Story
     AND key IN ({verified_story_keys})
```
Count matching stories. If 0 → Epic has no verified sprint work → exclude.

### Step 3: Resolve Initiatives
From verified Epics, collect unique Initiative keys. For each Initiative:
- Call `getJiraIssue` for Initiative key, summary, status
- Count how many verified Epics (and stories) it has

**Standalone Epics:** Epics with no Initiative parent are noted as standalone.

### Step 4: Write State Files

`state/epics_verified.json`:
```json
{
  "sprint_id": <id>,
  "epics_verified": [
    {
      "key": "JN-XXX", "summary": "...", "status": "...",
      "initiative_key": "JN-YYY",
      "story_count_in_sprint": N,
      "stories": ["JN-A", "JN-B"]
    }
  ],
  "epics_excluded": [ { "key": "...", "reason": "no_sprint_stories" } ],
  "standalone_epics": []
}
```

`state/initiatives.json`:
```json
{
  "sprint_id": <id>,
  "initiatives": [
    {
      "key": "JN-ZZZ", "summary": "...", "status": "...",
      "epic_count": N,
      "story_count": N,
      "epics": ["JN-XXX"]
    }
  ],
  "standalone_epics": [],
  "resolved_at": "ISO8601"
}
```

### Step 5: Confirm to User
```
✅ Hierarchy resolved:
   Epics verified (have sprint work):  [N]
   Epics excluded (no sprint work):    [N]
   Initiatives discovered:             [N]
   Standalone Epics:                   [N]

State saved: state/epics_verified.json + state/initiatives.json
Ready for Zone 5?
```

## Zone Trigger Prompt
```
You are in Zone 4: Hierarchy Resolution.

1. Read state/stories_verified.json
2. Extract all unique Epic keys from verified stories
3. For each Epic: get parent Initiative, verify has sprint stories
4. Build Initiative list (deduplicated)
5. Write state/epics_verified.json and state/initiatives.json
6. Report counts and confirm

Read: workflow/zones/zone4-hierarchy-resolution.md
🚨 CRITICAL: Verify Epics with JQL — do NOT assume an Epic is active just because it's a parent.
DO NOT proceed to Zone 5 automatically.
```

## Exit Criteria
- [ ] `state/epics_verified.json` exists
- [ ] `state/initiatives.json` exists
- [ ] Every Initiative has at least 1 verified Epic
- [ ] Every verified Epic has at least 1 verified Story
