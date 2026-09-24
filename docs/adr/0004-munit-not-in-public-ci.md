# 4. MUnit does not run in the public pipeline

Status: accepted

## Context

MUnit runs on the Mule Enterprise runtime, downloaded from the MuleSoft Enterprise repository. Access requires customer credentials tied to a MuleSoft subscription. This is a personal, public reference project, and using an employer's credentials in it — even as a protected secret or secure file — would mean using their licence outside their business.

## Decision

The public pipeline builds and packages with `-DskipMunitTests`. MUnit runs locally (`mvn clean test`) and is a pull request checklist item.

## Consequences

- The pipeline shows build, release and deployment, but not test execution.
- In a company setting, the fix is simple: store the Enterprise repository credentials in a variable group or secure file and remove `-DskipMunitTests` from `templates/jobs/ci.yml`.
