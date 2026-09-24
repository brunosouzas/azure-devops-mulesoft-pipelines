# 2. The Maven release runs in the pipeline, on main

Status: accepted

## Context

Versions must be reproducible: a released version is built once, from a tagged commit, and never rebuilt with different code. Developers editing versions by hand leads to duplicates and SNAPSHOTs in production.

## Decision

When a change reaches `main`, the pipeline runs `release:prepare release:perform` (maven-release-plugin):

1. `prepare` removes `-SNAPSHOT`, commits, creates tag `v<version>`, then commits the next `-SNAPSHOT` (bump chosen by the `nextVersionBump` parameter).
2. `perform` checks out the tag in a clean directory and runs `deploy`, publishing the release to Anypoint Exchange.

The plugin commits with the prefix `[skip ci]`, so its own pushes do not trigger the pipeline again.

## Consequences

- Nobody edits versions by hand; the tag is the source of truth for what was released.
- The pipeline needs to push to `main` (see ADR 3).
- Azure Pipelines checks out a commit in detached HEAD; the job switches to the real branch before releasing, because the plugin must commit on a branch.
