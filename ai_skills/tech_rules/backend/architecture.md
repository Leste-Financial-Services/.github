# 🏗️ Architecture (Arquitetura & Padrões)

Define como o código C# deve ser estruturado conforme o padrão **Generic DDD**.

## 1. Generic DDD (.NET 8.0+)
As aplicações devem ser organizadas em 4 camadas desacopladas:
- **{ProjectName}.Domain**: Entities, Value Objects, Repository Interfaces. **Nenhuma dependência externa.**
- **{ProjectName}.Application**: DTOs (Records), Services (Use Cases), AutoMapper 13.0+.
- **{ProjectName}.Infrastructure**: Implementação de dados (Dapper/EF Core 8), Refit 7.0+, Fluent API.
- **{ProjectName}.API / UI**: Controllers, Dependency Injection, Middleware, Scalar Setup.

## 2. Nomenclatura (Naming)
- **C#**: PascalCase (Classes/Métodos/Propriedades), camelCase (Variáveis/Parâmetros), _camelCase (Privados).
- **Interfaces**: Prefixo `I` (ex: `IUserRepository`).
- **DTOs**: Utilizar exclusivamente `record` (Imutabilidade).

## 3. Identação e Estilo
- **4 espaços** para identação.
- **Estilo Allman** (chaves na nova linha).
