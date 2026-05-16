# 🚫 Forbidden (O que é proibido)

O firewall lógico da IA: o que a IA **NUNCA** deve fazer.

## 1. SELECT *
- **NUNCA** utilize `SELECT *` em SQL. Liste as colunas explicitamente.

## 2. EF Core Migrations
- **PROIBIDO**: `Add-Migration`, `Update-Database`, etc.
- Toda mudança estrutural deve ser via script SQL manual em `scripts/sql/`.

## 3. Swagger/Swashbuckle
- **PROIBIDO**: Use exclusivamente o **Scalar** (`/scalar/v1`).

## 4. Exposição de Domínio
- **NUNCA** exponha Entidades de Domínio diretamente na camada de API. Sempre utilize **DTOs (records)**.

## 5. Bypassing Azure Key Vault
- **NUNCA** armazene segredos em arquivos de configuração ou código.
