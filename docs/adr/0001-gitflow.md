# 1. GitFlow as the branching model

Status: accepted

## Context

Mule applications are usually released on a schedule, promoted through several environments (test, uat, prod) with approvals, and sometimes need a hotfix while the next release is being prepared. The model has to make "what is in production" and "what is being prepared" unambiguous.

## Decision

Use GitFlow: `develop` for integration, `release/x.y.z` to stabilise a release, `main` for released code, `hotfix/x.y.z` for production fixes. The pipeline acts on the branch name (see `templates/stages/gitflow.yml`).

## Consequences

- Clear separation between work in progress and released code; hotfixes do not drag unreleased features into production.
- More branches and back-merges (`main → develop`) than trunk-based development.
- GitFlow's author has noted that teams doing continuous delivery of a single version are better served by simpler flows. If an application deploys to production many times a day, prefer trunk-based development with short-lived branches; this template is for the scheduled-release case.
