# Visual Flow — Manager Status Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 1: DISCOVERY & VERIFICATION                                  │
└─────────────────────────────────────────────────────────────────────┘

 User Input
     │ sprint number
     ▼
┌─────────────────────────┐
│  Zone 1                 │ → state/sprint.json
│  Sprint Selection       │   (sprint_id, dates, status)
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  Zone 2                 │ → state/stories_raw.json
│  Raw Data Collection    │   (ALL Stories/Subtasks from JQL)
│  Paginate exhaustively  │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  Zone 3 🚨 CRITICAL     │ → state/stories_verified.json
│  Sprint Verification    │   (customfield_10020 check)
│  Filters false positives│   Typically removes 10-30%
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  Zone 4 🚨 CRITICAL     │ → state/epics_verified.json
│  Hierarchy Resolution   │   state/initiatives.json
│  Story→Epic→Initiative  │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  Zone 5                 │ → state/validation_report.json
│  Validation Gate        │   8 consistency checks
│  PASS or FAIL           │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  Zone 6 🚨 APPROVAL     │ → state/approval.json
│  Human Approval Gate    │
│  User reviews Initiatives│
└────────────┬────────────┘
             │ "yes" → Phase 2
             │ "no"  → restart from Zone 1
             │ "re-verify" → restart from Zone 3

┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 2: CONTEXT, REPORT & DELIVERY                                │
└─────────────────────────────────────────────────────────────────────┘

             ▼
┌─────────────────────────┐
│  Zone 7                 │ → state/initiatives_with_context.json
│  Initiative Context     │   (descriptions, docs, what/why)
└────────────┬────────────┘
             │
             ▼ (parallel with Zone 9)
┌─────────────────────────┐    ┌─────────────────────────┐
│  Zone 8                 │    │  Zone 9                 │
│  Story Status Analysis  │    │  Supplemental Context   │
│  (subtasks, 1.5-2 exp.) │    │  Calendar+Slack+GitHub  │
│  → stories_detailed.json│    │  → context.json         │
└────────────┬────────────┘    └────────────┬────────────┘
             └──────────┬───────────────────┘
                        │
                        ▼
             ┌─────────────────────────┐
             │  Zone 10                │ → state/report_draft.md
             │  Report Assembly        │   (all 10 sections)
             └────────────┬────────────┘
                          │
                          ▼
             ┌─────────────────────────┐
             │  Zone 11 🚨 APPROVAL    │ → state/report_approved.json
             │  Self-Review (x2)       │
             │  + Human Approval       │
             └────────────┬────────────┘
                          │ approved
                          ▼
             ┌─────────────────────────┐
             │  Zone 12                │ → state/delivery.json
             │  Publish & Deliver      │   Google Doc created
             │  New Google Doc         │   artifacts/reports/ saved
             └─────────────────────────┘
                          │
                          ▼
                  📄 Google Doc URL
                  → Share with manager
```

---

## State File Dependencies

```
sprint.json
    ↓
stories_raw.json
    ↓
stories_verified.json
    ↓
epics_verified.json + initiatives.json
    ↓
validation_report.json
    ↓
approval.json
    ↓
initiatives_with_context.json
stories_detailed.json ← (parallel)
context.json          ← (parallel)
    ↓
report_draft.md
    ↓
report_approved.json
    ↓
delivery.json
```
