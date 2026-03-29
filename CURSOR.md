# Manager Status — Cursor Project Instructions

Skill logic lives in the **shared `skills/` directory**. The `.cursor/commands/*.md` files are thin wrappers.

## Available Commands

| Command | What it does |
|---|---|
| `/status-for-manager [sprint]` | Generate full weekly status report |
| `/status.verify [sprint]` | Verify JIRA data before generating |
| `/status.publish` | Create Google Doc from approved draft |
| `/manager-status.update` | Pull latest skills from GitHub |

## Configuration

Create `guidelines/jira-config.md` from `guidelines/config-example.md` before first use.

## Artifact Lifecycle

```
artifacts/reports/status-sprint-{N}-{date}.md
  status: draft      ← created by /status-for-manager
  status: published  ← updated by /status.publish (adds google_doc_url)
```

## To Edit a Skill

Edit `skills/` only — never the wrapper files in `.cursor/commands/`.
