# 🔐 Security Patterns (Segurança Técnica)

Define as diretrizes de proteção e autenticação técnica.

## 1. Secrets Management
- **Azure Key Vault**: Principal provedor de segredos. **NUNCA** armazene segredos em código ou arquivos locais.
- **Managed Identity**: Utilize identidades gerenciadas para autenticação automática entre recursos Azure.

## 2. Autenticação e Autorização (Auth)
- **Azure Entra ID (AD)**: Padronizar autenticação via Microsoft.
- **Claims-Based**: Utilize autorização baseada em claims para controle granular de acessos.

## 3. OWASP Top 10
- **Sanitização**: Valide e limpe toda entrada de dados (`FluentValidation`).
- **Prevenção de SQL Injection**: NUNCA concatene strings para SQL. Utilize parâmetros via **Dapper/EF Core**.
