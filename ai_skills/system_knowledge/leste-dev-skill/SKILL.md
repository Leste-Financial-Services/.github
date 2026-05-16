---
name: leste-dev-skill
description: Master development router for Leste DEV_SKILLS. Use for software engineering tasks, backend/frontend changes, architecture decisions, and when selecting domain skills with minimal token usage.
---
# Leste Dev Skill

This is the master development router for Leste projects. It coordinates engineering rules, domain skills, and context loading.

## CLI Session Bootstrap

At the start of every new CLI session, before applying development rules:

1. At least once per calendar day, check whether the local `DEV_SKILLS` repository has updates from its remote.
   - Use the configured remote for `D:\Source\Devops\IAProjects\DEV_SKILLS`.
   - Prefer `git fetch` + behind/ahead comparison before reading large files.
   - Store the last successful check date in a local, non-committed marker such as `.leste-ai/devskills-last-check.txt` or `memory/devskills-last-check.txt` in the project/session workspace.
2. If updates exist on `origin/main`, inform the user that the DEV_SKILLS skill repository has remote updates.
3. Force a refresh/pull of the local skill repository before continuing.
   - Prefer `git pull --ff-only` or an equivalent safe fast-forward update.
   - If local uncommitted changes, conflicts, or non-fast-forward updates block the update, stop and report the condition instead of overwriting local work.
4. After updating `DEV_SKILLS`, update the project view of the skill.
   - Projects configured by `setup-leste-ai.ps1` normally use a junction at `<project>/ai_skills`; the pull in `DEV_SKILLS` refreshes those projects automatically.
   - If a project has a copied `ai_skills` folder instead of a junction/link, rerun `setup-leste-ai.ps1` or recreate the link before continuing.
5. Reread this `leste-dev-skill/SKILL.md` and any referenced workflow files needed for the current request.
6. Apply the refreshed rules in the current CLI session.

## Token-Efficient Workflow

Always follow `ai_skills/workflows/token_efficient_context.md`.

1. Classify the request before loading details.
2. Read only the relevant router or `SKILL.md`.
3. Load detailed rules on demand.
4. Ask one clarifying question when route or inputs are ambiguous.
5. Keep responses concise; cite paths instead of pasting long rules.

## Engineering Flow

1. Read memory summary and recent relevant entries.
2. Check `config/stack_preferences.md`.
3. Check `ai_skills/project_knowledge/leste_projects_index.md` to find existing code that may already satisfy the demand.
4. If the central index shows a likely match, read that repository's `project_knowledge/README.md` and only the relevant detail file.
5. Create/link Azure DevOps issue before code changes when operating under Leste project rules.
   - Organization: `https://dev.azure.com/LesteDevOps`.
   - Auth: use current user credentials via `az login`; never store or request secrets.
   - Always use work item type **Issue** as the mandatory Leste development work item.
   - Assign the Issue to the currently logged-in user from `az account show --query user.name -o tsv`.
   - Add 1 to 3 category tags based on the activity/technology, for example `Backend`, `Frontend`, `API`, `MCP`, `IA`, `C#`, `Python`, `VUE.JS`, `REACT`, `Javascript`, `Management`, `PMO`, `Skill`, `C++`, `C`, `Mobile`, `Responsive`, `SQL`.
   - Fill the initial estimate/Effort before implementation starts.
   - Create a local non-committed `ISSUE-<id>-discussion.md` log and append user responses, assistant steps, commands, important outputs, validations, files changed, and final summary as the CLI session progresses.
   - At completion, fill actual time spent/Completed Work and Finish Date, then copy the local log into the Issue Discussion as one or more comments.
6. Check whether the Azure DevOps Board can represent the planned Issue workflow.
   - Identify stages planned for the Issue from the request, acceptance criteria, checklist, subtasks, or linked work items.
   - If the Board does not have columns/states for those stages, organize the Board so the Issue can be tracked through them.
   - Prefer adding/configuring Board columns for visual workflow; change process states only when project governance requires it.
   - Do not delete or rename existing Board stages without explicit approval.
   - If permissions are missing, record the missing stages in the Issue discussion and ask a project/process administrator to add them.
