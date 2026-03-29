---
name: status.generate
description: "Generate a weekly status report for your manager by discovering JIRA work bottom-up, gathering Calendar/Slack/Docs context, and assembling a structured report. Accepts sprint number."
user-invocable: true
allowed-tools:
  - mcp__atlassian__jira_search_issues
  - mcp__atlassian__jira_get_issue
  - mcp__google_workspace__get_events
  - mcp__google_workspace__get_drive_file_content
  - mcp__slack__conversations_history
  - mcp__slack__conversations_replies
  - mcp__time__get_current_time
  - Read
  - Write
---

Read the full contents of `skills/status.generate/SKILL.md` and follow all instructions exactly.

$ARGUMENTS
