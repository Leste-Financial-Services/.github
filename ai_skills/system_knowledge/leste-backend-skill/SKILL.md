---
name: leste-backend-skill
description: Domain knowledge and workflows for Leste Backend projects based on .NET 5/6/8. Use when working with any Leste C#/.NET API or service, especially Generic DDD, Clean Architecture, APIs, workers, Azure Functions, and data access.
---
# Leste Backend Skill

This skill provides specialized knowledge for Leste's backend services.

## Token-Efficient Workflow

Follow `ai_skills/workflows/token_efficient_context.md`.

1. Classify the backend task: API, worker, Azure Function, repository/data access, integration, security, observability, or tests.
2. Read only the matching rule file from `ai_skills/tech_rules/backend/`.
3. Read `workflows/create_api.md` when creating or updating an API/use case, including AI First parallel endpoints.
4. Read domain skills only when the backend logic involves that business domain.
5. Avoid loading all business rules up front; route by entity, product, or module first.

## Backend Defaults

- **Tech Stack**: ASP.NET Core / Azure Functions (.NET 5 to 8), EF Core, Dapper, FluentValidation, JWT.
- **Patterns**: Generic DDD / Clean Architecture with API or Host, Domain, Application, Infrastructure.
- **Packages**: `Leste.Logging`, `Leste.Utils`.
- **DI Patterns**: `ConfigureDomain`, `ConfigureInfra`.
- **SQL**: SQL first; EF Core migrations are forbidden unless a project-specific decision explicitly overrides this.
- **AI First APIs**: every created/updated API must include parallel AI First endpoints/contracts for the requested functionality, reusing the same Application use cases and protected by the same security model.

## Architecture

Use the 4-layer model from `tech_rules/backend/architecture.md`:

| Layer | Responsibility |
|---|---|
| Domain | Entities, Value Objects, Repository Interfaces, no external dependencies |
| Application | DTO records, Use Cases/Services, validation, mappings |
| Infrastructure | Dapper/EF Core, Refit, Fluent API, external integrations |
| API/Host | Controllers, Azure Functions, DI, middleware, Scalar/OpenAPI |

## Domain Routing

| Backend logic involves | Use |
|---|---|
| Pricing, valuation, PU, MTM, NAV, IRR, duration | `system_knowledge/leste-pricing-skill/SKILL.md` |
| Risk, VaR, stress, performance, attribution, FX exposure | `system_knowledge/leste-risco-performance-skill/SKILL.md` |
| Legal, compliance, contracts, regulatory, sensitive data, vendor SaaS | `system_knowledge/leste-legal-compliance-skill/SKILL.md` |

Do not copy full formula/legal references into code comments or memory. Keep durable business decisions in memory and implementation details in code/tests.
