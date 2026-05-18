# 🔄 Issue Lifecycle (Ciclo de Vida da Issue)

Define os estados e responsabilidades da IA ao longo de uma Issue no Azure DevOps.

## ⛔ PROIBIÇÕES ABSOLUTAS (todos os modelos)

- **PROIBIDO** pedir confirmação para criar Issues ou mover estados no kanban.
- **PROIBIDO** perguntar "posso mover para Doing?", "confirma que posso avançar?", "devo mover para Review?".
- **PROIBIDO** perguntar antes de selecionar/criar Iteration vigente ou mover Issues/Bugs em `Backlog`/`To Do` para a Iteration atual.
- **PROIBIDO** mover Issue para `Review` sem registrar Test Plan/Test Cases/evidências, salvo se Test Plans estiver indisponível e o bloqueio estiver documentado na Discussion.
- **PROIBIDO** deixar bug reportado pelo usuário sem work item `Bug` no Azure DevOps.
- **PROIBIDO** usar comentário que corte conteúdo multilinha na primeira linha; Discussion deve ser registrada via `System.History`/JSON Patch.
- **PROIBIDO** aguardar input do usuário entre as transições de estado.
- Todas as movimentações são **automáticas, imediatas e silenciosas** — o usuário é apenas informado no resumo.

---

## 🛑 Regras Mandatórias (Absolute Mandates)

1. **Criação Antecipada**: **SEMPRE** criar as Issues antes de qualquer código. Bug reportado pelo usuário deve criar work item `Bug` automaticamente. Seguir `ai_skills/workflows/backlog_management.md` e `ai_skills/workflows/bug_reporting.md`.

2. **Tipo e Responsáveis**:
   - **Tipo**: `Issue` — nunca `Task`.
   - **Bug reportado pelo usuário**: `Bug` — vincular à Issue original com `Related` quando identificável; se não for identificável, criar solto.
   - **Assign na criação**: `celeste@leste.com` (executora).
   - **Assign ao mover para Review**: `<NOME_REVISOR>` — o nome do usuário que solicitou à IA, informado no início.
   - **Reviewed By**: `<NOME_REVISOR>` — preenchido ao mover para Review.
   - **Required Review**: `Yes` — fixo em todas as Issues, definido na criação.

3. **Tags Obrigatórias** (cumulativas):
   - Uma tag por **repositório afetado** (nome exato). N repositórios → N tags.
   - Uma tag de **contexto** com palavra-chave da atividade (ex: `CriacaoEndpoint`, `CorrecaoBug`, `RefatoracaoAuth`).
   - Tag de tipo: `Backend` ou `Frontend`.

4. **Iteration Vigente — Obrigatória e Automática**:
   - Ao iniciar sessão/branch, selecionar a Iteration existente cujo intervalo contenha a data atual.
   - Se nenhuma Iteration vigente existir, criar a Iteration trimestral do projeto no formato `YYYY.QN` (ex: `2026.Q2`) como fallback.
   - Mover automaticamente Issues/Bugs em `Backlog` e `To Do` para a Iteration vigente.
   - Não mover Issues em `Doing`, `Review` ou `Done`.
   - Seguir `ai_skills/workflows/backlog_management.md` Passo 0.5.

5. **Test Plans — Obrigatório por Iteration**:
   - Criar/reutilizar Test Plan no formato `<PROJECT_NAME> - <ITERATION_NAME>`.
   - Criar suites conforme o plano: `Backend`, `Frontend`, `Integracao`, `Regressao`, `Smoke`.
   - Criar Test Cases a partir dos critérios de aceite e vincular às Issues/Bugs.
   - Registrar Test Plan, suites, Test Cases e resultado no comentário final antes de mover para `Review`.
   - Seguir `ai_skills/workflows/test_plans.md`.

6. **Fluxo de Estados — Sequencial, Obrigatório e Automático**:

   ```
   Backlog ──► To Do ──► Doing ──► Review
                                     └── Done (apenas humanos)
   ```

   | Transição          | Quando                              | Quem   | Pergunta ao usuário? |
   | ------------------ | ----------------------------------- | ------ | -------------------- |
   | Backlog → To Do    | Imediatamente após criar a Issue    | IA     | ❌ Nunca             |
   | To Do → Doing      | Ao iniciar a implementação          | IA     | ❌ Nunca             |
   | Doing → Review     | Ao concluir toda a implementação    | IA     | ❌ Nunca             |
   | Review → Done      | Após validação humana               | Humano | —                    |

   > A IA **NUNCA** move para `Done` e **NUNCA** pergunta antes de mover.

7. **Datas com Datetime Exato — Obrigatórias**:
   - **Start Date**: capturado no momento **exato** em que a Issue/Bug é movida para `Doing` (formato `YYYY-MM-DDTHH:MM:SSZ`).
   - **Estimated Date**: preenchido na criação/início da atividade e atualizado quando o escopo ou previsão mudar (`Microsoft.VSTS.Scheduling.TargetDate` ou campo customizado equivalente).
   - **Finish Date**: capturado no momento **exato** em que a Issue é movida para `Review` (formato `YYYY-MM-DDTHH:MM:SSZ`).
   - **Completed Work**: total de tokens consumidos (entrada + saída) em toda a execução.

