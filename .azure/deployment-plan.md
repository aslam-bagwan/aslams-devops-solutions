## Plan: Azure DevOps Bootstrap Pipelines

Status: In Progress

Scope
- Create Azure DevOps YAML build pipelines for the .NET API and Angular client.
- Generate Dockerfiles and .dockerignore files for the API and Angular client.
- Keep the pipelines bootstrap-safe because the application folders are currently empty.
- Exclude deployment, database migration, and infrastructure apply stages from this first implementation slice.

Architecture
- Independent pipeline files for API PR validation and main builds.
- Independent pipeline files for client PR validation and main builds.
- Run on self-hosted Azure DevOps agents via a shared pool variable.
- Read common settings, service connection names, and image metadata from variable groups.
- Main pipelines build and optionally push Docker images when an ACR service connection is configured.

Files
- azure-pipelines/api-service/build-pr.yml
- azure-pipelines/api-service/build-main.yml
- azure-pipelines/client-ng-app/build-pr.yml
- azure-pipelines/client-ng-app/build-main.yml
- api-service/Dockerfile
- api-service/.dockerignore
- client-ng-app/Dockerfile
- client-ng-app/.dockerignore

Decisions
- Use .NET 8 SDK and Node 20 as stable defaults.
- Detect missing project files, test assets, package manifests, and Dockerfiles and warn instead of failing for bootstrap-only repos.
- Use self-hosted agents with Docker capability instead of Microsoft-hosted images.
- Use variable groups for shared settings and app-specific build variables.
- Keep ACR push optional behind a Docker registry service connection variable.

Validation
- Check YAML parses cleanly with PowerShell YAML parsing if available.
- Spot-check file presence and key trigger/stage structure.

Next
- Add independent database migration and deployment pipelines after app scaffolding exists.