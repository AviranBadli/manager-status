# Manager Status

Cursor and Claude Code skills for generating weekly status reports for your manager — bottom-up JIRA discovery, multi-source context gathering, structured output, and one-click Google Doc creation.

Works with both **Cursor** (`.cursor/`) and **Claude Code** (`.claude/`).

## Quick Start

```
# Generate status report for a sprint
/status-for-manager [sprint-number]

# Verify data before generating
/status.verify [sprint-number]

# Publish approved report to Google Doc
/status.publish

# Keep skills up to date
/manager-status.update
```

## How It Works

```
/status-for-manager [N]
  ↓
Resolves Sprint ID from config
  ↓
Discovers active Initiatives (bottom-up: Stories → Epics → Initiatives)
  ↓
Reads Initiative/Epic descriptions + linked docs
  ↓
Reads subtask breakdown per Story
  ↓
Gathers Calendar + Slack + GitHub context
  ↓
Assembles structured report (TL;DR → Executive Summary → Deep Dive → Extra Context)
  ↓
Self-review (reads report twice, rewrites unclear items)
  ↓
Shows report in chat → user approves
  ↓
/status.publish → creates new Google Doc
```

## Skill Reference

| Skill | Command | Description |
|---|---|---|
| Generate | `/status-for-manager [N]` | Full report generation for sprint N |
| Verify | `/status.verify [N]` | Verify JIRA data and config before generating |
| Publish | `/status.publish` | Create Google Doc from approved draft |
| Update | `/manager-status.update` | Pull latest skills from GitHub |

## Report Structure

Every report includes:

| Section | Content |
|---|---|
| Sprint Info | Sprint number, dates, active contributors |
| TL;DR | One-line Initiative status summary |
| Executive Summary | Initiative overview + sub-area status + Management Takeaway |
| Deep Dive | Full Epic + Story breakdown per Initiative |
| Highlights | Completed work this sprint |
| In Progress | Active work with subtask breakdown |
| Risks / Blockers | Issues requiring attention |
| Next Steps | Upcoming work |
| Extra Context | Initiative explainers (What is it? Why does it matter?) |

## Configuration

Copy `guidelines/config-example.md` to `guidelines/jira-config.md` and fill in your values:

| Field | How to find |
|---|---|
| Cloud ID | Ask Cursor: "What is my Atlassian Cloud ID?" |
| Sprint IDs | Ask Cursor: "What are Sprint IDs for project [KEY]?" |
| Slack Channel ID | From Slack URL: `.../client/T.../CXXXXXXXXXX` |
| Google Doc IDs | From Doc URL: `.../document/d/DOC_ID/edit` |

`guidelines/jira-config.md` is gitignored — your account IDs stay local.

## File Structure

```
skills/                               ← SHARED source of truth (edit here)
├── status-report-template.md         ← canonical output format
├── status.generate/SKILL.md          ← full report generation logic
├── status.publish/SKILL.md           ← Google Doc creation
├── status.verify/SKILL.md            ← pre-flight config check
└── manager-status.update/SKILL.md    ← self-update from GitHub

.claude/                              ← Claude Code thin wrappers
└── skills/{name}/SKILL.md

.cursor/                              ← Cursor thin wrappers
├── commands/{name}.md
└── skills/manager-status/SKILL.md

guidelines/
├── status-guidelines.md              ← methodology, rules, common mistakes
├── config-example.md                 ← config template (copy → jira-config.md)
└── jira-config.md                    ← your config (gitignored)

artifacts/reports/                    ← report drafts (gitignored)

CLAUDE.md / CURSOR.md                 ← project instructions per tool
```

> **To update a skill:** edit `skills/` only. Both Cursor and Claude Code pick it up automatically.

## Key Rules (Built Into Skills)

- **Bottom-up discovery only** — Initiatives included only if they have Stories in the sprint
- **Never invent statuses** — only use the 6 approved statuses from the legend
- **Self-review mandatory** — report is read twice before showing to user
- **Never create Google Doc without approval** — always ask first
- **Every Story needs an explanation** — 1.5-2 sentences, not just the JIRA title
