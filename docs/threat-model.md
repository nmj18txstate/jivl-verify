# Threat Model

JIVL inspects **untrusted, potentially adversarial** input (a skill
directory authored by someone JIVL's operator may not know or trust). This
document describes what JIVL defends against, what it does not, and why.

## Assets

1. The machine running JIVL (CLI operator's workstation, or CI runner).
2. Secrets/credentials on that machine (env vars, SSH keys, cloud creds).
3. The integrity of the verification report itself (must not be gameable).
4. The registry website's host and any visitor's browser session.

## Threat: A Skill Tries to Escape Its Own Directory

- **Vector:** relative paths with `../`, absolute paths, encoded traversal
  sequences, or symlinks inside the skill directory pointing outside it.
- **Mitigation:** all file-reference resolution in `jivl-core`'s safe-path
  utilities rejects references (and, for symlinks, resolved targets) that
  escape the skill root. Findings are raised rather than the read silently
  failing or silently succeeding outside the boundary.

## Threat: A Skill Tries to Get JIVL to Execute Something

- **Vector:** shell commands embedded in `SKILL.md`, an `allowed-tools`
  declaration, a script referenced from the skill, or a Maven/Gradle plugin
  that runs arbitrary code during a build.
- **Mitigation:** JIVL never executes `SKILL.md` content, never executes
  `allowed-tools` declarations, and never runs arbitrary Maven/Gradle goals
  declared by the skill (Maven runs a fixed `verify` goal; Gradle runs a
  fixed `test` task). Build execution overall is only reachable via the
  explicit `--run-builds` flag, is off by default, and is documented as
  running repository-controlled build logic (i.e., plugins *can* still run
  arbitrary code — that risk is inherent to running someone else's Maven/
  Gradle project at all, and is why the flag is opt-in and prominently
  documented in `README.md`'s security warning, not something JIVL can
  fully neutralize).

## Threat: A Skill Tries to Exfiltrate Data or Reach the Network

- **Vector:** a build script or plugin that reads environment variables/
  credential files and phones them home; a `SKILL.md` instruction telling
  an agent to read `.ssh` or cloud credential directories.
- **Mitigation:** `jivl-security` includes deterministic rules flagging
  credential-directory access, environment-variable enumeration, and
  network exfiltration patterns as static findings (best-effort, not
  execution-time prevention). Builds run **offline by default**;
  `--allow-build-network` is required to permit dependency resolution, and
  even then JIVL does not proxy or inspect that traffic — it is documented,
  not blocked, at that point.

## Threat: A Skill Tries to Hide Malicious Content

- **Vector:** bidirectional Unicode control characters, invalid UTF-8,
  base64/encoded payloads followed by decode-and-execute, deeply nested
  reference chains designed to bury content from casual review.
- **Mitigation:** `jivl-security` includes a specific hidden-Unicode-control
  rule; structural validation reports invalid UTF-8 safely (without
  crashing the parser) and flags deep reference chains as a warning.

## Threat: Secrets Leak Through JIVL's Own Output

- **Vector:** a finding's "evidence" field, or a captured build stdout/
  stderr, contains what looks like a real secret.
- **Mitigation:** evidence is minimally quoted and redacted by design;
  captured process output is bounded and reviewed for the same patterns
  `jivl-security` already detects before being written into a report.

## Non-Threats / Explicit Non-Goals

- JIVL is not a sandbox or an execution isolation technology. It does not
  claim to safely contain a malicious build the way a container or VM
  might; `--run-builds` is a real subprocess with real capabilities,
  clearly labeled as requiring the operator's own trust judgment.
- JIVL does not attempt to detect every possible obfuscation technique.
  Static analysis has fundamental limits; a determined, sophisticated
  attacker can construct content that evades pattern-based detection. This
  is why every report and badge explicitly states that passing checks does
  not prove complete safety.
- JIVL does not authenticate skill authorship claims; `jivl.yaml`
  provenance fields are attestations by the declarer, not independently
  proven facts (see `docs/provenance-policy.md`).

## Registry-Specific Threats

- **Vector:** a malformed or adversarially crafted `jivl-report.json`
  placed in `registry-data/`.
- **Mitigation:** the registry validates every report against
  `schemas/jivl-report.schema.json` before rendering it, logs and skips
  malformed files rather than crashing or rendering partial/undefined
  content, and never executes anything derived from a report (it is
  read-only by construction — see ADR-0006).
- **Vector:** a report field containing a local absolute filesystem path
  or raw stack trace that could leak host information.
- **Mitigation:** DTOs exposed by the registry API/UI are hand-built to
  exclude local paths; a global exception handler returns safe error
  responses, never a raw stack trace.
