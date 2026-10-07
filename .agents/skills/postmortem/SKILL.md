---
name: postmortem
description: Use whenever a production or staging incident, near miss, data-integrity failure, security/privacy exposure, cross-surface divergence, rollback, repeated regression, or materially misleading diagnosis needs to be documented, updated, or audited. Creates evidence-based blameless PM-NNN records, maintains docs/postmortems/INDEX.md, and keeps incident history distinct from ADRs, FDRs, stories, changelogs, and runbooks.
---

# Postmortems

Preserve how an incident happened, how it was detected and resolved, and which
system controls now prevent recurrence. A postmortem is a durable learning
record, not a performance review and not a victory narrative.

## Classify first

Create a postmortem when one or more of these is true:

- users saw unavailable, missing, duplicated, corrupted, leaked, or misleading
  data;
- production or staging violated an explicit product, security, privacy,
  durability, or deployment invariant;
- a near miss could have caused material user or data harm;
- detection or diagnosis took materially longer because observability, tests,
  documentation, or ownership boundaries were misleading;
- several defects or independent failure chains appeared as one symptom;
- a rollback, emergency migration, data repair, or coordinated multi-repository
  response was required;
- the same defect shape escaped more than once.

Do not create one for an ordinary bug with low impact and an obvious local fix.
Capture that in the issue, story, or pull request instead.

When one observation exposed multiple independent causes, keep them in one
postmortem only when the shared detection and response are themselves part of
the lesson. Give each incident track its own root cause and contributing
factors; never force one explanation over unrelated mechanisms.

## Keep record types separate

- **Postmortem:** what happened, impact, response, causes, evidence, learning,
  and follow-up controls.
- **ADR:** why a cross-cutting architecture exists now.
- **FDR:** current user-visible behavior and feature-local rationale.
- **Story:** scoped implementation work, decisions, and acceptance criteria.
- **Runbook:** repeatable operational procedure for detection, mitigation, or
  recovery.
- **Changelog/release note:** what shipped, without incident analysis.

A postmortem may reveal that an ADR, FDR, story, plan, or runbook is stale. Link
and update those records separately; do not make the postmortem impersonate all
of them.

## Evidence standard

Write from evidence, not memory:

1. Read repository instructions and `docs/postmortems/INDEX.md` (or the
   postmortem location the repository instructions name instead).
2. Read the incident's related story, FDRs, ADRs, migrations, tests, runbooks,
   pull requests, deployment records, logs, database state, and release
   evidence narrowly.
3. Confirm the current production state before saying the incident is resolved.
4. Label facts, hypotheses, rejected hypotheses, and remaining unknowns.
5. Normalize timestamps to UTC in the timeline; include the original timezone
   when it explains operator experience.
6. Cite repository-relative sources. For external dashboards or releases, use
   stable URLs and state exactly what each proves.
7. Prefer measurements that distinguish layers: database row, emitted event,
   API response, client index, disk state. A green exit code alone is not
   evidence.

A source document can prove prior intent, not current behavior. A passing test
can prove a contract, not a production rollout. A release can prove deployment,
not that the user-visible symptom healed. Use the evidence appropriate to each
claim.

## Source scope and no-fabrication gate

Before drafting, write an internal evidence inventory: each source available,
which claim it can prove, and which claims remain unsupported.

- When the user supplies files, URLs, logs, queries, or an explicit source list,
  use only those sources unless they explicitly authorize broader discovery.
- When the task says inputs are absent, facts not supplied are evidence gaps, or
  discovery is out of scope, do not scan the ambient repository, use remembered
  project history, or infer identifiers from nearby files.
- Never invent incident times, impact counts, filenames, migration numbers, PRs,
  releases, environments, account counts, query results, or verification claims.
  Write `[evidence required]` or describe the gap instead.
- Negative claims need evidence too. Do not write "no alert fired", "no user
  reported it", "no data was lost", "nothing else was affected", or "monitoring
  was absent" merely because the prompt did not mention those things.
