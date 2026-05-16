---
name: leste-risco-performance-skill
description: >
  Lightweight router for Leste risk and performance calculations: VaR, stress tests,
  Sharpe, Sortino, Omega, drawdown, volatility, FX exposure, envelope, achievement,
  and attribution. Use the external full skill at
  D:\Source\Devops\IAProjects\CLAUDE_SKILLS\leste-risco-performance-skill when
  detailed formulas are required.
---
# Leste Risco Performance Skill

Use for quantitative risk and performance calculations for multi-strategy portfolios.

## Token-Efficient Workflow

1. Identify the metric/module requested.
2. Collect required inputs before calculating.
3. Validate parameters such as lambda, confidence, window, NAV, and date range.
4. Load only the matching external reference from `D:\Source\Devops\IAProjects\CLAUDE_SKILLS\leste-risco-performance-skill`.
5. Produce Calculation Workthrough with formulas, inputs, intermediate values, result, and interpretation.

## Routing

| User asks for | Load from external skill |
|---|---|
| EWMA VaR, parametric VaR, vertex VaR, semi-covariance, liquidity-adjusted VaR, component VaR | `references\var-methods.md` |
| Parametric, historical, event-driven, EVT/Hill stress | `references\stress-tests.md` |
| Sharpe, Sortino, Omega, Alpha, Beta, drawdown, volatility, rolling metrics | `references\performance-metrics.md` |
| FX exposure, treasury NAV hedge, envelope, achievement, attribution | `references\exposure-analysis.md` |
| Workthrough format | `references\calculation-workthrough.md` |

## Defaults

- EWMA lambda default: `0.94`.
- VaR confidence defaults: `95%` unless specified.
- Annualization: `sqrt(252)` business days.
- Always state NAV/PLA basis and whether inputs are returns, prices, positions, or DeltaBRL.
