# ADR-0003: Categorized Findings, Not a Single Numeric Score

- **Status:** Accepted
- **Date:** 2026-07-27

## Context

Many skill-quality tools converge on a single number ("87/100"). A single
score is easy to compare at a glance but hides *why* a skill scored what it
did, invites gaming a specific weighted formula, and cannot cleanly
represent "we don't know" (a SKIPPED check) versus "we checked and it's
fine" (PASS) versus "we checked and it's broken" (FAIL).

## Decision

JIVL reports a set of categorized `Finding`s, each with its own status
(PASS/WARN/FAIL/SKIPPED/ERROR), severity (INFO..CRITICAL), and category
(STRUCTURE, SECURITY, JAVA, MAVEN, ...). Aggregate counts are shown, but no
single opaque score is computed or displayed anywhere in the CLI, reports,
badges, or registry.

## Alternatives Considered

- **Single weighted score.** Rejected: weighting choices are inherently
  opinionated and would be far more contestable, and gameable, than a
  transparent finding list.
- **Score plus findings (both).** Considered but rejected for the MVP: a
  score displayed alongside findings tends to become the thing people
  actually look at, undermining the goal of forcing engagement with *why*.
  Revisit only via a future RFC if user feedback strongly demands it.

## Consequences

- Reports and badges are slightly more work to parse for a human than a
  single number would be, judged an acceptable tradeoff for transparency
  and resistance to gaming.
- CI gating uses status thresholds (`--fail-on-warn`, presence of any FAIL)
  rather than a numeric cutoff.