- A prompt saying evidence categories exist (for example, "we have logs and
  releases") does not reveal their contents. Cite only supplied contents; list
  the unopened source as evidence needed.
- A plausible mechanism is still a hypothesis. Call it a root cause only when a
  cited source or measured reproduction identifies the first failing boundary.
  Otherwise write `Root cause: unresolved` and list the discriminating probe.
- Do not revive a hypothesis a source marks as falsified. Preserve it under
  `Rejected hypotheses` with the evidence that rejected it.
- Memory, summaries, and comments are discovery aids, not evidence. Re-open and
  verify the underlying source before citing their claims.
- Auto-injected repository instructions and session context may constrain how
  work is performed, but they are not incident evidence unless the task's source
  scope authorizes them. Do not cite identifiers or facts learned only from that
  ambient context.
- Do not guess related-record identifiers. When an ADR, FDR, story, runbook, PR,
  migration, test, or release was not supplied or opened within the authorized
  source scope, write `[evidence required: identify related record]`.

When no source contents are supplied, enter **evidence-skeleton mode**. Preserve
only facts stated in the request, with bounded paraphrases. Leave causal steps,
contributing factors, missed-detection explanations, resolution, repair,
production verification, controls, and related identifiers as explicit evidence
gaps. Do not fill an unresolved failure chain with a plausible implementation;
write `confirmed observation → [unknown boundary; probe required] → confirmed
result`. A short factual Draft is better than a complete-looking narrative whose
mechanism was guessed.

Before saving, perform an unsupported-claim scrub:

1. Search for negative or absolute terms such as `no`, `none`, `never`, `zero`,
   `did not`, `only`, `every`, `unaffected`, `not required`, and `not affected`.
2. Search causal verbs such as `caused`, `emitted`, `deleted`, `queried`,
   `excluded`, `prevented`, `deployed`, and `verified`. A plausible positive
   mechanism requires evidence just as much as a negative claim.
3. Search identifiers such as `ADR-NNN`, `FDR-NNN`, work-item keys, PR numbers,
   migration numbers or filenames, release tags, commit hashes, test names, and
   URLs. Except for the new PM identifier allocated from an authorized index,
   every identifier must come from an authorized, opened source.

For each occurrence, cite a source that proves the bounded claim or rewrite it
as unknown. Silence in the inputs proves only that the claim is unsupported.
Apply the same scrub to `Rejected hypotheses`: without evidence that
discriminates, move an item to `Open hypotheses` and name the probe instead of
calling it rejected.

The scrub is an **edit gate**, not a report. Apply every revision it finds to the
postmortem before saving. Repeat until no unsupported occurrence remains. Do not
emit a separate scrub appendix that lists known defects while leaving them in
the record, and do not claim the scrub passed when it still proposes revisions.
In evidence-skeleton mode, sections such as Contributing Factors, Why Tests and
Reviews Missed It, controls, What Went Well/Wrong, and follow-up implementation
details contain only explicit evidence gaps and discriminating probes unless the
request itself supplies the fact. Do not add `may`, `likely`, `suggests`, example
function names, candidate filters, proposed test names, assumed owners, or
monitoring gaps: those still fabricate a mechanism. Related-record notes list
only generic record types and the question each must answer; they do not invent
candidate identifiers, APIs, migrations, or missing documents.

`Final` requires evidence for the incident window, user impact, every stated
root cause, the durable fix, preventive controls, production rollout, and
user-visible healing, plus
a completed review of the record. If any of those is missing, the record remains
`Draft` even when the prose is otherwise complete.

## Blameless analysis

Describe system conditions and decision context:

- what information was available at the time;
- which assumptions were reasonable, unstated, or later falsified;
- which boundaries allowed the defect to escape;
- why tests, reviews, monitoring, or documentation did not catch it;
- which incentives or coupling made the unsafe path easier than the safe path.

Do not attribute the incident to a person's carelessness, intelligence, or
intent. Individuals may appear in the timeline as actors, never as root causes.
"Human error" is a stopping point, not an explanation.

## Operations

### Create

1. Use the next unused three-digit ID from `docs/postmortems/INDEX.md` and
   existing filenames.
2. Create `docs/postmortems/PM-NNN-kebab-case-title.md`.
3. Read `references/template.md` and preserve all required sections. Remove an
   optional subsection only when it truly does not apply.
4. Add exactly one index row in the same change.
5. Start as `Draft`. Mark `Final` only when the incident window, user impact,
   every stated cause, durable resolution, preventive controls, production
   rollout, and user-visible healing are evidence-backed and the record has
   passed review.
6. Keep open follow-ups in the action table; a Final postmortem may have open
   actions when their ownership and success criteria are explicit.

### Update

Update a postmortem when evidence changes, a root-cause statement is corrected,
a follow-up ships, or production verification becomes available.

- Correct facts directly, but preserve consequential prior misdiagnoses in
  `Rejected hypotheses` or `What went wrong`; erasing them destroys the lesson.
- Update action status with evidence, not prose such as "probably fixed".
- Refresh `Last reviewed` and the index row when status or title changes.
- Do not append a chronological changelog. Integrate current facts into the
  relevant section and keep the timeline about the incident itself.

### Audit

Compare selected postmortems with current code, tests, decisions, releases, and
operational evidence. Report:

- **confirmed:** record still matches evidence;
- **drifted:** cause, control, status, or follow-up no longer matches reality;
- **incomplete:** required evidence or sections are missing;
- **duplicate:** another postmortem covers the same incident;
- **unresolved:** evidence cannot distinguish competing explanations.

Audit is read-only unless the user also asks to update records.

## Record format

Use `references/template.md`. Required top-level sections are:

1. Summary
2. User Impact
3. Detection
4. Timeline
5. Incident Tracks and Root Causes
6. Contributing Factors
7. Why Tests and Reviews Missed It
8. Resolution
9. Data Repair
10. Production Verification
11. Preventive Controls
12. What Went Well
13. What Went Wrong
14. Follow-up Actions
15. Evidence Gaps
16. Related Decisions
17. Evidence

Every record states `Why this qualifies` in one evidence-backed sentence near
the top: name the threshold from **Classify first** that makes the event worth a
durable postmortem. For a near miss, identify the credible material harm and the
boundary that prevented it; absence of user impact is not itself the reason to
skip the record.

For a near miss, `User Impact` states that no user impact occurred and describes
the credible potential impact. For a single-cause incident, keep one incident
track rather than manufacturing several.

## Index format

`docs/postmortems/INDEX.md` contains one sorted table:

```markdown
| ID | Incident | Status | Incident window | Last reviewed |
| --- | --- | --- | --- | --- |
| [PM-001](PM-001-example.md) | Short incident name | Final | 2026-08-17–2026-08-19 | 2026-08-19 |
```

Statuses:

- **Draft:** investigation or production evidence is incomplete.
- **Final:** incident window, user impact, every stated cause, durable resolution,
  preventive controls, production rollout, and user-visible healing are
  evidence-backed, and the
  record has passed review. Open follow-ups are allowed when explicitly tracked.

## Completion checks

- The event meets the postmortem threshold, and `Why this qualifies` names the
  evidence-backed reason.
- The title and ID are unique and indexed exactly once.
- Status matches the available evidence.
- Impact is user-centered and quantified where possible.
- Timeline timestamps and ordering are evidence-backed.
- Independent failure chains are not collapsed into one cause.
- Root causes explain mechanisms; contributing factors explain escape and
  amplification.
- Rejected hypotheses and material corrections are preserved.
- Resolution is distinguished from data repair and from production verification.
- Preventive controls name the invariant and the enforcement point.
- Follow-ups have status, success criteria, and evidence or a clearly named
  owner/system area.
- Related ADRs, FDRs, stories, migrations, tests, PRs, releases, and runbooks
  are linked rather than copied.
- The language is blameless and contains no unsupported certainty.
- The unsupported-claim scrub was applied as edits and ended with zero known
  violations; no separate audit file records unfixed defects in the PM.
- Every required repository gate passes.
