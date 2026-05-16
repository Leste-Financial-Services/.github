---
name: leste-ai-skill
description: Domain knowledge and workflows for Leste AI projects based on Azure Functions v4 and OpenAI SDK. Use when working with ToolBox.AI or CeLeste.AI.
---
# Leste AI Skill
This skill covers Leste's AI and document processing utilities.
- **Tech Stack**: Azure Functions v4, .NET 6/8, OpenAI SDK v2.1.
- **Integrations**: Microsoft Graph (SharePoint), Aspose.Words, iText, DocX.
- **Services**: `ToolBox.AI`, `CeLeste.AI`.

## Microsoft Learn Routing

For Azure Functions, Microsoft Graph, Microsoft Entra, Microsoft 365, SDK behavior, permissions, or Azure hosting details, use `system_knowledge/microsoft-learn-skill/SKILL.md` first and load only the matching Microsoft Learn page.

Defaults:
- Graph endpoint: `https://graph.microsoft.com`.
- Prefer Managed Identity + Key Vault for Azure-hosted integrations.
- For Azure DevOps operations, use `https://dev.azure.com/LesteDevOps` with the current user's `az login` session.
