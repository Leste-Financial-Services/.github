---
name: leste-pricing-skill
description: >
  Lightweight router for Leste pricing, valuation, fixed income, structured assets,
  derivatives, NAV, MTM, duration, IRR/XIRR, and cash-flow projection. Use the
  external full skill at D:\Source\Devops\IAProjects\CLAUDE_SKILLS\leste-pricing-skill
  when detailed formulas are required.
---
# Leste Pricing Skill

Use for pricing, valuation, yield analysis, cash-flow projection, and calculation memory for Leste credit and fund assets.

## Token-Efficient Workflow

1. Identify product or calculation type.
2. Ask for missing required inputs before calculating.
3. Load only the matching external reference from `D:\Source\Devops\IAProjects\CLAUDE_SKILLS\leste-pricing-skill`.
4. If metrics are requested with a product, also load `references\pricing-metrics.md`.
5. Produce the result and an audit trail; for Excel, include a calculation memory tab.

## Routing

| User mentions | Load from external skill |
|---|---|
| CCI, CCB, CRI, CRA, CPR, CDCA, Debenture, Loan, CDB, NPL, Preferred Equity, Structured Equity | `references\pricing-fixed-income.md` |
| Receivable, Direitos Creditorios, FIDC, Litigation, Precatorio | `references\pricing-structured.md` |
| Swap, Swap Cambial, DDI, TRS, Swap Equity, Forward, NDF | `references\pricing-derivatives.md` |
| Internal Fund, Fund, NAV, quota, cota | `references\pricing-funds.md` |
| Present Value, MTM, Average Term, Duration, IRR, XIRR, TIR | `references\pricing-metrics.md` |

## Output Rules

- Respect Brazilian market precision rules from the external skill.
- In chat, ask whether the user wants detailed Calculation Memory after each calculation.
- In Excel, Calculation Memory is mandatory.
- Do not calculate with missing material inputs; ask specifically for what is missing.
