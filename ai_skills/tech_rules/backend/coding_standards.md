# 🛠️ Coding Standards (Pacotes & Padrões)

Define a lista oficial de pacotes NuGet (.NET 8.0) e padrões de codificação técnica.

## 1. API / Web Layer
- **Scalar.AspNetCore**: Documentação de API moderna.
- **Serilog.AspNetCore**: Logging estruturado padrão.
- **AutoMapper**: Mapeamento entre Entidades e DTOs.
- **Microsoft.ApplicationInsights.AspNetCore**: Monitoramento Azure.

## 2. Domain Layer
- **FluentValidation**: Validação de regras de negócio.
- **Refit**: Cliente HTTP tipado.
- **CsvHelper / EPPlus**: Manipulação de arquivos.
- **Leste.Framework.Calendar**: Calendários internos.

## 3. Infrastructure Layer
- **Microsoft.EntityFrameworkCore.SqlServer**: EF Core 8 (SEM MIGRATIONS).
- **Dapper**: Micro-ORM para alta performance.
- **Microsoft.Data.SqlClient**: Provedor oficial.

## 4. Logging Mandatário
- Use templates (ex: `Log.Information("User {UserId}...", userId)`) em vez de interpolação simples.
