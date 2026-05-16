---
name: leste-legal-compliance-skill
description: >
  Lightweight router for Leste legal and compliance analysis: contract review,
  regulatory, fund structuring, private equity, structured credit, real estate,
  vendor/SaaS, employment, sensitive data, cross-border, and direct legal Q&A.
  Use the external full skill at
  D:\Source\Devops\IAProjects\CLAUDE_SKILLS\leste-legal-compliance when detailed
  legal references are required.
---
# Leste Legal Compliance Skill

Use for legal/compliance questions, contract review, regulatory analysis, vendor/SaaS review, employment, fund documents, and high-risk clause scans.

## Token-Efficient Workflow

1. Determine mode:
   - Direct legal Q&A: answer directly after identifying jurisdiction and relevant reference.
   - Contract/document review: follow the full analysis workflow.
2. Jurisdiction first: Brazil, US state, Cayman, Delaware, BVI, or other.
3. Identify contract type/business area.
4. Load only the matching external reference from `D:\Source\Devops\IAProjects\CLAUDE_SKILLS\leste-legal-compliance`.
5. For contract reviews, also load `references\leste-internal-policies.md` and `references\output-format.md`.
6. Flag critical risks and recommend specialist review where appropriate.

## Routing

| User mentions | Load from external skill |
|---|---|
| General contract review, unknown type | `references\core-analysis.md` |
| Fund regulation, PPM, subscription, side letter, IMA, LP Agreement | `references\fund-structuring.md` |
| SPA, SHA, PE deal, tag/drag, earn-out, reps and warranties | `references\private-equity.md` |
| Loan, debenture, CCB, credit assignment, collateral, covenants, waterfall | `references\credit-structuring.md` |
| Real estate BR, purchase, lease, SPE, property management | `references\real-estate.md` |
| Net lease, NNN, built-to-suit, sale-leaseback | `references\net-lease.md` |
| US development, EPC, GMP, ground lease, construction | `references\real-estate-us-development.md` |
| CVM, SEC, FINRA, ANBIMA, LGPD, GDPR, AML/KYC | `references\regulatory-compliance.md` |
| MSA, SaaS, vendor, DPA, AI training, data residency | `references\vendor-technology.md` + `references\bsi-saas-diligence.md` |
| Employment BR or US | `references\employment-hr-br.md` or `references\employment-hr-us.md` |
| Sensitive data access, Big4, auditor, consultant | `references\data-access-sensitive.md` |
| Fees, variable fee, financial impact | `references\financial-impact.md` |
| JV, partnership, co-investment, deadlock | `references\jv-partnerships.md` |
| Guarantees, indemnities, MAC, cross-default, penalties | `references\high-risk-clauses.md` |
| Offshore, cross-border, Cayman, Delaware, FATCA, CRS | `references\cross-border.md` |

## Safety Rules

- Never present a final legal opinion; provide risk analysis and escalation guidance.
- Protect Leste's position.
- Contract reviews require internal policy cross-check.
- Direct Q&A should avoid full template overhead.
