# ✅ Quality Gates (Portões de Qualidade)

Barreiras obrigatórias que a IA deve transpor para validar seu trabalho.

## 1. Portão Técnico
- **Generic DDD**: Camadas desacopladas (Domain, Application, Infrastructure, API).
- **SQL First**: Scripts numerados e datados em `scripts/sql/` (Proibição Total de Migrations).
- **Coding Standards**: Uso de pacotes homologados em `tech_rules/coding_standards.md`.
- **AI First para APIs**: toda API criada/alterada deve incluir endpoints/contratos paralelos AI First para a funcionalidade solicitada, reutilizando os mesmos use cases.

## 2. Portão de Testes
- **xUnit**: Testes unitários mandatários cobrindo fluxos principais e de erro.
- **FluentAssertions**: Verificações legíveis e expressivas.
- **FluentValidation**: Regras de entrada validadas na camada de aplicação.

## 3. Portão de Segurança
- **Azure Key Vault**: Nenhum segredo em código ou arquivos de configuração.
- **Managed Identity**: Autenticação automatizada entre recursos Azure.
- **OWASP Top 10**: Sanitização de input e prevenção de SQL Injection.
- **API protegida para produção**: API criada/alterada não pode seguir para deploy Azure aberta; deve ter Entra ID, API key/subscription key, Managed Identity/API Management, ou mecanismo aprovado.
- **AI First protegido**: endpoints AI First seguem a mesma autenticação/autorização e não podem ficar abertos em produção.
- **Frontend com decisão de auth**: frontend/tela criada/alterada deve ter autenticação definida ou confirmação explícita do usuário de que é site aberto.
