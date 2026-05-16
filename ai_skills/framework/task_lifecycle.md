# 🔄 Issue Lifecycle (Ciclo de Vida da Issue)

Define os estados e responsabilidades da IA ao longo de uma Issue no Azure DevOps.

## Organização e Autenticação

- **Organização Azure DevOps**: `https://dev.azure.com/LesteDevOps`
- **Autenticação padrão**: usar as credenciais Microsoft Entra do usuário via Azure CLI.
- **Setup recomendado**:
  ```powershell
  az login
  az devops configure --defaults organization=https://dev.azure.com/LesteDevOps
  ```
- **Proibido**: solicitar, salvar, imprimir ou embutir senha, PAT, refresh token ou qualquer segredo.
- **PAT**: usar `az devops login` somente se o usuário pedir explicitamente ou se for cenário de Guest User que exija PAT.
- **Fallback seguro**: se a CLI não estiver autenticada, interromper e pedir para o usuário concluir `az login`.

## 🛑 Regras Mandatórias (Absolute Mandates)

1.  **Criação Antecipada**: **SEMPRE** criar uma **Issue** no Azure DevOps antes de iniciar qualquer implementação ou alteração de código. Issue é o tipo obrigatório de work item.
2.  **Rastreabilidade e Versão**:
    *   **Branches**: Alterações devem ser feitas em uma **Branch Nova** (`feature/ID-nome`) ou na branch de trabalho vinculada à Issue. Não trabalhar diretamente na `main`.
    *   **Código não commitado**: Antes de iniciar, trocar branch, puxar atualizações, fazer merge/rebase, ou preparar push, verificar se existem alterações locais não commitadas.
    *   Se houver código não commitado, alertar o usuário e perguntar se ele quer colocar essas alterações em uma branch separada para analisar depois um merge.
    *   Não misturar alterações não commitadas preexistentes com a atividade atual sem confirmação explícita do usuário.
    *   Não sobrescrever, descartar, mover ou commitar código não commitado do usuário sem aprovação explícita.
    *   **Branch defasada**: Antes de criar commit, fazer push da branch, abrir/atualizar PR, ou levar algo para `main`, executar `git fetch` e verificar se a branch atual está atrás de `origin/main` ou do seu upstream.
    *   Se a branch estiver atrás, a IA deve parar e perguntar ao usuário o que fazer: rebase, merge de `main`, pull/fast-forward, criar branch nova a partir da main, ou continuar sem atualizar.
    *   Não criar commits, pushs, PRs, merges ou avanço de `main` a partir de branch defasada sem decisão explícita do usuário.
    *   **Commits/Push na branch**: Todo commit deve referenciar o ID da Issue (`#ID`). Ao finalizar a atividade, sempre fazer commit e push da branch de trabalho para o remoto.
    *   **Main somente com Issue em Done**: Push/merge para `main` só pode ocorrer quando a atividade/Issue estiver em `Done`. Se a Issue ainda estiver em `Review`, não levar para `main`.
    *   **Main após aprovação autorizada**: Ao terminar, perguntar se um usuário autorizado revisou e quer mover de `Review` para `Done`. Só depois de `Done` perguntar/confirmar push/merge para `main`.
    *   **Repositório**: O nome do repositório deve ser **exatamente o nome da pasta do projeto**.
3.  **Responsável da Issue**:
    *   Toda Issue deve ficar sempre atribuída (`Assigned To`) ao usuário logado.
    *   Identificar o usuário com `az account show --query user.name -o tsv`.
    *   Usar esse usuário em `--assigned-to` ou no campo `System.AssignedTo`.
4.  **Gestão de Estados (Fluxo)**:
     *   Estados permitidos: `Backlog`, `ToDo`, `Doing`, `Review`, `Done`.
     *   O estado final padrão da IA é **Review**.
    *   A IA só pode mover de `Review` para `Done` quando receber comando explícito de um usuário autorizado.
    *   Usuários autorizados para `Review` → `Done`: `christian.novaes` e `marcio.chicarino`.
    *   Validar o usuário em lowercase. Aceitar `christian.novaes`, `christian.novaes@leste.com`, `marcio.chicarino` e `marcio.chicarino@leste.com` quando normalizados para lowercase.
    *   Se outro usuário pedir `Done`, recusar a transição, manter em `Review` e registrar na Discussion da Issue.
    *   Se a Issue tiver etapas planejadas/agendadas que não existem no Board, organizar o Board para representar essas etapas antes de continuar a execução.
    *   Preferir colunas/configuração do Board quando for organização visual; alterar estados do processo somente quando a governança do projeto exigir.
    *   Não apagar ou renomear etapas existentes sem aprovação explícita.
    *   Se faltar permissão para alterar Board/processo, registrar as etapas ausentes na Discussion da Issue e solicitar ação de um administrador do projeto/processo.
