# 🔐 Security Patterns (Segurança Técnica)

Define as diretrizes de proteção e autenticação técnica.

## 1. Secrets Management
- **Azure Key Vault**: Principal provedor de segredos. **NUNCA** armazene segredos em código ou arquivos locais.
- **Managed Identity**: Utilize identidades gerenciadas para autenticação automática entre recursos Azure.

## 2. Autenticação e Autorização (Auth)
- **Azure Entra ID (AD)**: Padronizar autenticação via Microsoft.
- **Claims-Based**: Utilize autorização baseada em claims para controle granular de acessos.
- **API não pode ir aberta para produção**: qualquer API criada ou atualizada deve ter uma camada de segurança validada antes de deploy na Azure.
  - Opções aceitas: Microsoft Entra ID/JWT, API key, subscription key/API Management, Managed Identity entre serviços, ou outro mecanismo explicitamente aprovado.
  - Se a API ainda não possui segurança, interromper o fluxo de deploy e perguntar qual mecanismo deve ser implementado.
  - Não publicar endpoint em produção sem autenticação/autorização ou chave validada.
  - Documentar no `project_knowledge/backend-endpoints.md` o mecanismo de segurança esperado para consumo.

## 3. OWASP Top 10
- **Sanitização**: Valide e limpe toda entrada de dados (`FluentValidation`).
- **Prevenção de SQL Injection**: NUNCA concatene strings para SQL. Utilize parâmetros via **Dapper/EF Core**.
