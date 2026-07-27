# Security Policy

JIVL is a verification tool for third-party Agent Skill content. Its own
security posture matters twice over: as a normal piece of software, and as a
tool that inspects **untrusted, potentially adversarial input** (skill
directories) by design.

## Reporting a Vulnerability

Please do not open a public GitHub issue for a suspected security
vulnerability. Instead:

1. Open a GitHub private security advisory on this repository
   (Security tab -> "Report a vulnerability"), or
2. Contact the Lead Maintainer, Nityam Jigyasu (@nmj18txstate), directly
   through GitHub.

Include: affected version/commit, a minimal reproduction (ideally a crafted
skill directory), expected vs. actual behavior, and impact assessment.

## Scope

In scope:
- Path traversal, symlink escape, or archive-extraction issues when JIVL
  inspects a skill directory.
- Command injection or shell-metacharacter issues in Maven/Gradle build
  invocation.
- Ways a malicious SKILL.md, jivl.yaml, pom.xml, or build.gradle could cause
  JIVL to execute unintended code, read files outside the skill directory,
  or exfiltrate data.
- Registry website issues (XSS, path exposure, unsafe deserialization of
  report files).
- Secret-redaction failures where JIVL prints an unredacted secret in a
  report or log.

Out of scope:
- Vulnerabilities in a skill's own content that JIVL correctly reports as a
  finding (that is the product working as intended, not a JIVL bug).
- Vulnerabilities in third-party dependencies (please report those upstream;
  we will track remediation via dependency updates).

## Design Principles That Bound Our Attack Surface

- JIVL never executes skill-declared shell commands, allowed-tools
  instructions, or SKILL.md content as code.
- JIVL only invokes javac / the Java Compiler API, Maven, or Gradle, and
  only when the user explicitly passes --run-builds.
- Builds run offline by default; --allow-build-network is required to
  permit dependency resolution.
- Process execution uses ProcessBuilder with argument lists, never a
  concatenated shell string.
- The registry website is read-only: it never executes a skill, a build, or
  a script, and never fetches remote URLs.

## Disclosure Timeline

We aim to acknowledge reports within 5 business days and to provide a
remediation plan or timeline within 14 days. Given this is a young,
single-maintainer project, timelines are best-effort, not contractual.