7. Check DevOps/deployment readiness for the target repository before proposing or implementing deployable changes.
   - Pipeline check: look only for lightweight signals such as `azure-pipelines*.yml`, `.azure-pipelines/`, `.github/workflows/`, or documented CI/CD notes in `project_knowledge/`.
   - If no pipeline is found, ask the user whether to create **one Azure DevOps pipeline** for the repository before adding pipeline files.
   - Azure environment check: look for existing deployment target/environment signals such as Azure resource documentation, IaC files, app settings docs, `project_knowledge/`, or Azure DevOps environment references.
   - If no Azure deployment environment exists or is documented, ask the user whether they want to create one before provisioning or wiring deployment.
   - Use current user credentials via `az login`; do not request/store/print PATs, passwords, tokens, or connection strings.
   - Keep this check token-efficient: inspect repository root/config and project knowledge first; do not scan the full codebase unless needed.
8. Enforce API/frontend security before Azure deploy.
   - API created or updated cannot proceed to Azure deploy open to production.
   - Validate a security layer: Microsoft Entra ID/JWT, API key/subscription key, Managed Identity/API Management, or another approved mechanism.
   - If no API security exists, stop and ask what security mechanism to implement before deploy.
   - For frontend/screens, verify whether authentication exists. If it may be an open site, ask the user to confirm they want it open.
   - If frontend authentication will be implemented, ask whether to use another existing frontend authentication flow as a reference.
   - When authorized, inspect only relevant project knowledge `.md` from matching frontends before implementing auth.
9. Use branch-first Git flow.
   - Work in a new feature/Issue branch or an existing branch tied to the Issue; do not work directly on `main`.
   - Before starting, switching branches, pulling updates, merging/rebasing, or preparing push, check for local uncommitted changes.
   - If uncommitted code exists, alert the user and ask whether to put it in a separate branch so they can analyze and merge it later.
   - Do not mix pre-existing uncommitted changes with the current activity unless the user explicitly confirms.
   - Do not overwrite, discard, move, or commit the user's uncommitted code without explicit approval.
   - Before creating commits, pushing the branch, opening/updating PRs, merging/rebasing, or promoting to `main`, run `git fetch` and check whether the current branch is behind `origin/main` or its upstream.
   - If the branch is behind, stop and ask the user what to do: rebase, merge `main`, pull/fast-forward, create a new branch from current `main`, or continue without updating.
   - Do not create commits, push branches, open/update PRs, merge, or advance `main` from a behind/outdated branch without explicit user decision.
   - At completion, always commit and push the working branch to the remote.
   - Push/merge to `main` is allowed only for activities whose Issue is already in `Done`.
   - If the Issue is still in `Review`, ask whether an authorized user reviewed and wants the AI to move it from `Review` to `Done`.
   - Only `christian.novaes` and `marcio.chicarino` may authorize `Review` -> `Done`; normalize the requester/login to lowercase and accept either short user or `@leste.com` UPN.
   - If any other user asks for `Done`, refuse, keep the Issue in `Review`, and record the refusal in the Issue Discussion.
   - After the Issue is confirmed in `Done`, ask/confirm whether to push/merge the changes to `main`.
   - Do not push or merge to `main` while the Issue is not in `Done`.
10. Determine impact: Backend, Frontend, Both, Data/SQL, Governance, Domain calculation, Legal/Compliance.
11. Load only the rules for the selected impact area.
12. Implement, validate, update project knowledge when behavior changes, and update memory.

## Development Rule Routing

