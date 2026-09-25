# Azure Pipelines templates for MuleSoft

Reusable Azure Pipelines templates that take a Mule application from a pull request to production on CloudHub 2.0, following **GitFlow** and using the **maven-release-plugin** for versioning.

Applications keep a ten-line `azure-pipelines.yml` and delegate the whole flow to a tagged version of this repository. The reference application is [mulesoft-orders-api](https://github.com/brunosouzas/mulesoft-orders-api).

## What runs, and when

| Branch | Stages |
|---|---|
| Pull request, `feature/*`, `release/*`, `hotfix/*` | Build |
| `develop` | Build → publish `x.y.z-SNAPSHOT` to Exchange → deploy **test** |
| `main` | Build → **Maven release** (tag `vx.y.z`, publish to Exchange) → deploy **uat** → deploy **prod** (approval) |

The same artifact published by the release is deployed to uat and prod; nothing is rebuilt per environment.

## Layout

```
templates/
  stages/gitflow.yml         entry point: decides the stages from the branch
  jobs/ci.yml                build and package
  jobs/publish-snapshot.yml  develop → Exchange
  jobs/release.yml           release:prepare release:perform on main
  jobs/deploy.yml            deploy a version from Exchange to CloudHub 2.0
  steps/setup-maven.yml      Java 17, Maven cache, settings.xml from secrets
docs/adr/                    why the templates work this way
```

## Using it

```yaml
resources:
  repositories:
  - repository: templates
    type: github
    endpoint: <your GitHub service connection>
    name: brunosouzas/azure-devops-mulesoft-pipelines
    ref: refs/tags/v1.1.0

extends:
  template: templates/stages/gitflow.yml@templates
  parameters:
    nextVersionBump: minor   # patch | minor | major
```

The application needs:

- `groupId` set to the Anypoint organization ID, and `distributionManagement` pointing to Exchange;
- the `maven-release-plugin` with `tagNameFormat` `v@{project.version}` and `scmCommentPrefix` starting with `[skip ci]`;
- a `cloudhub2Deployment` block reading `deploy.*` properties;
- `deployment/<env>.yaml` for each environment, optionally with a `healthUrl` for the post-deployment smoke test.

The reference application has all of them.

### Plugins and libraries

A Mule plugin or library (`packaging=mule-extension`/`mule-library`) has nothing to deploy to CloudHub 2.0 — pass `deploy: false` to skip straight from build to publishing on Exchange:

```yaml
extends:
  template: templates/stages/gitflow.yml@templates
  parameters:
    deploy: false
```

It still needs `groupId`, `distributionManagement` and the `maven-release-plugin` block above; it does not need `cloudhub2Deployment` or `deployment/<env>.yaml`. See [ADR 7](docs/adr/0007-plugin-library-pipeline-variant.md). The reference is [mule4-circuit-breaker](https://github.com/brunosouzas/mule4-circuit-breaker).

## Azure DevOps setup

| Item | Name | Contents |
|---|---|---|
| Variable group | `vg-00-anypoint-platform` | `ANYPOINT_ORG_ID`, `ANYPOINT_CLIENT_ID`, `ANYPOINT_CLIENT_SECRET` (secret) — a Connected App with Exchange Contributor and Runtime Manager deploy scopes |
| Environments | `test`, `uat`, `prod` | `prod` with an **Approvals** check |
| Service connection | GitHub | used to read this repository and to check out the application |

For the release job to push its commits and tag, the pipeline identity must be allowed to push to `main` (see [ADR 3](docs/adr/0003-release-push-identity.md)).

## Decisions

1. [GitFlow as the branching model](docs/adr/0001-gitflow.md)
2. [The Maven release runs in the pipeline, on main](docs/adr/0002-maven-release-on-main.md)
3. [The pipeline identity may push release commits to main](docs/adr/0003-release-push-identity.md)
4. [MUnit does not run in the public pipeline](docs/adr/0004-munit-not-in-public-ci.md)
5. [Build once, deploy the same artifact everywhere](docs/adr/0005-build-once-deploy-from-exchange.md)
6. [Approvals live on Azure DevOps environments](docs/adr/0006-approvals-on-environments.md)
7. [A `deploy` parameter, not a separate entry point, for plugins and libraries](docs/adr/0007-plugin-library-pipeline-variant.md)

## Versioning of this repository

Templates are released with tags (`v1.0.0`, `v1.1.0`, …). Applications pin a tag, so a change here never alters an application's pipeline until that application chooses to upgrade.

## Licence

[MIT](LICENSE) © Bruno Pinto de Souza
