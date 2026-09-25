# 7. A `deploy` parameter, not a separate entry point, for plugins and libraries

Status: accepted

## Context

`gitflow.yml` was built for Mule applications: `ci` and `release`/`snapshot` publish the artifact, and `deploy_test`/`deploy_uat`/`deploy_prod` push it to CloudHub 2.0. A Mule plugin or library (`packaging=mule-extension`, no `cloudhub2Deployment`, no `deployment/<env>.yaml`) only needs the first half — build, publish SNAPSHOT from `develop`, Maven release from `main` — and has nothing to deploy.

## Decision

Add a `deploy` boolean parameter to `gitflow.yml`, default `true`. The `deploy_test` stage, and the `deploy_uat`/`deploy_prod` pair, are wrapped in `${{ if eq(parameters.deploy, true) }}:` template conditionals, so with `deploy: false` those stages are compiled out of the pipeline entirely rather than appearing as skipped. `ci`, `snapshot` and `release` are unaffected either way.

A plugin repository's `azure-pipelines.yml` stays the same shape as an application's — `extends: templates/stages/gitflow.yml@templates` with one extra line, `deploy: false` — instead of pointing at a second template file.

## Consequences

- Applications keep the exact same pipeline: the default is `true`, and every app already pinned to a tag (`v1.1.0` and earlier) never sees this parameter at all.
- One entry point and one ADR set to read, instead of two templates that would drift apart over time.
- `environments` (test/uat/prod Azure DevOps environments) is accepted but unused when `deploy: false` — harmless, since the stages that would read it are never compiled in.
- The mixing of an app-shaped template with a library use case is implicit in the parameter name rather than in the file structure; anyone extending `gitflow.yml` for deploy-stage changes needs to keep the `deploy: false` case in mind, or the two conditional blocks it appears in.