8. **Discussion — Espelho Integral do Console (MANDATO ABSOLUTO)**:
   - **TODO** o texto que a IA escreve no console **DEVE** ser postado integralmente na Discussion.
   - Para GitHub Copilot CLI, isso inclui **todo texto gerado pelo Copilot na CLI**: preambulos, analises, decisoes, avisos, perguntas, respostas, resultados finais e mensagens de erro.
   - Inclui: análises, código gerado, outputs de comandos, logs, erros e como foram resolvidos.
   - **Proibido** resumir, filtrar ou omitir qualquer parte.
   - Cada etapa gera um novo comentário com timestamp exato — nunca editar anteriores.
   - Comentários multilinha devem ser postados via `System.History`/JSON Patch para preservar o conteúdo completo.
   - Ao finalizar → **Comentário de Encerramento** com tudo que foi implementado (arquivos, commits, decisões, métricas).
   - A mem├│ria local deve registrar IDs das Issues e os principais comentários/textos do GitHub Copilot CLI, confirmando que o espelho integral foi publicado na Issue.
   - Consultar `ai_skills/workflows/backlog_management.md` (Passo 6) para templates e momentos obrigatórios.

9. **Rastreabilidade**:
   - Branch: `session/<YYYYMMDD>-<LOGIN>-<contexto>` — uma branch por sessão, compartilhada por todas as Issues (ver `backlog_management.md` Passo 3).
   - Commits: referenciar o ID da Issue/Bug com `AB#<ID>` na mensagem para sensibilizar/linkar o commit no Azure DevOps. Se um commit atender múltiplos itens, incluir todos (`AB#123 AB#456`).
   - Test Cases: vincular cada Test Case à Issue/Bug correspondente.
   - Push ao final de cada atividade concluída.

10. **Campos Técnicos Obrigatórios**:
   - **Activity**: `Deployment`, `Design`, `Development`, `Documentation`, `Requirements` ou `Testing`.
   - **Effort**: estimativa inicial em horas.

---

## 📋 Checklist de Finalização (Antes de mover para Review)

- [ ] Todos os comentários incrementais (espelho do console) na Discussion.
- [ ] Todo texto gerado pelo GitHub Copilot CLI na CLI foi postado integralmente na Discussion da(s) Issue(s).
- [ ] Memória local registra IDs das Issues e principais comentários/textos da IA na CLI.
- [ ] Comentário de Encerramento com lista completa do que foi implementado.
- [ ] `Start Date` = datetime exato da transição para Doing.
- [ ] `Estimated Date` preenchido/atualizado no campo `TargetDate` ou equivalente.
- [ ] `Finish Date` = datetime exato da transição para Review.
- [ ] `Completed Work` = total de tokens utilizados.
- [ ] `Assign` alterado para `<NOME_REVISOR>`.
- [ ] `Reviewed By` = `<NOME_REVISOR>`.
- [ ] `Required Review` = `Yes`.
- [ ] Test Plan da Iteration existe ou bloqueio foi documentado.
- [ ] Test Cases foram criados/vinculados ou justificativa foi registrada.
- [ ] Resultado dos Test Cases/evidências incluído no comentário final.
- [ ] `.gitignore` existe e cobre build outputs, caches, dependências, logs, temporários e arquivos de IDE/SO.
- [ ] Nenhum artefato gerado, pasta de dependência, cache, log ou arquivo local foi incluído no commit.
- [ ] `.gitignore` cobre arquivos locais de desenvolvimento com segredos.
- [ ] Arquivos versionáveis não contêm credenciais reais; usam Key Vault, variáveis de ambiente, variáveis secretas de pipeline ou placeholders.
- [ ] APIs/endpoints novos ou alterados exigem autenticação/autorização.
- [ ] Teste/cenário de acesso sem credencial válida retorna `401` ou `403`.
- [ ] Endpoint anônimo, se existir, está justificado na Issue, documentado no Scalar e limitado ao mínimo necessário.
- [ ] Push realizado em todos os repositórios afetados.
- [ ] Commits referenciam `AB#<ID>` das Issues/Bugs impactadas.
- [ ] Estado = `Review`.

---

## 📈 Referência Rápida dos Estados

| Estado   | Significado                                                  | Responsável |
| -------- | ------------------------------------------------------------ | ----------- |
| Backlog  | Issue registrada                                             | IA          |
| To Do    | Priorizada, pronta para iniciar                              | IA          |
| Doing    | Em execução — Start DateTime registrado                      | IA          |
| Review   | Entregue — Finish DateTime + Completed Work + Assign mudado  | IA          |
| Done     | Validado e aceito                                            | Humano      |

