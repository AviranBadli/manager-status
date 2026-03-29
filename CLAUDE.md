# Manager Status — Claude Code Project Instructions

Skill logic lives in the **shared `skills/` directory**. The `.claude/skills/*/SKILL.md` files are thin wrappers.

## Available Skills

| Command | Wrapper | Shared Skill |
|---|---|---|
| `/status-for-manager` | `.claude/skills/status.generate/SKILL.md` | `skills/status.generate/SKILL.md` |
| `/status.verify` | `.claude/skills/status.verify/SKILL.md` | `skills/status.verify/SKILL.md` |
| `/status.publish` | `.claude/skills/status.publish/SKILL.md` | `skills/status.publish/SKILL.md` |
| `/manager-status.update` | `.claude/skills/manager-status.update/SKILL.md` | `skills/manager-status.update/SKILL.md` |

## Configuration

Create `guidelines/jira-config.md` from `guidelines/config-example.md` before first use.

## To Edit a Skill

Edit `skills/` only — never the wrapper files.
