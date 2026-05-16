# 🔐 Security Patterns (Segurança Técnica)

Define as diretrizes de proteção e autenticação técnica.

## 1. Secrets Management
- **Azure Key Vault**: Principal provedor de segredos. **NUNCA** armazene segredos em código ou arquivos locais.
- **Managed Identity**: Utilize identidades gerenciadas para autenticação automática entre recursos Azure.
- **Arquivos Locais no `.gitignore`**: Arquivos locais de desenvolvimento com usuários, senhas, connection strings, tokens, chaves de API, chaves de cloud ou qualquer credencial devem ser incluídos no `.gitignore` antes de serem criados ou alterados.
- **Padrões Locais Obrigatórios no `.gitignore`**: `.env`, `.env.*`, `*.local`, `*.local.*`, `appsettings.Development.json`, `appsettings.Local.json`, `appsettings.*.Local.json`, `appsettings.Development.local.json`, `local.settings.json`, `secrets.json` e `*.secrets.json` devem ser ignorados. Templates sem segredo, como `.env.example`, podem ser versionados.
- **Configuração Não Local com Segredo**: Se um arquivo versionável ou compartilhado contiver senha, token, chave, connection string ou credencial real, a IA deve bloquear o commit e substituir por referência a Azure Key Vault, variável de ambiente, variável secreta de pipeline ou mecanismo equivalente. Se houver mais de uma alternativa válida, questionar o usuário antes de prosseguir.
- **Sem Segredo em Histórico Novo**: É proibido criar commit com credenciais reais, mesmo em arquivos de desenvolvimento. Valores de exemplo devem ser placeholders inequívocos, como `<CONFIGURE_VIA_KEY_VAULT>` ou `<SET_VIA_ENVIRONMENT>`.

## 2. Autenticação e Autorização (Auth)
- **Azure Entra ID (AD)**: Padronizar autenticação via Microsoft.
- **Claims-Based**: Utilize autorização baseada em claims para controle granular de acessos.
- **Endpoint Seguro por Padrão**: Toda API deve exigir autenticação/autorização em todos os endpoints antes de ser entregue, publicada ou movida para `Review`.
- **Proibição de Endpoint Público Acidental**: É proibido criar endpoint acessível sem segurança. Todo endpoint deve exigir pelo menos um mecanismo de acesso, como JWT/OAuth2/OIDC, Azure Entra ID, API Key, subscription key, mTLS ou outro controle aprovado pelo projeto.
- **Exceções Explícitas**: Endpoints realmente públicos, como `health`, `ready`, `metrics` sanitizado, callback público ou documentação, só podem existir se forem explicitamente justificados na Issue, documentados no Scalar e limitados ao mínimo necessário. A exposição pública nunca pode ser presumida.
- **Fail Closed**: Na ausência de definição clara de autenticação, bloquear a implementação/publicação e solicitar definição do mecanismo de acesso. Nunca assumir acesso anônimo como padrão.
- **Testes Obrigatórios de Segurança**: Para qualquer endpoint novo ou alterado, incluir teste/cenário validando que requisição sem credencial válida retorna `401 Unauthorized` ou `403 Forbidden`, conforme o caso.

## 3. OWASP Top 10
- **Sanitização**: Valide e limpe toda entrada de dados (`FluentValidation`).
- **Prevenção de SQL Injection**: NUNCA concatene strings para SQL. Utilize parâmetros via **Dapper/EF Core**.
