# 🔄 Issue Lifecycle (Ciclo de Vida da Issue)

Define os estados e responsabilidades da IA ao longo de uma Issue no Azure DevOps.

## ⛔ PROIBIÇÕES ABSOLUTAS (todos os modelos)

- **PROIBIDO** pedir confirmação para criar Issues ou mover estados no kanban.
- **PROIBIDO** perguntar "posso mover para Doing?", "confirma que posso avançar?", "devo mover para Review?".
- **PROIBIDO** aguardar input do usuário entre as transições de estado.
- Todas as movimentações são **automáticas, imediatas e silenciosas** — o usuário é apenas informado no resumo.

---

## 🛑 Regras Mandatórias (Absolute Mandates)

1. **Criação Antecipada**: **SEMPRE** criar as Issues antes de qualquer código. Seguir `ai_skills/workflows/backlog_management.md`.

2. **Tipo e Responsáveis**:
   - **Tipo**: `Issue` — nunca `Task`.
   - **Assign na criação**: `celeste@leste.com` (executora).
   - **Assign ao mover para Review**: `<NOME_REVISOR>` — o nome do usuário que solicitou à IA, informado no início.
   - **Reviewed By**: `<NOME_REVISOR>` — preenchido ao mover para Review.
   - **Required Review**: `Yes` — fixo em todas as Issues, definido na criação.

3. **Tags Obrigatórias** (cumulativas):
   - Uma tag por **repositório afetado** (nome exato). N repositórios → N tags.
   - Uma tag de **contexto** com palavra-chave da atividade (ex: `CriacaoEndpoint`, `CorrecaoBug`, `RefatoracaoAuth`).
   - Tag de tipo: `Backend` ou `Frontend`.

4. **Fluxo de Estados — Sequencial, Obrigatório e Automático**:

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

5. **Datas com Datetime Exato — Obrigatórias**:
   - **Start Date**: capturado no momento **exato** em que a Issue é movida para `Doing` (formato `YYYY-MM-DDTHH:MM:SSZ`).
   - **Finish Date**: capturado no momento **exato** em que a Issue é movida para `Review` (formato `YYYY-MM-DDTHH:MM:SSZ`).
   - **Completed Work**: total de tokens consumidos (entrada + saída) em toda a execução.

6. **Discussion — Espelho Integral do Console (MANDATO ABSOLUTO)**:
   - **TODO** o texto que a IA escreve no console **DEVE** ser postado integralmente na Discussion.
   - Inclui: análises, código gerado, outputs de comandos, logs, erros e como foram resolvidos.
   - **Proibido** resumir, filtrar ou omitir qualquer parte.
   - Cada etapa gera um novo comentário com timestamp exato — nunca editar anteriores.
   - Ao finalizar → **Comentário de Encerramento** com tudo que foi implementado (arquivos, commits, decisões, métricas).
   - Consultar `ai_skills/workflows/backlog_management.md` (Passo 6) para templates e momentos obrigatórios.

7. **Rastreabilidade**:
   - Branch: `session/<YYYYMMDD>-<LOGIN>-<contexto>` — uma branch por sessão, compartilhada por todas as Issues (ver `backlog_management.md` Passo 3).
   - Commits: referenciar o ID da Issue (`#ID`).
   - Push ao final de cada atividade concluída.

8. **Campos Técnicos Obrigatórios**:
   - **Activity**: `Deployment`, `Design`, `Development`, `Documentation`, `Requirements` ou `Testing`.
   - **Effort**: estimativa inicial em horas.

---

## 📋 Checklist de Finalização (Antes de mover para Review)

- [ ] Todos os comentários incrementais (espelho do console) na Discussion.
- [ ] Comentário de Encerramento com lista completa do que foi implementado.
- [ ] `Start Date` = datetime exato da transição para Doing.
- [ ] `Finish Date` = datetime exato da transição para Review.
- [ ] `Completed Work` = total de tokens utilizados.
- [ ] `Assign` alterado para `<NOME_REVISOR>`.
- [ ] `Reviewed By` = `<NOME_REVISOR>`.
- [ ] `Required Review` = `Yes`.
- [ ] Push realizado em todos os repositórios afetados.
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


