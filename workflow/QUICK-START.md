# Quick Start — Manager Status Workflow

## Prerequisites

1. `guidelines/jira-config.md` exists (copy from `guidelines/config-example.md`)
2. JIRA/Atlassian MCP configured in Cursor
3. Google Workspace MCP configured (for Zone 12)
4. Slack MCP configured (recommended for Zone 9)

---

## Run the Workflow

### Phase 1: Discovery (Zones 1-6)

Run each zone sequentially. Each zone will confirm when done and ask if you're ready for the next.

**Zone 1 — Sprint Selection:**
```
You are in Zone 1: Sprint Selection & Resolution.
Read and follow: workflow/zones/zone1-sprint-selection.md
```

**Zone 2 — Raw Data:**
```
You are in Zone 2: Raw Sprint Data Collection.
Read and follow: workflow/zones/zone2-raw-data-collection.md
```

**Zone 3 — Verification 🚨 (critical):**
```
You are in Zone 3: Sprint Membership Verification.
Read and follow: workflow/zones/zone3-sprint-verification.md
```

**Zone 4 — Hierarchy 🚨 (critical):**
```
You are in Zone 4: Hierarchy Resolution.
Read and follow: workflow/zones/zone4-hierarchy-resolution.md
```

**Zone 5 — Validation:**
```
You are in Zone 5: Pre-Execution Validation Gate.
Read and follow: workflow/zones/zone5-validation-gate.md
```

**Zone 6 — Approval 🚨:**
```
You are in Zone 6: Human Approval Gate.
Read and follow: workflow/zones/zone6-human-approval-gate.md
```

At Zone 6, **review the Initiative list carefully**. This is your last chance to catch errors before expensive Phase 2 work.

---

### Phase 2: Report Generation (Zones 7-12)

After approving in Zone 6:

**Zone 7 — Context:**
```
You are in Zone 7: Initiative Context Gathering.
Read and follow: workflow/zones/zone7-initiative-context.md
```

**Zone 8 — Story Analysis:**
```
You are in Zone 8: Story Status Analysis.
Read and follow: workflow/zones/zone8-story-status-analysis.md
```

**Zone 9 — Supplemental:**
```
You are in Zone 9: Supplemental Context Gathering.
Read and follow: workflow/zones/zone9-supplemental-context.md
```

**Zone 10 — Assembly:**
```
You are in Zone 10: Report Assembly.
Read and follow: workflow/zones/zone10-report-assembly.md
```

**Zone 11 — Review 🚨:**
```
You are in Zone 11: Self-Review & Human Approval.
Read and follow: workflow/zones/zone11-self-review-approval.md
```

At Zone 11, read the full report and request any changes before approving.

**Zone 12 — Publish:**
```
You are in Zone 12: Publish & Deliver.
Read and follow: workflow/zones/zone12-publish-deliver.md
```

---

## Resuming from a Zone

If a zone fails or the session is interrupted, restart from the failed zone. All state is in `state/` and will be read by the zone on startup.

Example — resume from Zone 8:
```
You are in Zone 8: Story Status Analysis.
State from previous zones is in state/.
Read and follow: workflow/zones/zone8-story-status-analysis.md
```

---

## Typical Timeline

| Phase | Zones | Duration | API Calls |
|---|---|---|---|
| Phase 1 | 1-6 | 5-10 min | ~100-150 |
| Phase 2 | 7-12 | 10-20 min | ~50-100 |
| **Total** | 1-12 | **~20-30 min** | **~200** |

Zones 3, 4, and 8 are the API-heavy zones.
