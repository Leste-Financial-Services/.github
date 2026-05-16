# Azure DevOps Configuration

## Organization

Use this organization for all Azure DevOps CLI and portal operations:

```text
https://dev.azure.com/LesteDevOps
```

When a project is required and the user did not specify one, infer it from repository context only when safe. Otherwise ask before creating work items.

## Authentication

Use the current user's Microsoft Entra credentials through Azure CLI.

```powershell
az login
az devops configure --defaults organization=https://dev.azure.com/LesteDevOps
```

Rules:

- Do not store, print, request, or hardcode passwords, PATs, refresh tokens, or session tokens.
- Prefer `az login` for interactive user authentication.
- Do not use `az devops login` unless the user explicitly chooses PAT-based authentication or is a guest user scenario that requires it.
- For commands, pass `--organization https://dev.azure.com/LesteDevOps` when defaults are not configured.
- If authentication fails, surface the exact CLI error and ask the user to complete `az login`; do not invent credentials or silently fall back.

## Common Commands

```powershell
az devops configure --defaults organization=https://dev.azure.com/LesteDevOps project=<ProjectName>
az account show --query user.name -o tsv
az boards work-item create --organization https://dev.azure.com/LesteDevOps --project <ProjectName> --type Issue --title "<Title>" --assigned-to "<LoggedUserUPN>"
az boards work-item relation add --organization https://dev.azure.com/LesteDevOps --id <SourceId> --target-id <TargetId> --relation-type Related
az boards work-item update --organization https://dev.azure.com/LesteDevOps --id <IssueId> --fields "System.Tags=<Tag1>; <Tag2>" "System.AssignedTo=<LoggedUserUPN>"
```

## Board and Issue Workflow

When creating or organizing an Issue:

1. Identify the stages planned for the Issue from the requested workflow, acceptance criteria, subtasks, checklist, or linked work items.
2. Check whether the Azure DevOps Board already has columns/states that represent those planned stages.
3. If the Board does not have the required stages, organize it so the planned Issue stages are represented before execution continues.
4. Prefer board columns/team settings when the need is only visual workflow organization; change process states only when the project governance requires true state changes.
5. If the current user lacks permission to update the Board/process, document the missing stages in the Issue discussion and ask a project/process administrator to add them.
6. Do not delete or rename existing Board stages without explicit user approval.
7. Keep the default lifecycle aligned with `Backlog`, `ToDo`, `Doing`, `Review`, `Done`, and add only the extra stages needed by the Issue workflow.

## Done and Main Promotion Gate

1. The AI's normal completion state is `Review`.
2. The AI may move an Issue from `Review` to `Done` only when explicitly instructed by an authorized user.
3. Authorized users for `Review` -> `Done` are:
   - `christian.novaes`
   - `marcio.chicarino`
4. Normalize requester/login to lowercase before checking authorization. Accept both short username and `@leste.com` UPN forms.
5. If any other user requests `Done`, refuse the transition, keep the Issue in `Review`, and document the refusal in the Issue Discussion.
6. Push/merge to `main` is allowed only after the linked Issue/activity is in `Done`.
7. If the Issue is still in `Review`, do not push/merge to `main`; ask for authorized review/Done first.

## Local Uncommitted Code Gate

1. Before starting implementation, switching branches, pulling updates, merging/rebasing, or preparing push, check for local uncommitted changes.
2. If uncommitted code exists, alert the user before proceeding.
3. Ask whether the user wants those local changes moved/preserved in a separate branch so they can review and merge later.
4. Do not mix pre-existing uncommitted changes with the current Issue unless the user explicitly approves.
5. Do not overwrite, discard, move, or commit the user's uncommitted changes without explicit approval.

## Behind Branch Gate

1. Before creating commits, pushing a branch, opening/updating PRs, merging/rebasing, or promoting to `main`, run `git fetch`.
2. Check whether the current branch is behind `origin/main`.
3. If the branch has an upstream, also check whether it is behind that upstream.
4. If the branch is behind, stop and ask the user what to do before continuing.
5. Valid choices include: rebase onto current `main`, merge current `main`, pull/fast-forward from upstream, create a new branch from current `main`, or explicitly continue without updating.
6. Do not commit, push, open/update PR, merge, or advance `main` from a behind/outdated branch without an explicit user decision.

## Issue Ownership, Tags, and Work Accounting

1. Always create or use work item type **Issue**. This is the mandatory work item type for Leste development tracking.
2. Every Issue must be assigned to the currently logged-in user.
   - Resolve the user with `az account show --query user.name -o tsv`.
   - Set `System.AssignedTo` / `--assigned-to` to that logged user.
3. Every Issue must have 1 to 3 category tags that describe the work.
   - Choose from the activity and technology context; examples: `Backend`, `Frontend`, `API`, `MCP`, `IA`, `C#`, `Python`, `VUE.JS`, `REACT`, `Javascript`, `Management`, `PMO`, `Skill`, `C++`, `C`, `Mobile`, `Responsive`, `SQL`, `AzureDevOps`, `Azure`, `Security`, `Documentation`.
   - Use no fewer than 1 and no more than 3 tags unless the user explicitly overrides.
4. At the start, fill the estimate before implementation begins.
   - Prefer the Azure DevOps scheduling field used by the process, usually `Microsoft.VSTS.Scheduling.Effort`.
   - If the field is unavailable, write the estimate in the Issue description or first discussion comment.
5. At completion, fill actual time spent.
   - Prefer `Microsoft.VSTS.Scheduling.CompletedWork` and `Microsoft.VSTS.Scheduling.FinishDate` when available.
   - If a field is unavailable, write the value in the Issue discussion.

## Issue Discussion Log

1. Before implementation begins, create a local, non-committed log file named with the Issue ID, for example:
   - `ISSUE-<id>-discussion.md`
2. Store it outside committed source or in a git-ignored local work area.
3. Append everything relevant that appears during the CLI session, step by step:
   - user responses and decisions;
   - assistant explanations and final summaries;
   - commands run;
   - important command outputs, errors, validations, and fixes;
   - files changed and why.
4. At the end, copy the local log content into the Issue discussion as one or more comments.
5. If the Azure DevOps CLI or permissions prevent adding comments, keep the local file and explicitly tell the user that the Issue discussion still needs synchronization.
