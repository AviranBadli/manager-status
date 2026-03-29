# Status Report Guidelines — Rules and Methodology

## Core Methodology: Bottom-Up Discovery

**ALWAYS discover Initiatives bottom-up from sprint Stories — NEVER top-down.**

```
Sprint Stories → Parent Epics → Parent Initiatives
```

An Initiative is "active" ONLY if it has at least 1 Story or Subtask with work in the sprint. If an Initiative exists in JIRA but has no sprint work → exclude it from the report.

---

## Critical Rules

### 1. Status Must Come from JIRA — Never Invent

- ONLY use statuses from JIRA fields
- NEVER infer status from descriptions or comments
- NEVER use statuses other than: ✅ Done, 🟢 On track, 🟡 Pending, 🟠 Delayed, 🔄 In Progress, 📋 Planned

### 2. Sprint ID ≠ Sprint Number

- Sprint Number = "Sprint N" (human label, e.g., "Sprint 15")
- Sprint ID = the integer in the JIRA API (e.g., `23`)
- Use `customfield_10020` for sprint verification, not just the sprint name
- Always look up Sprint ID from config — never guess

### 3. Verify Every Initiative Before Including

For each Initiative in the report:
- Verify at least 1 Story or Subtask has `sprint = {SPRINT_ID}` via `customfield_10020`
- If verification fails → remove the Initiative from the report

### 4. Three Story Sections — Always

The Deep Dive MUST have three sections for each Initiative:
- ✅ Completed This Sprint
- 🔄 In Progress
- 📋 Backlog (Planned This Sprint)

Never collapse them. Never skip an empty section — write "None this sprint" if empty.

### 5. Every Story Needs an Explanation

Never show a Story as just its title. Every Story in the report needs:
- 1.5-2 sentences explaining what was done and why it matters
- Read the Story description and any linked docs before writing
- Write in plain language the manager can understand without JIRA context

### 6. Extra Context = Transformation, Not Description

The "What is it?" / "Why does it matter?" section must:
- Explain what changes (before vs. after)
- Quantify impact where possible (e.g., "reduces deployment from 2 hours to 5 minutes")
- Avoid vague language ("improves quality", "makes things better")

### 7. Document Creation Protocol

- NEVER create or overwrite a Google Doc without explicit user approval
- Always generate the report in chat first
- Self-review twice before showing to user
- Ask "Shall I create a Google Doc?" — wait for "yes"
- Always create a NEW doc — never edit existing ones
- Naming: "Weekly Status - Sprint [X] - [Date]"

---

## Self-Review Protocol (Mandatory)

Before showing the report to the user:
1. Read the entire report **twice**
2. For each item: "Would the manager understand this without JIRA context?"
3. If unclear → rewrite
4. Check: all Stories covered? statuses accurate? TL;DR matches Deep Dive?

---

## Common Mistakes to Avoid

| Mistake | Correct approach |
|---|---|
| Including Initiatives not verified in sprint | Always validate bottom-up from Stories |
| Hardcoding Sprint IDs in skills | Read from `guidelines/jira-config.md` |
| Only Completed + In Progress sections | THREE sections: Completed, In Progress, Backlog |
| Inventing status names | ONLY use the 6 approved statuses |
| Missing Story explanations | Every Story needs 1.5-2 sentence explanation |
| Creating doc without approval | Generate in chat first, ask before creating |
| Overwriting existing Google Doc | Always create NEW doc |
| TL;DR not matching report body | TL;DR must match what's in Executive Summary |

---

## Data Sources Priority

1. **JIRA** — primary source for all work status (Stories, Epics, Initiatives)
2. **Google Calendar** — meetings, decisions, blockers from last 7 days
3. **Slack** — team discussions, threads (always read full threads, not just top-level messages)
4. **Google Docs** — design docs, planning docs linked from JIRA
5. **GitHub** — PRs merged or in review for technical context
