---
name: leste-risk-skill
description: Domain knowledge and workflows for Leste Risk domain. Use when working with Risk.Performance, risk robots, VaR, stress tests, performance metrics, FX exposure, or attribution.
---
# Leste Risk Skill
Risk monitoring and performance metrics.
- **Projects**: `Risk.Performance`, `Webjob.Risk.PriceImporter`.
- **Tech**: .NET 6/8, Bloomberg, Monte Carlo algorithms.

## Quantitative Skill Routing

For detailed calculation rules, use `system_knowledge/leste-risco-performance-skill/SKILL.md`.

Token-efficient default:
1. Identify the risk/performance module.
2. Load only the matching reference from `D:\Source\Devops\IAProjects\CLAUDE_SKILLS\leste-risco-performance-skill`.
3. Produce a Calculation Workthrough only at the detail level required by the user/output.
