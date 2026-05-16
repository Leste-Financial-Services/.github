# 🚀 Create API Workflow (Criação de APIs .NET 8)

Define o passo a passo para implementar uma nova API seguindo o padrão da Leste.

## 1. Planejamento (SQL First)
- **Ação**: Criar o script SQL em `scripts/sql/YYYY-MM-DD_demand/`.
- **Regra**: Seguir a nomenclatura `snake_case` singular e incluir campos de auditoria.

## 2. Camada Domain
- **Ação**: Criar a Entidade de Domínio e a interface do Repositório (`I[Entity]Repository`).
- **Regra**: Nenhuma dependência externa.

## 3. Camada Infrastructure
- **Ação**: Implementar o mapeamento Fluent API e o Repositório (Dapper ou EF Core).
- **Regra**: Sem migrations automáticas.

## 4. Camada Application
- **Ação**: Criar DTOs (`record`), Service (Use Case) e Mapeamentos (AutoMapper).
- **Regra**: Validação via `FluentValidation`.

## 5. Camada API
- **Ação**: Criar o Controller e registrar as dependências (Injeção de Dependência).
- **Regra**: Documentação via **Scalar** e Logging estruturado via **Serilog**.
