# Project Knowledge Workflow

Use this workflow before implementing any new feature, endpoint, screen, job, integration, or business rule.

## Purpose

Maintain project-level Markdown knowledge so agents can discover existing implementations, avoid duplicate functionality, and reuse services, APIs, frontend endpoints, components, and domain rules already implemented at Leste.

## Required Files

Each repository should contain:

```text
project_knowledge/
├── README.md
├── overview.md
├── frontend-endpoints.md
├── backend-endpoints.md
└── reuse-inventory.md
```

The DEV_SKILLS hub should maintain the cross-project index:

```text
ai_skills/project_knowledge/leste_projects_index.md
```

## Workflow Before Any Implementation

1. **Ask before cross-project knowledge search**
   - Before executing, ask whether the user wants to search knowledge from other projects first or execute directly.
   - If the user chooses direct execution, continue with the requested work and do not spend tokens on cross-project knowledge unless a blocker appears.
   - If the user chooses knowledge search, continue with the steps below.

2. **Read the central index first**
   - Open `ai_skills/project_knowledge/leste_projects_index.md`.
   - Search by business term, endpoint, entity, product, screen name, job name, and repository name.

3. **Check for existing implementation**
    - If the central index indicates a likely match, open that repository's `project_knowledge/README.md`.
    - Then read only the relevant file: `frontend-endpoints.md`, `backend-endpoints.md`, or `reuse-inventory.md`.
    - Read only `.md` knowledge files, only where they exist, and only for projects that fit the request.
    - Do not open every repository's knowledge files.
     - Search the actual code only after the knowledge files identify likely locations.
   - When authorized by the user, consider the knowledge base of other Leste projects before creating a new implementation.

4. **Analyze frontend production endpoints**
   - For frontend repositories, inspect `frontend-endpoints.md` before adding API calls.
   - Confirm production base URLs, environment variables, auth model, headers, and client/service files.
   - Reuse existing API clients and auth handling instead of creating parallel implementations.

5. **Reuse before creating**
    - Prefer existing APIs/services/components/jobs when they satisfy the demand.
    - If reusing is not possible, explain the gap and update `reuse-inventory.md` after implementation.

6. **Broader local search when knowledge is incomplete**
   - If the central index and likely `project_knowledge/` files are insufficient, ask the user before searching outside the current repository.
   - When authorized, search from the first useful local workspace/root level that contains sibling projects.
   - Example pattern: if a repo is under `C:\devops\Source\Projeto`, search from a local projects root such as `C:\devops\Source` (or the equivalent first-level workspace root on that machine), not from the entire disk.
   - Keep the search narrow: start with top-level repository names, relevant `project_knowledge/*.md`, solution/package manifests, endpoint/config files, and targeted keywords.
   - Do not search personal folders, OS folders, unrelated drives, secrets, or broad disk roots unless the user explicitly authorizes that scope.

7. **Update knowledge after changes**
    - When changing endpoints, services, jobs, integrations, screens, or major business behavior, update the local `project_knowledge/` files.
    - Regenerate the central index with `Update-LesteProjectKnowledge.ps1`.

## Duplication Check

Before building something new, answer:

| Question | Required action |
|---|---|
| Does another repository expose a similar endpoint? | Check central index + backend endpoint docs |
| Does a frontend already call the same production API? | Check frontend endpoint docs |
| Is there an existing service/client/component/job? | Check reuse inventory |
| Is the same entity/domain already modeled? | Search central index and relevant project knowledge |
| Is a shared package available? | Check packages/framework repos before copying code |

## Token-Efficient Use

- Start with the central index and local `README.md`.
- Do not read every project file.
- Load `.md` knowledge docs only for likely matches and only when they exist.
- Ask the user when multiple existing implementations could satisfy the same demand.
- Ask the user before expanding from the current repository to a parent/local workspace search.
