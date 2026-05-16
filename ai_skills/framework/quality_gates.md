# ✅ Quality Gates (Portões de Qualidade)

Barreiras obrigatórias que a IA deve transpor para validar seu trabalho.

## 1. Portão Técnico
- **Generic DDD**: Camadas desacopladas (Domain, Application, Infrastructure, API).
- **SQL First**: Scripts numerados e datados em `scripts/sql/` (Proibição Total de Migrations).
- **Coding Standards**: Uso de pacotes homologados em `tech_rules/coding_standards.md`.

## 2. Portão de Testes
- **xUnit**: Testes unitários mandatários cobrindo fluxos principais e de erro.
- **FluentAssertions**: Verificações legíveis e expressivas.
- **FluentValidation**: Regras de entrada validadas na camada de aplicação.

## 3. Portão de Segurança
- **Azure Key Vault**: Nenhum segredo em código ou arquivos de configuração.
- **Managed Identity**: Autenticação automatizada entre recursos Azure.
- **OWASP Top 10**: Sanitização de input e prevenção de SQL Injection.
