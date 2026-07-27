# JIVL Research Roadmap (Documentation Only — Not Built)

JIVL Research is a possible future direction: reproducible, citable
research artifacts built on JIVL's verification and evidence model. As with
JIVL Academy, **nothing in this section is implemented.**

## Concept

Research software rot and irreproducibility are well-known problems.
JIVL's existing machinery — deterministic reports, JDK/framework
verification, canonical digesting — is a plausible foundation for
reproducibility tooling aimed at JVM-based research software, extended
with research-specific metadata JIVL does not currently model at all.

## Envisioned Components

- **Reproducibility manifests** — an extension of the `jivl.yaml` concept
  capturing exactly what is needed to reproduce a result, not just what is
  needed to verify a skill compiles.
- **Research skill packs** — packaged, verified research code plus the
  metadata below.
- **Citation metadata** — DOI-style or `CITATION.cff`-style metadata
  scoped to the research artifact itself (distinct from citing JIVL the
  tool, per this repository's own `CITATION.cff`).
- **Experiment definitions** — structured description of what was run,
  with what parameters, expecting what outcome.
- **Dataset provenance** — where research data came from, distinct from
  and more detailed than `jivl.yaml`'s general `provenance` block.
- **Dataset licensing** — explicit, since research datasets often carry
  restrictive or field-specific licenses that ordinary Apache-2.0 skill
  content does not.
- **Environment capture** — OS, JVM vendor/build, and relevant native
  library versions, beyond JIVL's current JDK-version-only compatibility
  model.
- **Dependency capture** — a full, pinned dependency graph snapshot, not
  just the top-level declared versions JIVL currently extracts.
- **JVM-version capture** — building on JIVL's existing DECLARED/DETECTED/
  EXECUTED/VERIFIED distinction (`docs/verification-model.md`).
- **Benchmark definitions** — structured performance/behavioral benchmarks
  with defined inputs and measured outputs.
- **Baseline results** — a recorded reference result to compare against.
- **Replication attempts** — a way to record independent replication runs
  and whether they matched the baseline, distinct from JIVL's own
  effectiveness-evaluation baseline/with-skill model.
- **Peer-review state** — explicit status field (e.g., unreviewed,
  under review, reviewed), since research artifacts have a review
  lifecycle ordinary skills do not.
- **Result history** — an append-only history of results across
  environment/dependency changes over time.
- **Artifact digests** — reusing JIVL's existing SHA-256 canonical
  digesting approach (`docs/verification-model.md`, report digest model)
  for research artifacts.
- **Bioinformatics, healthcare research, and scientific-computing
  possibilities** — plausible JVM-heavy domains (e.g., existing JVM-based
  bioinformatics toolkits) where reproducibility tooling has real demand;
  no specific integration is implemented or promised.
- **Defense/public-sector use** — explicitly restricted, if ever pursued,
  to lawful, unclassified, and properly authorized material only; JIVL
  Research is not intended or designed for classified or export-controlled
  workloads.

## Explicitly Not Promised

No timeline, no funding model, and no confirmation this will ever be
built.
