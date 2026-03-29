# Status Publish Skill

Create a new Google Doc from an approved status report draft. Always get explicit approval before creating anything.

All output must be in English.

---

## Input

`$ARGUMENTS` — Optional file path to a local draft. If not provided, uses the most recent `artifacts/reports/` file with `status: draft`.

---

## Step 1 — Load the Draft

Read the draft file (or auto-detect most recent draft in `artifacts/reports/`).

Read `guidelines/jira-config.md` for the user's Google email and report naming convention.

---

## Step 2 — Show Full Content for Approval

Display the complete report content:

```
📄 Report Ready to Publish
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Sprint: [N]
File:   [file path]

[Full report content here — every section, no truncation]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Create a new Google Doc with this content?
Proposed title: "Weekly Status - Sprint [N] - [Date]"
(yes / no / edit first)
```

**Do NOT call any Google API until the user says "yes".**

If the user requests edits → apply them, re-display, ask again.

---

## Step 3 — Create Google Doc

After explicit approval, create a new Google Doc using Google Workspace MCP:
- Title: `Weekly Status - Sprint [N] - [Date]`
- Body: the full report content formatted as HTML
- **NEVER overwrite an existing doc — always create NEW**

---

## Step 4 — Update Artifact and Report Success

Update the draft frontmatter:
```yaml
status: published
google_doc_url: https://docs.google.com/document/d/{doc_id}/edit
```

Output:
```
✅ Google Doc created!

Title:  "Weekly Status - Sprint [N] - [Date]"
Link:   https://docs.google.com/document/d/{doc_id}/edit
File:   artifacts/reports/status-sprint-[N]-{date}.md

Share this link with your manager.
```

---

## Step 5 — On Failure

If Google MCP call fails:
```
⚠️ Google Doc creation failed.

Error: {error}

Your report is saved locally at: {file path}

Manual option:
1. Copy the content from {file path}
2. Go to docs.google.com → New Document
3. Title: "Weekly Status - Sprint [N] - [Date]"
4. Paste the content
```
Stop — do not retry automatically.
