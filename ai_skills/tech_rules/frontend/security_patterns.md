# 🔐 Security Patterns (Segurança Técnica)

Define as diretrizes de proteção e autenticação técnica.

## 1. Secrets Management
- **Azure Key Vault**: Principal provedor de segredos. **NUNCA** armazene segredos em código ou arquivos locais.
- **Managed Identity**: Utilize identidades gerenciadas para autenticação automática entre recursos Azure.
- **Arquivos Locais no `.gitignore`**: Arquivos locais de desenvolvimento com usuários, senhas, tokens, API keys, chaves de cloud ou credenciais devem ser incluídos no `.gitignore` antes de serem criados ou alterados.
- **Padrões Locais Obrigatórios no `.gitignore`**: `.env`, `.env.*`, `*.local`, `*.local.*`, `local.settings.json`, `secrets.json` e `*.secrets.json` devem ser ignorados. Templates sem segredo, como `.env.example`, podem ser versionados.
- **Configuração Não Local com Segredo**: Se arquivo versionável contiver senha, token, chave ou credencial real, bloquear o commit e substituir por variável de ambiente, Azure Key Vault, variável secreta de pipeline ou mecanismo equivalente. Se houver mais de uma alternativa válida, questionar o usuário antes de prosseguir.

## 2. Autenticação e Autorização (Auth)
- **Azure Entra ID (AD)**: Padronizar autenticação via Microsoft.
- **Claims-Based**: Utilize autorização baseada em claims para controle granular de acessos.

## 3. OWASP Top 10
- **Sanitização**: Valide e limpe toda entrada de dados (`FluentValidation`).
- **Prevenção de SQL Injection**: NUNCA concatene strings para SQL. Utilize parâmetros via **Dapper/EF Core**.
