# 🔄 Approvals (Fluxos de Aprovação)

Garante que toda mudança crítica seja validada antes da execução.

## 1. Issue-Driven (Azure DevOps)

**Obrigatório**: Nenhuma alteração de código pode ser iniciada sem uma **Issue** ativa no projeto no Azure DevOps.

> ⚡ **A criação de Issues e branches é automática e não requer aprovação humana prévia.**
> A IA deve criar a Issue e a branch imediatamente ao receber a solicitação e iniciar a implementação.

### 📋 Planejamento Mandatório:
Antes da implementação, a IA deve estruturar as Issues com:
- Escopo e objetivo claros.
- Critérios de aceite (Acceptance Criteria).
- Dependências e riscos.

### 🚀 Execução Controlada:
- **Início**: Mover para **Doing** — sem aprovação necessária.
- **Branch**: Criar a branch automaticamente — sem aprovação necessária.
- **Registro**: Documentar decisões, comandos e testes nos **comentários da Issue** (Discussion incremental).
- **Conclusão**: Mover para **Review**.
- **Done**: Somente após aprovação humana documentada.

## 2. Mudanças em Banco de Dados (Produção)
- **Aprovação**: Scripts de alteração de banco de dados (`scripts/sql/`) exigem aprovação manual de um DBA ou Engenheiro Sênior.
- **Rollback**: Todo script SQL deve possuir uma estratégia de rollback validada.
