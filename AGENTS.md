# azure-devops-mulesoft-pipelines: project context

## Purpose

Reusable Azure Pipelines templates that take a Mule application from a pull request to production on CloudHub 2.0, following **GitFlow** and using the **maven-release-plugin** for versioning.

## Technology

Stack: Azure Pipelines, Maven, Mule 4, CloudHub 2.0
Delivery: E reference
Integration branch: `main`
Data: none
Article: `brunosouzas-content: writing/mulesoft-delivery-azure-devops/`

- Build: `not applicable: templates only; they run inside a consumer's pipeline (mulesoft-orders-api, mule4-circuit-breaker, mule4-circuit-breaker-demo-app)`
- Test: `not applicable: no test suite in this repository; a template change is tested by pointing a consumer's resources.repositories ref at the branch and running its pipeline`
- Run locally: `not applicable`
- Deliver and verify: `nothing is deployed on merge: consumers pin a tag (refs/tags/vX.Y.Z), so a merge changes no pipeline until Bruno tags a version and a consumer moves to it; verify that the links and paths the published articles cite still work`

## Technology declarations

No framework or interpreter version is pinned by the root manifests inspected. Check the task-specific source before selecting a runtime.

These are source declarations, not evidence of installed runtimes. Maven properties may describe build/test dependencies rather than supported runtime minima; unresolved expressions remain inherited until verified.

## Layout and operation sources

Top-level source/documentation directories: `docs`, `templates`.

- [README.md](README.md).

GitHub default branch inspected on 2026-10-06: `main`. Release bases are defined by the project sources, separately from that setting. Build/publish commands mentioned by those sources are context, not authorization.

## Project rules

Before planning, reviewing or changing this project, read [the applicable project rules](rules/README.md).

- Article repository: this repository was made for a published article and keeps what the article published (its code, branches and pipeline). A change here changes the article, so every merge is Bruno's (level 2), also under a delegation.
- Board area: `mulesoft` (Azure Boards, project `brunosouzas`).
- OpenSpec: no: every change takes the small path.
- Versions of this repository are tags (`vX.Y.Z`) that Bruno creates; never create, move or delete one.
