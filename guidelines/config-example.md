# JIRA & Services Configuration — Example

Copy this file to `guidelines/jira-config.md` and fill in your values.
`jira-config.md` is gitignored — your account IDs and personal info stay local.

---

## Manager Info

```
manager_name: YOUR_MANAGER_NAME
manager_title: YOUR_MANAGER_TITLE
manager_email: YOUR_MANAGER_EMAIL@company.com
```

## User Info

```
user_name: YOUR_NAME
user_email: YOUR_EMAIL@company.com
user_jira_id: YOUR_JIRA_USERNAME
user_google_email: YOUR_EMAIL@company.com
```

## JIRA Configuration

```
cloud_id: YOUR_ATLASSIAN_CLOUD_ID
project_key: YOUR_PROJECT_KEY
project_name: Your Project Name
jira_base_url: https://YOUR_ORG.atlassian.net
```

**Find your Cloud ID:** Ask Cursor: "What is my Atlassian Cloud ID?"

### Sprint ID Mapping

⚠️ Sprint ID ≠ Sprint Number. Find Sprint IDs from JIRA URL (`...sprint=XX`) or ask Cursor.

```
sprints:
  - number: 15
    id: YOUR_SPRINT_15_ID
    name: Sprint 15
  - number: 16
    id: YOUR_SPRINT_16_ID
    name: Sprint 16
  - number: N
    id: YOUR_SPRINT_N_ID
    name: Sprint N
  - number: 18
    id: YOUR_SPRINT_18_ID
    name: Sprint 18
```

## Slack Configuration

```
team_channel_id: YOUR_SLACK_CHANNEL_ID
team_channel_name: "#your-team-channel"
```

**Find your Channel ID:** In Slack browser URL: `.../client/TXXXXX/CXXXXXXXXXX` → copy `CXXXXXXXXXX`

## Google Docs (Optional — for live content)

```
google_docs:
  - name: Design Doc
    id: YOUR_GOOGLE_DOC_ID
  - name: Planning Doc
    id: YOUR_GOOGLE_DOC_ID_2
```

**Find a Doc ID:** From URL: `docs.google.com/document/d/DOC_ID/edit` → copy `DOC_ID`

## GitHub Repos (Optional)

```
github_repos:
  - repo: YOUR_ORG/repo-name
    purpose: Main application
  - repo: YOUR_ORG/repo-2
    purpose: Infrastructure
```

## Report Settings

```
timezone: YOUR_TIMEZONE        # e.g., Asia/Jerusalem, America/New_York
language: English
report_naming: "Weekly Status - Sprint [X] - [Date]"
```

---

## Quick Setup with Cursor

Just ask Cursor:
```
Help me set up my manager-status config.

My manager is [NAME] ([email]).
My JIRA project is [PROJECT_KEY].
My team Slack channel is #[channel-name].

Please:
1. Find my Atlassian Cloud ID
2. Find the Sprint IDs for my project
3. Find the Slack channel ID
4. Create guidelines/jira-config.md for me
```
