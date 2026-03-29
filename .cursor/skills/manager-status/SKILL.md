# Manager Status — System Entry Point

All actual skill logic lives in the shared `skills/` directory. Cursor commands are thin wrappers that load from there.

## Available Commands

| Command | Shared Skill | What it does |
|---|---|---|
| `/status-for-manager [N]` | `skills/status.generate/SKILL.md` | Generate full status report for sprint N |
| `/status.verify [N]` | `skills/status.verify/SKILL.md` | Verify JIRA data before generating |
| `/status.publish` | `skills/status.publish/SKILL.md` | Create Google Doc from approved draft |
| `/manager-status.update` | `skills/manager-status.update/SKILL.md` | Pull latest skills from GitHub |

## Artifacts

All reports saved to `artifacts/reports/status-sprint-{N}-{date}.md`

## Configuration

Edit `guidelines/jira-config.md` (from `guidelines/config-example.md`) with your Cloud ID, Sprint IDs, Slack channel, and Google Doc IDs.