Define os estados e responsabilidades da IA ao longo de uma Issue no Azure DevOps.

## 🛑 Regras Mandatórias (Absolute Mandates)

1. **Criação Antecipada**: **SEMPRE** criar as Issues antes de qualquer código. Seguir `ai_skills/workflows/backlog_management.md`.

2. **Tipo e Responsável**:
   - **Tipo**: `Issue` — nunca `Task`.
   - **Assign**: `celeste@leste.com` — fixo, sem exceção.

3. **Tags Obrigatórias** (cumulativas na mesma Issue):
   - Uma tag por **repositório afetado** (nome exato do repositório). Se tocar N repos → N tags.
   - Uma tag de **contexto** com palavra-chave que identifique a atividade (ex: `CriacaoEndpoint`, `CorrecaoBug`, `RefatoracaoAuth`, `AjusteLayout`, `MigracaoSchema`).
   - Tag de tipo de projeto: `Backend` ou `Frontend`.

4. **Fluxo de Estados — Sequencial e Obrigatório**:

   ```
   Backlog ──► To Do ──► Doing ──► Review
                                     └── Done (apenas humanos)
   ```

   | Transição          | Quando                              | Quem   |
   | ------------------ | ----------------------------------- | ------ |
   | Backlog → To Do    | Imediatamente após criar a Issue    | IA     |
   | To Do → Doing      | Ao iniciar a implementação          | IA     |
   | Doing → Review     | Ao concluir toda a implementação    | IA     |
   | Review → Done      | Após validação humana               | Humano |

   > A IA **NUNCA** move para `Done`.

5. **Datas e Métricas — Obrigatórias**:
   - **Start Date** (`Microsoft.VSTS.Scheduling.StartDate`): preencher ao mover para `Doing`.
   - **Finish Date** (`Microsoft.VSTS.Scheduling.FinishDate`): preencher ao mover para `Review`.
   - **Completed Work** (`Microsoft.VSTS.Scheduling.CompletedWork`): preencher com o **total de tokens consumidos** (entrada + saída) durante toda a execução da Issue.

6. **Discussion — Comentários Incrementais Obrigatórios**:
   - A cada nova etapa executada → novo comentário na Discussion.
   - **Nunca editar** comentários anteriores — são registros imutáveis de evidência.
   - Ao finalizar → adicionar o **Comentário de Encerramento** com resumo completo do que foi entregue.
   - Consultar `ai_skills/workflows/backlog_management.md` (Passo 6) para templates.

7. **Rastreabilidade**:
   - Branch: `session/<YYYYMMDD>-<LOGIN>-<contexto>` — uma branch por sessão, compartilhada por todas as Issues (ver `backlog_management.md` Passo 3).
   - Commits: mensagem deve referenciar o ID da Issue (`#ID`).
   - Push ao final de cada atividade concluída.

8. **Campos Técnicos Obrigatórios**:
   - **Activity**: `Deployment`, `Design`, `Development`, `Documentation`, `Requirements` ou `Testing`.
   - **Effort**: estimativa inicial em horas.

---

## 📋 Checklist de Finalização (Antes de mover para Review)

- [ ] Todos os comentários incrementais registrados na Discussion.
- [ ] Comentário de Encerramento adicionado com resumo da entrega.
- [ ] `Start Date` preenchido com a data de início real.
- [ ] `Finish Date` preenchido com a data de conclusão.
- [ ] `Completed Work` preenchido com o total de tokens utilizados.
- [ ] `Activity` e `Effort` preenchidos.
- [ ] Push realizado para o remoto em todos os repositórios afetados.
- [ ] Estado alterado para `Review`.

---

## 📈 Referência Rápida dos Estados

| Estado   | Significado                                      | Responsável |
| -------- | ------------------------------------------------ | ----------- |
| Backlog  | Issue registrada, aguardando priorização         | IA          |
| To Do    | Priorizada, pronta para iniciar                  | IA          |
| Doing    | Em execução ativa — Start Date preenchido        | IA          |
| Review   | Entregue — Finish Date + Completed Work + resumo | IA          |
| Done     | Validado e aceito pelo time                      | Humano      |

