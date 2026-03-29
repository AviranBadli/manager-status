# Zone 10: Report Assembly

## Responsibility
Assemble the complete status report from all state files. Produce every section of the report in the canonical format.

## Input
- `state/sprint.json`
- `state/initiatives_with_context.json`
- `state/stories_detailed.json`
- `state/context.json`
- `skills/status-report-template.md` (canonical format)

## Output
- **File:** `state/report_draft.md`
- **Schema:** frontmatter only

## Execution Steps

### Step 1: Read All State Files
Read all 4 state files plus `skills/status-report-template.md` for exact format.

### Step 2: Determine Initiative Status

For each Initiative, assign one status based on evidence from state files:

| Status | When |
|---|---|
| 🟢 On track | Work progressing, no blockers found |
| 🟡 Pending | Blocker found in Slack/calendar, or story stuck |
| 🟠 Delayed | Behind schedule — note from Slack or story comments |
| 🔄 In Progress | Active work, neutral status |

**Base status on data. Never invent.**

### Step 3: Assemble Each Section

Follow `skills/status-report-template.md` exactly. Sections in order:

1. **Header:** date from `state/sprint.json` created_at
2. **Sprint Info:** sprint name, dates, contributors (unique assignees from stories_detailed)
3. **TL;DR:** one line per Initiative — key + title + status emoji + 1 sentence
4. **Executive Summary:** per Initiative (3-line explanation + sub-area status) + Management Takeaway
5. **Deep Dive:** per Initiative — Epics list + THREE story sections (Completed / In Progress / Backlog)
6. **Highlights:** completed stories from `by_category.completed`
7. **In Progress:** active stories with subtask breakdown
8. **Risks/Blockers:** items from Slack/calendar + any blocked stories
9. **Next Steps:** backlog stories + upcoming work
10. **Extra Context:** "What is it?" + "Why it matters?" from `initiatives_with_context`

### Step 4: Write Draft

Save to `state/report_draft.md` with frontmatter:
```yaml
---
status: draft
sprint: [N]
assembled_at: ISO8601
initiative_count: N
story_count: N
---
```

Then the full report content.

### Step 5: Confirm to User
```
✅ Report assembled:
   Initiatives: [N]
   Stories covered: [N]
   Sections: all 10

State saved: state/report_draft.md
Ready for Zone 11 (Self-Review)?
```

## Zone Trigger Prompt
```
You are in Zone 10: Report Assembly.

1. Read all state files (sprint, initiatives_with_context, stories_detailed, context)
2. Read skills/status-report-template.md for exact section format
3. Determine status for each Initiative from evidence (never invent)
4. Assemble ALL 10 sections in order
5. Write state/report_draft.md
6. Confirm to user

Read: workflow/zones/zone10-report-assembly.md
🚨 Three story sections always: Completed + In Progress + Backlog (never skip)
🚨 Every story needs a 1.5-2 sentence explanation (read from stories_detailed)
DO NOT proceed to Zone 11 automatically.
```

## Exit Criteria
- [ ] `state/report_draft.md` exists
- [ ] All 10 sections present
- [ ] Every Initiative has all three story sections
- [ ] Every story has a plain-language explanation
