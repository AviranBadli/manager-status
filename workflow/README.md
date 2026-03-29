# Manager Status Workflow — Zone-Based Architecture

A **zone-based, state-driven** workflow for generating weekly manager status reports. Each zone has a single responsibility, all state is persisted to JSON files, and the workflow is fully resumable.

**Status:** Phase 1 (Zones 1-6) ✅ | Phase 2 (Zones 7-12) ✅

---

## Core Principles

1. **Zone-Based Execution** — each zone does exactly one thing
2. **Explicit State** — all data persisted to JSON files between zones
3. **No Implicit Memory** — zones read only from state files, never assume context
4. **Resumable** — can restart from any zone if something fails
5. **Human-in-the-Loop** — explicit approval gates at Zone 6 and Zone 11
6. **Fail Fast** — validation errors stop the workflow immediately

---

## Workflow Phases

### Phase 1: Discovery & Verification (Zones 1-6)
Discovers which Initiatives have work in the sprint and gets user approval before any expensive work.

| Zone | Responsibility | Output |
|---|---|---|
| 1 | Sprint Selection & Resolution | `state/sprint.json` |
| 2 | Raw Sprint Data Collection | `state/stories_raw.json` |
| 3 | Sprint Membership Verification 🚨 | `state/stories_verified.json` |
| 4 | Hierarchy Resolution 🚨 | `state/epics_verified.json` + `state/initiatives.json` |
| 5 | Pre-Execution Validation Gate | `state/validation_report.json` |
| 6 | Human Approval Gate 🚨 | `state/approval.json` |

### Phase 2: Context, Report & Delivery (Zones 7-12)
Gathers deep context, assembles the report, self-reviews, and publishes.

| Zone | Responsibility | Output |
|---|---|---|
| 7 | Initiative Context Gathering | `state/initiatives_with_context.json` |
| 8 | Story Status Analysis | `state/stories_detailed.json` |
| 9 | Supplemental Context (Calendar/Slack/GitHub) | `state/context.json` |
| 10 | Report Assembly | `state/report_draft.md` |
| 11 | Self-Review & Human Approval 🚨 | `state/report_approved.json` |
| 12 | Publish & Deliver | `state/delivery.json` + Google Doc |

---

## State Directory

All workflow state lives in `state/` within the working directory:

```
state/
├── sprint.json                    ← Zone 1
├── stories_raw.json               ← Zone 2
├── stories_verified.json          ← Zone 3
├── epics_verified.json            ← Zone 4
├── initiatives.json               ← Zone 4
├── validation_report.json         ← Zone 5
├── approval.json                  ← Zone 6
├── initiatives_with_context.json  ← Zone 7
├── stories_detailed.json          ← Zone 8
├── context.json                   ← Zone 9
├── report_draft.md                ← Zone 10 + 11
├── report_approved.json           ← Zone 11
└── delivery.json                  ← Zone 12
```

---

## Zone Definitions

- [Zone 1 — Sprint Selection](zones/zone1-sprint-selection.md)
- [Zone 2 — Raw Data Collection](zones/zone2-raw-data-collection.md)
- [Zone 3 — Sprint Verification](zones/zone3-sprint-verification.md)
- [Zone 4 — Hierarchy Resolution](zones/zone4-hierarchy-resolution.md)
- [Zone 5 — Validation Gate](zones/zone5-validation-gate.md)
- [Zone 6 — Human Approval Gate](zones/zone6-human-approval-gate.md)
- [Zone 7 — Initiative Context](zones/zone7-initiative-context.md)
- [Zone 8 — Story Status Analysis](zones/zone8-story-status-analysis.md)
- [Zone 9 — Supplemental Context](zones/zone9-supplemental-context.md)
- [Zone 10 — Report Assembly](zones/zone10-report-assembly.md)
- [Zone 11 — Self-Review & Approval](zones/zone11-self-review-approval.md)
- [Zone 12 — Publish & Deliver](zones/zone12-publish-deliver.md)

---

## Why Zones 3 and 4 Are Critical

**Zone 3** checks `customfield_10020` for every story. JIRA's JQL `sprint = X` returns historical assignments — stories that were once in the sprint but were moved out. Without Zone 3, those stories pollute the report.

**Zone 4** verifies Epics actually have sprint work (via JQL), not just a parent link. Initiatives with Epics that have no sprint work are excluded.

In practice, these two zones filter out 10-30% of the initial JQL results.

---

## Quick Navigation

- **[Quick Start Guide](QUICK-START.md)** — step-by-step for first run
- **[Visual Flow](VISUAL-FLOW.md)** — ASCII diagram of the full workflow
- **[Orchestration Guide](ORCHESTRATION.md)** — how to run, resume, and troubleshoot
- **[State Schemas](state-schemas/)** — JSON schemas for all state files
