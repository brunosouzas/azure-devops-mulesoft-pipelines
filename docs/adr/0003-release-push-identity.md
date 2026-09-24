# 3. The pipeline identity may push release commits to main

Status: accepted

## Context

`main` is protected: changes arrive through reviewed pull requests. The Maven release plugin, however, pushes two commits and a tag directly to `main`. Branch protection and an automated release conflict.

## Options

1. **Let the pipeline identity bypass the protection on `main` only.** On GitHub, add the Azure Pipelines app to the ruleset's bypass list; on Azure Repos, grant the build service "Bypass policies when pushing" on `main`.
2. **Release on the `release/*` branch** and merge the result into `main` through a PR. Keeps `main` fully protected, but the tag is created on a branch that is later merged, and a failed PR leaves an orphan tag.
3. **Push with a personal token or SSH key** stored as a secret. Works, but ties releases to a person and adds a secret to rotate.

## Decision

Option 1. The pipeline uses the token it already has (`persistCredentials: true` on checkout) and only the release job pushes. Human changes to `main` still require a pull request.

## Consequences

- No extra secret for Git access.
- The bypass must be scoped to `main` and to the pipeline identity, and reviewed like any other permission.
