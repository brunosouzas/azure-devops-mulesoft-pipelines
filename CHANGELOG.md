# Changelog

## 1.2.0

- `gitflow.yml`: new `deploy` parameter (default `true`). With `deploy: false`, `deploy_test`/`deploy_uat`/`deploy_prod` are compiled out entirely, for plugins and libraries that only publish to Exchange (see ADR 7).

## 1.1.0

- Deploy job: smoke test after each deployment. Calls `healthUrl` from `deployment/<env>.yaml` and fails unless it reports `status: UP` for the expected environment (retries for up to five minutes).

## 1.0.1

- Release job: scope the checkout token to the whole Git host, so the maven-release-plugin can push when the pom's `<scm>` URL differs from the checkout URL (for example, a `.git` suffix). Without it, `release:prepare` fails with *could not read Username*.

## 1.0.0

- GitFlow stages, Maven release on `main`, CloudHub 2.0 deployments from Exchange, approvals on environments.
