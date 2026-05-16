# 🔐 Security Patterns (Segurança Técnica)

Define as diretrizes de proteção e autenticação técnica.

## 1. Secrets Management
- **Azure Key Vault**: Principal provedor de segredos. **NUNCA** armazene segredos em código ou arquivos locais.
- **Managed Identity**: Utilize identidades gerenciadas para autenticação automática entre recursos Azure.

## 2. Autenticação e Autorização (Auth)
- **Azure Entra ID (AD)**: Padronizar autenticação via Microsoft.
- **Claims-Based**: Utilize autorização baseada em claims para controle granular de acessos.
- **Frontend deve ter decisão explícita de autenticação**: toda tela/frontend criado ou atualizado deve verificar se há camada de autenticação antes de deploy.
  - Pode ser um site aberto, mas isso deve ser confirmado com o usuário antes de seguir sem autenticação.
  - Se o usuário quiser autenticação, perguntar se deseja usar algum frontend existente como espelho.
  - Antes de implementar autenticação, consultar `project_knowledge/frontend-endpoints.md` e knowledge `.md` relevante de outros frontends autorizados pelo usuário para reutilizar o padrão existente.
  - Documentar no `project_knowledge/frontend-endpoints.md` o modelo de autenticação, base URLs, headers e requisitos para chamadas de API.

## 3. OWASP Top 10
- **Sanitização**: Valide e limpe toda entrada de dados (`FluentValidation`).
- **Prevenção de SQL Injection**: NUNCA concatene strings para SQL. Utilize parâmetros via **Dapper/EF Core**.
