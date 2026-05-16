---
name: microsoft-learn-skill
description: Router for Microsoft Learn knowledge used by Leste development agents. Use for Azure, Microsoft Graph, Microsoft Entra, Azure CLI, Azure DevOps CLI, Azure Functions, App Service, Service Bus, Key Vault, Application Insights, and Microsoft 365 integration questions.
---
# Microsoft Learn Skill

Use this skill to understand Microsoft cloud services and implementation details from Microsoft Learn.

## Token-Efficient Workflow

1. Classify the Microsoft area: Azure DevOps, Azure CLI/auth, Microsoft Graph, Entra ID, Functions, App Service, Service Bus, Key Vault, Application Insights, or SDK.
2. Open only the matching Microsoft Learn page or command reference.
3. Prefer official Learn docs over memory or third-party snippets.
4. Capture only the actionable command/API pattern needed for the current task.
5. Cite the Learn URL in notes or final explanation when the user asks for sources.

## Core Learn Index

| Topic | Use | Microsoft Learn URL |
|---|---|---|
| Azure CLI interactive user login | User credential authentication via `az login` | `https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli-interactively` |
| Azure DevOps CLI | Organization-level commands and defaults | `https://learn.microsoft.com/en-us/cli/azure/devops?view=azure-cli-latest` |
| Azure DevOps CLI auth | PAT vs `az login`; prefer `az login` for user credentials | `https://learn.microsoft.com/en-us/azure/devops/cli/log-in-via-pat?view=azure-devops` |
| Microsoft Graph overview | Graph endpoint and Microsoft 365/Entra data model | `https://learn.microsoft.com/en-us/graph/overview` |
| Microsoft Graph permissions | Delegated/application permissions and consent model | `https://learn.microsoft.com/en-us/graph/permissions-overview` |
| Microsoft Graph SDK .NET | SDK usage for .NET integrations | `https://learn.microsoft.com/en-us/graph/sdks/sdks-overview` |
| Azure Functions overview | Serverless scenarios, triggers, hosting, monitoring | `https://learn.microsoft.com/en-us/azure/azure-functions/functions-overview` |
| Azure Functions triggers/bindings | Event sources and bindings | `https://learn.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings` |
| Azure Service Bus | Messaging patterns and queues/topics | `https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview` |
| Azure Key Vault | Secret/certificate/key management | `https://learn.microsoft.com/en-us/azure/key-vault/general/overview` |
| Managed identities | Azure resource authentication without secrets | `https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview` |
| Application Insights | Application monitoring and telemetry | `https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview` |

## Leste Defaults

- Azure DevOps organization: `https://dev.azure.com/LesteDevOps`.
- Authentication: use the current user's credentials through `az login`.
- Graph endpoint: `https://graph.microsoft.com`.
- Prefer Managed Identity and Key Vault over secrets in code or config files.
- For automation, prefer service connections/workload identity over PATs where supported.

## Stop Conditions

- If a task requires tenant, subscription, resource group, app registration, or project name and it is not known, ask the user instead of guessing.
- If Learn guidance conflicts with a Leste rule, escalate the conflict and do not proceed silently.
