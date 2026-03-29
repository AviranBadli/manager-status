# Zone 5: Pre-Execution Validation Gate

## Responsibility
Run consistency checks on all state files before presenting the Initiative list to the user. Fail fast on bad data.

## Input
- `state/sprint.json`
- `state/stories_verified.json`
- `state/epics_verified.json`
- `state/initiatives.json`

## Output
- **File:** `state/validation_report.json`
- **Schema:** `state-schemas/validation_report.json`

## Validation Checks

Run all 8 checks. Any FAIL = workflow stops.

| # | Check | Pass Condition |
|---|---|---|
| 1 | Sprint state exists | `state/sprint.json` present and valid |
| 2 | Stories verified | `stories_verified.json` has > 0 verified stories |
| 3 | All Epics have stories | Every Epic in `epics_verified` has `story_count_in_sprint > 0` |
| 4 | All Initiatives have Epics | Every Initiative has `epic_count > 0` |
| 5 | No orphan stories | Every verified story maps to a known Epic |
| 6 | No duplicate Initiatives | Initiative keys are unique in `initiatives.json` |
| 7 | Story → Epic → Initiative chain intact | Every verified story traces to an Initiative or standalone Epic |
| 8 | Total counts consistent | Sum of stories per Initiative = total verified stories |

## State File Output

```json
{
  "sprint_id": <id>,
  "overall_result": "PASS",
  "checks": [
    { "id": 1, "name": "Sprint state exists", "result": "PASS" },
    { "id": 2, "name": "Stories verified", "result": "PASS", "count": N },
    ...
  ],
  "failed_checks": [],
  "summary": {
    "initiatives_count": N,
    "epics_count": N,
    "stories_count": N
  },
  "validated_at": "ISO8601"
}
```

## Confirm to User

If PASS:
```
✅ Validation PASSED (8/8 checks)

Summary:
  Initiatives: [N]
  Epics:       [N]
  Stories:     [N]

State saved: state/validation_report.json
Ready for Zone 6 (Human Approval)?
```

If FAIL:
```
❌ Validation FAILED

Failed checks:
  - Check [N]: [name] — [what went wrong]

Fix: [specific instruction]

Do NOT proceed to Zone 6 until validation passes.
```

## Zone Trigger Prompt
```
You are in Zone 5: Pre-Execution Validation Gate.

1. Read all 4 state files (sprint, stories_verified, epics_verified, initiatives)
2. Run all 8 consistency checks
3. Write state/validation_report.json with results
4. If ALL pass → confirm to user and offer Zone 6
5. If ANY fail → list failures with fix instructions, stop

Read: workflow/zones/zone5-validation-gate.md
🚨 DO NOT proceed to Zone 6 if any check fails.
DO NOT proceed to Zone 6 automatically even if all pass.
```

## Exit Criteria
- [ ] `state/validation_report.json` exists
- [ ] `overall_result = "PASS"`
- [ ] All 8 checks have results