5.  **Qualidade da Informação (Discussion - Espelhamento Integral)**:
     *   Os comentários na seção `Discussion` devem ser um **ESPELHAMENTO INTEGRAL, EXATO E COMPLETO** de **TODO** o conteúdo textual gerado pela IA no console.
    *   Isso inclui: Pensamentos (thought), justificativas técnicas, logs de shell, e resumos finais. **Não é permitido resumir ou omitir partes da interação do console.**
    *   Antes da execução, criar arquivo local não commitado com o ID da Issue no nome, por exemplo `ISSUE-<id>-discussion.md`.
    *   Acrescentar nesse arquivo, passo a passo, tudo que aparecer/relevante no CLI: respostas do usuário, decisões, comandos, saídas importantes, erros, validações, arquivos alterados e resumo final.
    *   Ao finalizar, copiar o conteúdo desse arquivo para a Discussion da Issue em um ou mais comentários.
6.  **Análise de Custo (Analysis Field)**:
    *   O campo `Analysis` (ou o início da descrição/comentário se o campo não estiver disponível via CLI) deve conter a **Análise de Custo** detalhada (Tokens, Tempo, Activity).
7.  **Campos Técnicos Obrigatórios**:
    *   **Activity**: `Deployment`, `Design`, `Development`, `Documentation`, `Requirements`, ou `Testing`.
    *   **Effort/Estimate** deve ser preenchido no início, antes de implementar.
    *   **Completed Work** e **Finish Date** devem ser preenchidos ao finalizar com o tempo efetivamente gasto.
    *   Se algum campo técnico não existir no processo do Azure DevOps, registrar o valor na Discussion da Issue.
8.  **Organização por Tags**:
    *   Toda Issue deve ter de **1 a 3 tags** que reflitam a categoria da atividade.
    *   Exemplos: `Backend`, `Frontend`, `API`, `MCP`, `IA`, `C#`, `Python`, `VUE.JS`, `REACT`, `Javascript`, `Management`, `PMO`, `Skill`, `C++`, `C`, `Mobile`, `Responsive`, `SQL`, `AzureDevOps`, `Azure`, `Security`, `Documentation`.

---

## 📋 Checklist de Finalização (Antes do Review)

- [ ] Campo `Analysis` preenchido com a Análise de Custo exata.
- [ ] Issue atribuída ao usuário logado.
- [ ] Issue com 1 a 3 tags de categoria.
- [ ] Estimativa inicial preenchida.
- [ ] Verificado se havia código não commitado antes de iniciar/trocar branch/merge/push.
- [ ] Se havia código não commitado, usuário foi alertado e decidiu se queria branch separada para análise/merge posterior.
- [ ] Executado `git fetch` e validado se a branch atual estava atrás de `origin/main` ou upstream antes de commit/push/PR/merge/main.
- [ ] Se a branch estava defasada, usuário decidiu explicitamente entre rebase, merge, pull/fast-forward, branch nova ou continuar sem atualizar.
- [ ] Arquivo local `ISSUE-<id>-discussion.md` criado e atualizado com o passo a passo.
- [ ] **TODO** o texto/log relevante do console replicado integralmente na `Discussion`.
- [ ] Campos `Completed Work` e `Finish Date` preenchidos com o tempo gasto.
- [ ] Estado alterado para `Review`.
- [ ] Commit realizado na branch de trabalho.
- [ ] Push realizado para a branch remota.
- [ ] Se solicitado levar para `main`, confirmar antes que a Issue está em `Done`.
- [ ] Se a Issue ainda estiver em `Review`, perguntar se usuário autorizado (`christian.novaes` ou `marcio.chicarino`, lowercase) revisou e quer mover para `Done`.
- [ ] Push/merge para `main` realizado somente depois da Issue em `Done`.

---

## 📈 Estados do Fluxo
- **Backlog/ToDo**: Planejamento.
- **Doing**: Execução ativa.
- **Review**: Conclusão da IA, aguardando validação humana.
- **Done**: Validação final autorizada somente por `christian.novaes` ou `marcio.chicarino` (comparar usuário em lowercase).

## 🧭 Organização do Board por Issue

- Antes de executar uma Issue, identificar as etapas planejadas no escopo, checklist, subtasks ou critérios de aceite.
- Verificar se o Board possui colunas/estados equivalentes para acompanhar essas etapas.
- Se não possuir, organizar o Board para ter as etapas necessárias ao acompanhamento da Issue.
- Manter o fluxo base (`Backlog`, `ToDo`, `Doing`, `Review`, `Done`) e adicionar apenas etapas realmente necessárias ao workflow da Issue.
