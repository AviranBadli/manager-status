# Manager Status Update Skill

Pull the latest skills from the manager-status GitHub repo and sync them into the local workspace.

---

## Step 1 — Pull Latest

```bash
git pull origin main
```

If "Already up to date." → tell the user and stop.

If pull fails → show the error and manual instructions:
```
cd ~/Projects/manager-status
git pull origin main
Then re-run /manager-status.update
```

---

## Step 2 — Show What Changed

Parse the git output and list which skill files were updated:

```
📦 Updates pulled from GitHub
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Branch: main
Files changed:
  • skills/status.generate/SKILL.md
  • skills/status-report-template.md
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Skills are now up to date.
```

---

## Step 3 — Sync to Knowledge Base (Optional)

If `--sync-kb` argument is provided, copy updated files to `~/Projects/abe-personal-knowledge-base`:

```bash
REPO=~/Projects/manager-status
KB=~/Projects/abe-personal-knowledge-base

mkdir -p $KB/.cursor/skills/manager-status/generate
mkdir -p $KB/.cursor/skills/manager-status/publish
mkdir -p $KB/.cursor/skills/manager-status/verify

cp $REPO/skills/status.generate/SKILL.md    $KB/.cursor/skills/manager-status/generate/SKILL.md
cp $REPO/skills/status.publish/SKILL.md     $KB/.cursor/skills/manager-status/publish/SKILL.md
cp $REPO/skills/status.verify/SKILL.md      $KB/.cursor/skills/manager-status/verify/SKILL.md
cp $REPO/skills/status-report-template.md   $KB/.cursor/skills/manager-status/status-report-template.md
```

Then fix path references in copied files (replace `guidelines/` with `.cursor/rules/` equivalents).

Report each file synced.

---

## Arguments

`$ARGUMENTS` — Optional. Pass `--sync-kb` to also sync to the knowledge base workspace.
