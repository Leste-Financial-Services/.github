# 🔐 Security Policy (Políticas de Segurança)

Garante a proteção de recursos e dados em todo o ecossistema.

## 1. Secrets Management
- **Azure Key Vault**: Principal provedor de segredos. **NUNCA** armazene segredos em código ou arquivos locais.
- **Managed Identity**: Utilize identidades gerenciadas para autenticação automática entre recursos Azure.
- **Git Ignore Mandatório**: Arquivos locais de desenvolvimento que possam conter usuário, senha, connection string, token, API key, chave de cloud ou credencial devem estar no `.gitignore` antes de qualquer alteração ou commit.
- **Segredos Fora do Repositório**: Configurações versionáveis não podem conter credenciais reais. Quando uma credencial aparecer em arquivo não local, a IA deve interromper e substituir por Azure Key Vault, variável de ambiente, variável secreta de pipeline ou solução equivalente, questionando o usuário se houver decisão de arquitetura/infraestrutura.

## 2. Autenticação e Autorização (Auth)
- **Azure Entra ID (AD)**: Padronizar autenticação via Microsoft.
- **Claims-Based**: Utilize autorização baseada em claims para controle granular de acessos.

## 3. OWASP Top 10
- **Sanitização**: Valide e limpe toda entrada de dados (`FluentValidation`).
- **Segurança de APIs**: Proteja endpoints críticos com `[Authorize]` e valide permissões.
- **SQL Injection**: NUNCA concatene strings para SQL. Utilize parâmetros via **Dapper/EF Core**.
