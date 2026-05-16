# 🔄 Approvals (Fluxos de Aprovação)

Garante que toda mudança crítica seja validada antes da execução.

## 1. Issue-Driven Approval (Azure DevOps)

**Obrigatório**: Nenhuma alteração de código pode ser iniciada sem uma Issue ativa no projeto **IAProjects** (https://dev.azure.com/LesteDevOps/IAProjects).

### 📋 Planejamento Mandatório:
Antes da implementação, a IA deve estruturar a Issue com:
- Escopo e objetivo claros.
- Critérios de aceite (Acceptance Criteria).
- Dependências e riscos.
- Responsável (`Assigned To`) igual ao usuário logado.
- De 1 a 3 tags de categoria.
- Estimativa inicial de esforço/tempo.

### 🚀 Execução Controlada:
- **Início**: Mover para **In Progress**.
- **Registro**: Documentar decisões, respostas do usuário, comandos, saídas relevantes, validações e testes nos **comentários da Issue**.
- **Código não commitado**: Antes de iniciar, trocar branch, puxar, mesclar ou preparar push, verificar alterações locais não commitadas; se existirem, alertar o usuário e perguntar se quer preservar em branch separada para análise e merge posterior.
- **Branch defasada**: Antes de commit, push, PR, merge/rebase ou promoção para `main`, executar `git fetch` e verificar se a branch está atrás de `origin/main` ou upstream. Se estiver atrás, parar e perguntar se deve rebasear, fazer merge, pull/fast-forward, criar nova branch, ou continuar sem atualizar.
- **Branch**: Commitar e fazer push sempre na branch de trabalho vinculada à Issue.
- **Conclusão da IA**: Mover para **Ready for Review / Validation**.
- **Review → Done**: A IA só pode mover para `Done` quando o comando vier de `christian.novaes` ou `marcio.chicarino`, comparando o usuário normalizado em lowercase. Aceitar também `christian.novaes@leste.com` e `marcio.chicarino@leste.com`.
- **Main**: Push/merge para `main` somente quando a Issue estiver em `Done`.
- **Bloqueio**: Se a Issue estiver em `Review`, a IA deve recusar push/merge para `main` até a transição autorizada para `Done`.

## 2. Mudanças em Banco de Dados (Produção)
- **Aprovação**: Scripts de alteração de banco de dados (`scripts/sql/`) exigem aprovação manual de um DBA ou Engenheiro Sênior.
- **Rollback**: Todo script SQL deve possuir uma estratégia de rollback validada.
