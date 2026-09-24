# 6. Approvals live on Azure DevOps environments

Status: accepted

## Context

Production deployments need a human decision, and the approver should not have to watch the pipeline.

## Decision

Each deployment job targets an Azure DevOps **environment** (`test`, `uat`, `prod`). `prod` has an *Approvals* check; the pipeline pauses until an approver accepts or rejects. Approvers can act in the Azure DevOps portal, or from Slack or Microsoft Teams through the Azure Pipelines apps, which post the pending approval to a channel.

## Consequences

- Approval rules are configured once per environment, not in each application's YAML.
- The audit trail (who approved, when) stays in Azure DevOps.
