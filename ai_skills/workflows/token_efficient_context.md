# Token-Efficient Context Workflow

Use this workflow before reading large rule sets, domain references, prompts, or memory files.

## Goal
Spend tokens only on the minimum context needed for the current request, while preserving correctness and auditability.

## Workflow

1. **Refresh DEV_SKILLS at session start**
   - At least once per calendar day, check whether the local `DEV_SKILLS` repository has remote updates from `origin/main`.
   - Store the last successful check date in a local, non-committed marker such as `.leste-ai/devskills-last-check.txt` or `memory/devskills-last-check.txt`.
   - If updates exist, inform the user, force a safe refresh/pull with `git pull --ff-only`, then reread the relevant updated `SKILL.md` and workflow files before continuing.
   - Projects configured with `setup-leste-ai.ps1` usually use a junction at `<project>/ai_skills`; refreshing `DEV_SKILLS` updates those project skills automatically.
   - If a project uses a copied `ai_skills` folder instead of a junction/link, rerun `setup-leste-ai.ps1` or recreate the link before continuing.
   - If local uncommitted changes, conflicts, or non-fast-forward updates block the update, report the issue and do not overwrite local work.

2. **Classify the request first**
   - Identify domain: development, backend, frontend, pricing, risk/performance, legal/compliance, business rules, data contracts, governance.
   - If the request touches Azure, Microsoft Graph, Microsoft Entra, Microsoft 365, Azure CLI, Azure DevOps CLI, Functions, Service Bus, Key Vault, App Insights, or SDK behavior, route to `system_knowledge/microsoft-learn-skill/SKILL.md`.
   - Identify task type: question, implementation, review, calculation, analysis, report, setup.

3. **Read the smallest router**
   - Prefer `SKILL.md`, README, routing table, or index file before opening detailed references.
   - Do not load every file in a domain folder up front.

4. **Load details on demand**
   - Open only the referenced file that matches the user request.
   - For calculations, load the product/method reference and the standard output/workthrough reference only when needed.
   - For legal/compliance, load jurisdiction + contract type references only after classifying the request.

5. **Ask before expanding context**
    - If the route is ambiguous, ask one clarifying question instead of reading multiple large references.
    - If required inputs are missing, ask for the missing inputs before calculating or drafting.
   - Before searching knowledge from other projects, ask whether the user wants that knowledge search first or wants direct execution.
   - If the user chooses knowledge search, read only relevant `.md` knowledge files where they exist and only for projects that fit the request.
   - If useful context may exist in sibling local repositories, ask before searching outside the current repository.
   - When authorized, search from the first useful local workspace/root level that contains related projects, not from the entire disk.

6. **Summarize, do not paste**
   - Keep intermediate notes concise.
   - Cite the rule/source path when useful, but do not reproduce long reference text unless the user asks.

7. **Persist only durable knowledge**
   - Record decisions, issue IDs, and durable project preferences in memory.
   - Do not copy transient calculations, full contracts, or large datasets into memory.

## Default Context Budget

| Phase | Read |
|---|---|
| Session start | At least daily, check remote updates for `DEV_SKILLS`; if updated, pull safely, refresh linked project skills, and reread relevant `SKILL.md`/workflow files |
| Triage | This workflow + `framework/decision_flow.md` + relevant `SKILL.md` |
| Reuse check | `project_knowledge/leste_projects_index.md` + matched repo `project_knowledge/README.md` |
| Cross-project knowledge search | Ask first, then read only relevant `.md` project knowledge where it exists |
| Cross-project local search | Ask first, then search the first useful local workspace/root level using targeted patterns |
| Development | Relevant `tech_rules/` file + project code touched by the task |
| Pricing | `leste-pricing-skill/SKILL.md` + one matched pricing reference |
| Risk/Performance | `leste-risco-performance-skill/SKILL.md` + one matched risk reference |
| Legal/Compliance | `leste-legal-compliance-skill/SKILL.md` + jurisdiction/contract reference |
| Microsoft/Azure/Graph | `microsoft-learn-skill/SKILL.md` + one matched Microsoft Learn page |

## Stop Conditions

- Stop reading and act once the selected route, required rules, and affected files are known.
- Stop and ask if multiple routes are equally likely or the missing input changes the output materially.
