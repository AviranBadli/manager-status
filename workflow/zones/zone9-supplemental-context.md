# Zone 9: Supplemental Context Gathering

## Responsibility
Gather context from Calendar, Slack, and GitHub for the last 7 days. Identifies decisions made, blockers raised, and key events to enrich the report.

## Input
- `state/approval.json`
- `guidelines/jira-config.md` (user email, Slack channel ID, GitHub repos)

## Output
- **File:** `state/context.json`
- **Schema:** `state-schemas/context.json`

## Execution Steps

### Step 1: Read Config
Get user email, Slack channel ID, GitHub repos, timezone from `guidelines/jira-config.md`.

### Step 2: Calendar — Last 7 Days (parallel with Steps 3 and 4)

```
get_events(user_google_email: {EMAIL}, time_min: [7 days ago], time_max: [today], max_results: 50)
```

For each event, extract:
- Meeting title
- Attendees (names, not emails)
- Whether a decision or blocker was mentioned (scan description)

### Step 3: Slack — Last 7 Days (parallel)

```
conversations_history(channel_id: {TEAM_CHANNEL_ID}, limit: 100)
```

For each message with `thread_ts` → read the FULL thread (not just the top-level message).

Extract:
- Blockers mentioned
- Decisions made
- Items unblocked
- PR reviews or deployments mentioned

### Step 4: GitHub — Recent PRs (parallel, if repos configured)

For each configured repo, check PRs merged or updated in the last 7 days:
- PR title, status (merged/open/draft)
- Link to PR
- Note if it's directly related to a Story in the sprint

### Step 5: Write State File
```json
{
  "sprint_id": <id>,
  "period": { "from": "YYYY-MM-DD", "to": "YYYY-MM-DD" },
  "calendar": {
    "events": [
      { "title": "...", "date": "...", "key_notes": "..." }
    ]
  },
  "slack": {
    "decisions": ["..."],
    "blockers": ["..."],
    "highlights": ["..."]
  },
  "github": {
    "prs_merged": [ { "title": "...", "url": "...", "repo": "..." } ],
    "prs_in_review": []
  },
  "gathered_at": "ISO8601"
}
```

### Step 6: Confirm to User
```
✅ Supplemental context gathered:
   Calendar events: [N]
   Slack messages scanned: [N] (including [N] threads)
   GitHub PRs found: [N]

State saved: state/context.json
Ready for Zone 10?
```

## Zone Trigger Prompt
```
You are in Zone 9: Supplemental Context Gathering.

1. Read guidelines/jira-config.md for email, Slack channel, repos
2. In PARALLEL: fetch Calendar events + Slack history + GitHub PRs (last 7 days)
3. For Slack: read ALL threads (not just top-level messages)
4. Extract decisions, blockers, highlights
5. Write state/context.json
6. Confirm to user

Read: workflow/zones/zone9-supplemental-context.md
🚨 Always read full Slack threads — top-level messages miss most decisions.
DO NOT proceed to Zone 10 automatically.
```

## Exit Criteria
- [ ] `state/context.json` exists
- [ ] Calendar, Slack, GitHub sections populated (or empty if no data/config)
- [ ] Slack threads were read (not just top-level messages)
