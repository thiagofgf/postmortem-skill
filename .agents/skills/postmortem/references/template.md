# PM-NNN: Incident title

**Status:** Draft | Final
**Incident window:** YYYY-MM-DD HH:MM UTC – YYYY-MM-DD HH:MM UTC
**Last reviewed:** YYYY-MM-DD
**Affected systems:** System or surface list
**Impact classification:** User-visible | Internal | Near miss
**Why this qualifies:** Evidence-backed reason this event crosses the postmortem threshold

## Summary

In two to four paragraphs: what users or operators observed, what invariant was
violated, the actual mechanism, and how the incident ended. State explicitly
when one observation exposed multiple independent incidents.

## User Impact

- Who was affected.
- What they experienced.
- Duration and scope, quantified when evidence permits.
- Data-loss, privacy, availability, or trust impact.
- What was not affected only when a supplied source proves that bounded claim;
  otherwise write `Unknown — evidence required`.

## Detection

How the incident was first noticed, why existing monitoring or tests did or did
not detect it, and which signal ultimately separated the failing layers.

## Timeline

Use UTC and evidence-backed timestamps.

| Time (UTC) | Event | Evidence |
| --- | --- | --- |
| YYYY-MM-DD HH:MM | Detection, decision, mitigation, deploy, or verification | Link or measured source |

## Incident Tracks and Root Causes

### Track A — Descriptive name

**Observed behavior:** What this track looked like externally.

**Root cause:** The technical mechanism and first failing boundary.

**Failure chain:**

When the mechanism is evidenced:

```text
input/state
→ component decision
→ missing or wrong output
→ downstream consequence
→ user-visible result
```

When source contents are absent or the root cause is unresolved, do not invent
those middle steps:

```text
confirmed observation
→ [unknown failing boundary; discriminating probe required]
→ confirmed result
```

**Rejected hypotheses:** Material alternatives that were tested and falsified,
including the evidence that rejected them. If no discriminating evidence was
supplied, use **Open hypotheses** instead and name the probe required; an
untested idea is not rejected.

### Track B — Remove when there was only one independent cause

Repeat the same structure. Do not use a second track for a contributing factor.

## Contributing Factors

Conditions that made the incident possible, harder to detect, or larger in
scope. Examples: duplicated policy across languages, implicit contracts,
historical rows, missing observability, misleading names, deployment ordering,
or tests that asserted counts instead of state.

## Why Tests and Reviews Missed It

Name the exact blind spots. Distinguish absent coverage, impossible fixtures,
mocked-away behavior, stale checkouts, incomplete review lenses, and tests that
passed without exercising the claimed invariant.

## Resolution

What code, schema, configuration, or operational changes stopped the incident.
Separate immediate mitigation from the durable fix.

## Data Repair

What existing data or external state required repair, how the repair was
bounded, and how its result was verified. State `None` only when supplied
evidence proves no repair was needed; otherwise state `Unknown — evidence
required`.

## Production Verification

Evidence that the fix and repair are live. Include environment, release or
commit, relevant API/database/client/disk observations, and negative evidence
only when a supplied source explicitly proves the bounded absence. An unopened
log category does not prove `no errors`; an unrun query does not prove `zero
remaining rows`.

## Preventive Controls

For each control, state the invariant and enforcement point.

| Control | Invariant | Enforcement | Evidence |
| --- | --- | --- | --- |
| Example regression | Current snapshot survives commit | Database trigger + database test | Test link |

## What Went Well

Signals, practices, reviews, or recovery mechanisms that reduced impact or time
to resolution.

## What Went Wrong

Systemic gaps, misleading assumptions, coordination failures, or evidence
mistakes. Keep this blameless and preserve material corrections.

## Follow-up Actions

| Action | Area/owner | Status | Success criteria | Evidence |
| --- | --- | --- | --- | --- |
| Concrete follow-up | System area or named team | Open | Observable completion condition | Link when complete |

Statuses: `Open`, `In progress`, `Done`, `Declined` (with rationale).

## Evidence Gaps

List each claim that could not be verified, the evidence needed to resolve it,
and whether it blocks `Final` status. Write `None` only after checking the
incident window, impact, root causes, rollout, and user-visible healing.

## Related Decisions

- **ADRs:** Relevant architecture records, or `[evidence required: identify ADR]`.
- **FDRs:** Current user-visible behavior records, or `[evidence required: identify FDR]`.
- **Stories:** Implementation and acceptance records, or `[evidence required: identify story]`.
- **Runbooks:** Detection, mitigation, repair, or rollback procedures, or `[evidence required: identify runbook]`.

Never infer record identifiers from project conventions or ambient context; link
only records supplied or opened inside the authorized source scope. In
evidence-skeleton mode, use generic placeholders by record type and do not name
an identifier, even conditionally.

## Evidence

- Production logs or queries.
- Migrations and tests.
- Pull requests and review threads.
- Releases and deployment runs.
- Client or disk verification.

Link sources and state what each proves. Do not place secrets, raw access tokens,
or unnecessary personal data in the record.
