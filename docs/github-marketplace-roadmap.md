# GitHub Marketplace Roadmap

**Status: not published.** `jivl-github-action` is a composite-action
*prototype* built from checked-in source in this repository. It is not
published to GitHub Marketplace, has no release tag, and this MVP does not
claim otherwise anywhere in its docs, CLI, or CI output.

## What Exists Today

- `jivl-github-action/action.yml` — composite action definition.
- Local-use example: reference the action via a relative path or a full
  commit SHA from a workflow in *this* repository.
- The action builds JIVL from checked-in source and invokes the CLI; it
  does not reimplement verification logic.

## What Publishing Would Require (Not Done Yet)

1. A tagged release (e.g., `v0.1.0`) with a stable `action.yml` contract.
2. Marketplace listing metadata (icon, color, category, description) that
   does not overstate maturity — "early-stage MVP" language would need to
   carry over, not disappear at the marketing layer.
3. A decision on whether the action continues to build-from-source per
   invocation (slower, always current) or fetches a prebuilt `jivl-cli.jar`
   from a release asset (faster, needs a release/artifact pipeline).
4. Documentation of fork-PR limitations: `GITHUB_TOKEN` permissions and
   secret availability differ for PRs from forks; the action must degrade
   gracefully (e.g., skip any step needing elevated permissions) rather
   than fail confusingly.
5. A versioning/compatibility policy so consumers pinning `@v1` get
   non-breaking updates, per normal GitHub Action conventions.
6. Security review of the action's own supply chain (pinned action
   dependencies, e.g. `actions/checkout` by SHA, not just a tag).

## Non-Goals For This MVP

- No Marketplace submission.
- No published release/tag.
- No claim of "used by" adoption numbers.

Future remote-action usage and artifact-upload examples are illustrated
(with local, non-published semantics) in `jivl-github-action/examples/`.
