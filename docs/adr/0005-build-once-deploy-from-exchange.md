# 5. Build once, deploy the same artifact everywhere

Status: accepted

## Context

Rebuilding for each environment risks deploying something different from what was tested.

## Decision

Artifacts are published to Anypoint Exchange once (the SNAPSHOT from `develop`, the release from `main`). Deployment stages never rebuild: they run `mule:deploy -Dmule.artifact=dummy.jar -DmuleDeploy`, which tells CloudHub 2.0 to deploy the version already in Exchange. Per-environment settings (replicas, vCores, runtime) come from `deployment/<env>.yaml` in the application repository.

## Consequences

- uat and prod receive exactly the artifact produced by the release.
- An environment's sizing is reviewed as code, in the application's pull requests.
