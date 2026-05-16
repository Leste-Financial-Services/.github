---
name: leste-illiquids-skill
description: Domain knowledge and workflows for Leste Illiquids projects. Use when working with Pricing, Enquadramento, fund calculators, structured assets, fixed income valuation, derivatives, NAV, MTM, IRR, or duration.
---
# Leste Illiquids Skill
Complex financial calculations for illiquid assets.
- **Projects**: `WebJob.Illiquids.Pricing`, `FundTaxesCalculator`, `Consignado.ExtracaoDados`.
- **Tech**: .NET 6, Service Bus, EF Core.

## Pricing Skill Routing

For detailed pricing formulas and precision rules, use `system_knowledge/leste-pricing-skill/SKILL.md`.

Token-efficient default:
1. Identify product/calculation type.
2. Load only the matched external pricing reference.
3. Ask for missing required inputs before calculating.
4. Keep calculation memory available without pasting every step unless requested or required by the output artifact.