| Request | Load |
|---|---|
| Backend API/service | `tech_rules/backend/architecture.md`, then only needed backend rule files |
| Frontend feature | `tech_rules/frontend/architecture.md`, then only needed frontend rule files |
| API creation/update | `workflows/create_api.md`; include parallel AI First endpoints/contracts for requested functionality |
| Frontend creation | `workflows/create_frontend.md` |
| API/frontend auth before Azure deploy | `tech_rules/backend/security_patterns.md`, `tech_rules/frontend/security_patterns.md`, `framework/quality_gates.md` |
| SQL/data model | `data_contracts/` + `validation/forbidden.md` |
| Review/quality | `framework/quality_gates.md` + `validation/review_checklist.md` |
| Governance/security | `governance/` + `tech_rules/*/security_patterns.md` |
| Existing implementation/reuse check | `project_knowledge/leste_projects_index.md` + matched repo `project_knowledge/` |
| Frontend API call/endpoints | matched repo `project_knowledge/frontend-endpoints.md` |
| Azure DevOps setup/auth | `config/azure_devops.md` + `system_knowledge/microsoft-learn-skill/SKILL.md` |
| Issue creation, assignment, tags, estimate/time, Discussion log | `config/azure_devops.md` + `framework/task_lifecycle.md` |
| Local uncommitted changes before branch/merge/push | `framework/task_lifecycle.md`; alert user and ask about separate branch for later merge |
| Branch behind main/upstream before commit/push/merge | `framework/task_lifecycle.md`; fetch, detect behind status, and ask user how to proceed |
| Branch commit/push and main promotion | `framework/task_lifecycle.md`; push working branch, require Issue `Done` before `main` |
| Missing Board stages for Issue workflow | `config/azure_devops.md`; organize Board columns/states to represent planned Issue stages |
| Missing repository pipeline or Azure deployment environment | Ask user whether to create one pipeline/environment before adding CI/CD or provisioning Azure resources |
| Microsoft/Azure/Graph question | `system_knowledge/microsoft-learn-skill/SKILL.md` |

## Domain Skill Routing

| Domain | Use |
|---|---|
| Pricing, valuation, NAV, MTM, PU, IRR, duration, fixed income, derivatives | `system_knowledge/leste-pricing-skill/SKILL.md` |
| Risk, VaR, stress, performance metrics, attribution, FX exposure | `system_knowledge/leste-risco-performance-skill/SKILL.md` |
| Legal, compliance, contract review, regulatory, vendor SaaS, employment, fund docs | `system_knowledge/leste-legal-compliance-skill/SKILL.md` |
| Azure, Microsoft Graph, Entra, Functions, Service Bus, Key Vault, App Insights | `system_knowledge/microsoft-learn-skill/SKILL.md` |

## Backend Architecture Default

Backend development follows Generic DDD / Clean Architecture unless a project memory decision overrides it:

- `{ProjectName}.Domain`
- `{ProjectName}.Application`
- `{ProjectName}.Infrastructure`
- `{ProjectName}.API` or host/UI layer

Do not load every project or domain reference unless the selected task requires it.

## API AI First Rule

Whenever creating or updating an API, also create AI First parallel endpoints/contracts for the requested functionality:

1. Reuse the same Application services/use cases; do not duplicate business logic.
2. Expose structured JSON contracts with explicit DTOs, validation errors, status fields, and correlation id.
3. Provide filters/pagination/search metadata where list/query functionality exists.
4. Document AI First endpoints in Scalar/OpenAPI and `project_knowledge/backend-endpoints.md`.
5. Apply the same production security gate as regular APIs.

## Reuse-First Rule

Before creating new functionality, the main skill must check whether existing code already satisfies the demand, but the cross-project knowledge search is user-controlled:

1. Before executing, ask whether the user wants to search knowledge from other projects first or execute directly.
2. If the user chooses knowledge search, search `ai_skills/project_knowledge/leste_projects_index.md`.
3. Open only relevant `.md` project knowledge files from projects that match the request.
4. Do not read every project knowledge file; skip projects and `.md` files that do not fit the request.
5. Analyze the `.md` knowledge base of other Leste projects, where it exists, before assuming something does not exist.
6. Inspect source code only for likely matches identified by relevant `.md` project knowledge or the central index.
7. If project knowledge is incomplete and broader local search is needed, ask the user before searching outside the current repository.
8. When the user authorizes broader local search, search from the first useful local parent/root level that contains sibling projects, not from the entire disk.
   - Example pattern: if the current repository is under `C:\devops\Source\Projeto`, ask before searching from the local projects root such as `C:\devops\Source` or another first-level workspace root used by that machine.
   - Keep it token-efficient: start with top-level directories, repository names, relevant `project_knowledge/*.md`, solution/package files, endpoint/config files, and targeted content searches.
   - Do not scan personal folders, system folders, or unrelated drives unless the user explicitly authorizes that scope.
9. Prefer reuse of existing APIs, services, frontend clients, components, jobs, and shared packages.
10. If no reusable implementation exists, document the gap in the affected project's `project_knowledge/reuse-inventory.md`.
