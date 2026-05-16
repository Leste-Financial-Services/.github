# 📋 Backlog Management Workflow (Gerenciamento de Backlog Obrigatório)

> ## ⛔ REGRA ABSOLUTA — VÁLIDA PARA TODOS OS MODELOS (GPT, Claude, Gemini, etc.)
>
> **Criar Issues no Azure DevOps é OBRIGATÓRIO e AUTOMÁTICO para TODA solicitação.**
> **Mover Issues no kanban é OBRIGATÓRIO e AUTOMÁTICO a cada transição de estado.**
> **PROIBIDO** pedir confirmação para criar Issues: "posso criar?", "deseja que eu crie?", ou qualquer variação.
> **PROIBIDO** pedir confirmação para mover Issues: "posso mover para Doing?", "confirma a movimentação?", ou qualquer variação.
> **PROIBIDO** iniciar qualquer implementação sem ter criado as Issues primeiro.
> **PROIBIDO** justificar a não-criação com frases como "vou direto ao código", "para agilizar", "pulando o backlog".
> A criação de Issues, movimentação no kanban e criação de branches acontece **sempre, imediatamente e sem aprovação prévia**.

---

## 🔎 Passo 0 — Verificar Issues Abertas em To Do (Início de Sessão)

> **Executar sempre que uma nova sessão ou branch for iniciada**, antes de criar novas Issues.

### Buscar Issues existentes em To Do atribuídas à Celeste

```bash
az boards query \
  --wiql "SELECT [System.Id], [System.Title], [System.TeamProject], [System.Tags]
          FROM WorkItems
          WHERE [System.WorkItemType] = 'Issue'
            AND [System.State] = 'To Do'
            AND [System.AssignedTo] = 'celeste@leste.com'
          ORDER BY [System.CreatedDate] ASC" \
  --org "https://dev.azure.com/LesteDevOps" \
  -o json
```

### Apresentar ao usuário e perguntar se devem ser incluídas

Se houver Issues em `To Do`, exibir antes de prosseguir:

```
📋 Issues abertas em To Do (atribuídas à Celeste):

| #  | Issue ID | Projeto      | Título                            | Tags                     |
|----|----------|--------------|-----------------------------------|--------------------------|
| 1  | #XXXX    | <PROJETO>    | <Título da Issue>                 | <Tags>                   |
| 2  | #YYYY    | <PROJETO>    | <Título da Issue>                 | <Tags>                   |

➕ Deseja incluir alguma dessas Issues na sessão atual?
   Informe os números (ex: "1, 3") ou "nenhuma" para continuar apenas com a solicitação atual.
```

> **Esta é a ÚNICA pergunta permitida nesta etapa.** Após a resposta, prosseguir imediatamente.

### Regras de inclusão

| Resposta do usuário    | Comportamento                                                                 |
| ---------------------- | ----------------------------------------------------------------------------- |
| Números (ex: "1, 2")   | Adicionar as Issues selecionadas à fila de execução da sessão                 |
| "todas" / "all"        | Incluir todas as Issues listadas                                              |
| "nenhuma" / "não" / "" | Ignorar a lista e prosseguir apenas com a solicitação atual                   |

> Se **não houver** Issues em `To Do`, pular este passo silenciosamente e continuar.

---

## 🗓️ Passo 0.5 — Criar/Atualizar Iteration Trimestral

> **Executar sempre que uma nova sessão ou branch for iniciada**, após consultar Issues abertas em `To Do` e antes de criar novas Issues.
> As Iterations são trimestrais e seguem obrigatoriamente o formato `YYYY.QN`.

### Regra de nomeação

| Período | Nome da Iteration | Data inicial | Data final |
| ------- | ----------------- | ------------ | ---------- |
| Janeiro a Março | `YYYY.Q1` | `YYYY-01-01` | `YYYY-03-31` |
| Abril a Junho | `YYYY.Q2` | `YYYY-04-01` | `YYYY-06-30` |
| Julho a Setembro | `YYYY.Q3` | `YYYY-07-01` | `YYYY-09-30` |
| Outubro a Dezembro | `YYYY.Q4` | `YYYY-10-01` | `YYYY-12-31` |

Exemplo: qualquer sessão iniciada em maio/2026 usa a Iteration `2026.Q2`.

### Calcular trimestre atual

```bash
YEAR=$(date -u +"%Y")
MONTH=$(date -u +"%m")

if [ "$MONTH" -le 3 ]; then
  QUARTER="Q1"
  START_DATE="${YEAR}-01-01"
  FINISH_DATE="${YEAR}-03-31"
elif [ "$MONTH" -le 6 ]; then
  QUARTER="Q2"
  START_DATE="${YEAR}-04-01"
  FINISH_DATE="${YEAR}-06-30"
elif [ "$MONTH" -le 9 ]; then
  QUARTER="Q3"
  START_DATE="${YEAR}-07-01"
  FINISH_DATE="${YEAR}-09-30"
else
  QUARTER="Q4"
  START_DATE="${YEAR}-10-01"
  FINISH_DATE="${YEAR}-12-31"
fi

ITERATION_NAME="${YEAR}.${QUARTER}"
```

### Criar a Iteration no projeto, se não existir

Executar para **cada projeto/repositório impactado** pela sessão:

```bash
PROJECT_NAME="<NOME_REPOSITORIO_OU_PROJETO>"
ITERATION_PATH="\\${PROJECT_NAME}\\${ITERATION_NAME}"

ITERATION_ID=$(az boards iteration project list \
  --project "$PROJECT_NAME" \
  --org "https://dev.azure.com/LesteDevOps" \
  --path "$ITERATION_PATH" \
  --query "[0].identifier" -o tsv 2>/dev/null)

if [ -z "$ITERATION_ID" ]; then
  ITERATION_ID=$(az boards iteration project create \
    --name "$ITERATION_NAME" \
    --project "$PROJECT_NAME" \
    --org "https://dev.azure.com/LesteDevOps" \
    --start-date "$START_DATE" \
    --finish-date "$FINISH_DATE" \
    --query "identifier" -o tsv)
fi

# Adicionar a Iteration ao time padrão do projeto para aparecer como Sprint.
# Se o projeto não tiver time com o mesmo nome, não bloquear a execução.
az boards iteration team add \
  --id "$ITERATION_ID" \
  --team "$PROJECT_NAME" \
  --project "$PROJECT_NAME" \
  --org "https://dev.azure.com/LesteDevOps" \
  2>/dev/null || true
```

> Se a Iteration já existir, reutilizar. Se o vínculo com o time já existir, continuar sem erro. Não perguntar ao usuário.

### Mover Issues em Backlog e To Do para a Iteration trimestral

Mover automaticamente todas as Issues do projeto que estejam em `Backlog` ou `To Do` para a Iteration atual:

```bash
ISSUES_TO_MOVE=$(az boards query \
  --wiql "SELECT [System.Id]
          FROM WorkItems
          WHERE [System.WorkItemType] = 'Issue'
            AND [System.TeamProject] = '$PROJECT_NAME'
            AND [System.State] IN ('Backlog', 'To Do')" \
  --org "https://dev.azure.com/LesteDevOps" \
  --query "[].id" -o tsv)

for ISSUE_ID in $ISSUES_TO_MOVE; do
  az boards work-item update \
    --id "$ISSUE_ID" \
    --org "https://dev.azure.com/LesteDevOps" \
    --fields "System.IterationPath=${PROJECT_NAME}\\${ITERATION_NAME}"
done
```

> **Sem confirmação**: a criação da Iteration e a movimentação das Issues em `Backlog`/`To Do` são automáticas.
> **Escopo**: não mover Issues em `Doing`, `Review` ou `Done`.

---

## 🔍 Passo 1 — Análise de Impacto por Projeto

Para cada solicitação, a IA deve categorizar o impacto:

| Tipo de Impacto       | Critério                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------ |
| **Backend apenas**    | Mudanças em API, banco de dados, regras de negócio, serviços, domínio ou infraestrutura.   |
| **Frontend apenas**   | Mudanças em UI, componentes Vue/React, stores, serviços TypeScript ou rotas do cliente.    |
| **Ambos (Full-Stack)**| Qualquer mudança que exija contrato de dados novo ou ajuste em endpoints já existentes.    |

### Detecção Automática do Revisor (sem perguntar ao usuário)

A IA **DEVE** detectar automaticamente o email do usuário logado no laptop usando a seguinte ordem de precedência:

```bash
# 1ª opção — Azure CLI (conta logada no az login)
NOME_REVISOR=$(az account show --query "user.name" -o tsv 2>/dev/null)

# 2ª opção — Git config global
if [ -z "$NOME_REVISOR" ]; then
  NOME_REVISOR=$(git config --global user.email 2>/dev/null)
fi

# 3ª opção — Variável de ambiente do sistema operacional
if [ -z "$NOME_REVISOR" ]; then
  NOME_REVISOR="${USERNAME}@leste.com"   # Windows
  # ou: NOME_REVISOR="${USER}@leste.com" # Linux/macOS
fi

echo "👤 Revisor detectado: $NOME_REVISOR"
```

> Armazenar como `<NOME_REVISOR>` — será usado nos campos `Reviewed By` e `AssignedTo` ao mover para Review.
> **Nunca perguntar ao usuário** — a detecção é sempre automática.

### Matriz de Decisão

```
Solicitação recebida
       │
       ├─ Envolve API / DB / Backend Logic?  ──► Criar Issue no BACKEND
       │
       ├─ Envolve UI / Componentes / Frontend Logic?  ──► Criar Issue no FRONTEND
       │
       └─ Envolve contrato de dados (DTO / Interface)?  ──► Criar Issues em AMBOS e vincular
```

---

## 🗺️ Passo 1.5 — Plano de Ação Estruturado

> **Obrigatório para toda nova solicitação.** Antes de criar qualquer Issue, a IA deve decompor o pedido em Issues atômicas, identificar dependências e organizar a execução em ondas paralelas.

### Estrutura do Plano

Apresentar ao usuário **antes de criar as Issues**:

```
📋 Plano de Ação — <Título resumido da solicitação>

🌊 Onda 1 — Execução Paralela (sem dependências)
┌─────────────────────────────────────────────────────────────────┐
│ Issue A │ [BACKEND]  │ <Título>  │ Repo: <repo>     │ 🤖 Agente 1 │
│ Issue B │ [FRONTEND] │ <Título>  │ Repo: <repo>     │ 🤖 Agente 2 │
└─────────────────────────────────────────────────────────────────┘

🌊 Onda 2 — Execução após Onda 1 (depende de A e/ou B)
┌─────────────────────────────────────────────────────────────────┐
│ Issue C │ [BACKEND]  │ <Título>  │ Depende de: A    │ 🤖 Agente 1 │
│ Issue D │ [FRONTEND] │ <Título>  │ Depende de: B    │ 🤖 Agente 2 │
└─────────────────────────────────────────────────────────────────┘

🌊 Onda 3 — Execução após Onda 2
┌─────────────────────────────────────────────────────────────────┐
│ Issue E │ [BACKEND]  │ <Título>  │ Depende de: C, D │ 🤖 Agente 1 │
└─────────────────────────────────────────────────────────────────┘

📊 Resumo
- Total de Issues   : <N>
- Ondas de execução : <N>
- Paralelismo       : <ex: "Onda 1 pode ter 2 agentes simultâneos">

✅ Confirma o plano? Responda "sim" para criar as Issues, ou descreva ajustes.
```

### Regras de decomposição

| Regra | Descrição |
| ----- | --------- |
| **Atômica** | Cada Issue deve ter escopo único e ser entregável independente dentro de sua onda |
| **Dependência real** | Só marcar dependência se Issue B precisar do output técnico de Issue A (ex: endpoint criado no backend antes de consumir no frontend) |
| **Paralelismo máximo** | Agrupar na mesma onda tudo que pode ser desenvolvido simultaneamente por agentes diferentes |
| **Granularidade** | Preferir Issues menores e paralelas a Issues grandes e sequenciais |

### Tipos de dependência entre Issues

| Tipo de relação  | Quando usar                                                           | Relation-type DevOps |
| ---------------- | --------------------------------------------------------------------- | -------------------- |
| **Predecessor**  | Issue B só pode iniciar após Issue A estar em Review                  | `Predecessor`        |
| **Related**      | Issues do mesmo contexto sem bloqueio técnico entre elas              | `Related`            |
| **Duplicate**    | Issue duplicada de outra já existente                                 | `Duplicate`          |

---

## 📝 Passo 2 — Criação Obrigatória de Issues no Azure DevOps

> **Organização**: `https://dev.azure.com/LesteDevOps`
> **Tipo**: `Issue` — nunca `Task`.
> **Assign na criação**: `celeste@leste.com` (executora). Muda para `<NOME_REVISOR>` ao mover para Review.
> **Tags obrigatórias**:
> - Uma tag por **repositório afetado** (nome exato). N repositórios → N tags.
> - Uma tag de **contexto** com palavra-chave da atividade (ex: `CriacaoEndpoint`, `CorrecaoBug`, `RefatoracaoAuth`).

### 2.1 Para o projeto Backend
```bash
ISSUE_ID=$(az boards work-item create \
  --title "[BACKEND] <Título descritivo da issue>" \
  --type "Issue" \
  --project "<NOME_REPOSITORIO_BACKEND>" \
  --org "https://dev.azure.com/LesteDevOps" \
  --fields \
    "System.AreaPath=<NOME_REPOSITORIO_BACKEND>" \
    "System.AssignedTo=celeste@leste.com" \
    "System.Tags=Backend;<NOME_REPOSITORIO_BACKEND>;<PALAVRA_CONTEXTO>" \
    "Microsoft.VSTS.Common.Activity=Development" \
    "Microsoft.VSTS.Common.RequiresReview=Yes" \
    "System.Description=<Descrição detalhada do escopo, critérios de aceite e dependências>" \
  --query "id" -o tsv)

az boards work-item update \
  --id $ISSUE_ID \
  --org "https://dev.azure.com/LesteDevOps" \
  --state "To Do"
```

### 2.2 Para o projeto Frontend
```bash
ISSUE_ID=$(az boards work-item create \
  --title "[FRONTEND] <Título descritivo da issue>" \
  --type "Issue" \
  --project "<NOME_REPOSITORIO_FRONTEND>" \
  --org "https://dev.azure.com/LesteDevOps" \
  --fields \
    "System.AreaPath=<NOME_REPOSITORIO_FRONTEND>" \
    "System.AssignedTo=celeste@leste.com" \
    "System.Tags=Frontend;<NOME_REPOSITORIO_FRONTEND>;<PALAVRA_CONTEXTO>" \
    "Microsoft.VSTS.Common.Activity=Development" \
    "Microsoft.VSTS.Common.RequiresReview=Yes" \
    "System.Description=<Descrição detalhada do escopo, critérios de aceite e dependências>" \
  --query "id" -o tsv)

az boards work-item update \
  --id $ISSUE_ID \
  --org "https://dev.azure.com/LesteDevOps" \
  --state "To Do"
```

### 2.3 Issue tocando múltiplos repositórios
```bash
"System.Tags=Backend;<REPO_1>;<REPO_2>;<REPO_N>;<PALAVRA_CONTEXTO>"
```

### 2.4 Vincular Issues — Related (mesmo contexto, sem bloqueio técnico)
```bash
az devops work-item relation add \
  --id <ID_ISSUE_A> \
  --target-id <ID_ISSUE_B> \
  --relation-type "Related" \
  --org "https://dev.azure.com/LesteDevOps"
```

### 2.5 Vincular Issues — Predecessor (dependência bloqueante entre ondas)

Usar quando Issue B só pode iniciar após Issue A estar concluída (ondas diferentes no plano):

```bash
# Issue B depende de Issue A (A é predecessor de B)
az devops work-item relation add \
  --id <ID_ISSUE_B> \
  --target-id <ID_ISSUE_A> \
  --relation-type "Predecessor" \
  --org "https://dev.azure.com/LesteDevOps"
```

> Registrar **todas** as dependências do Plano de Ação (Passo 1.5) como relações `Predecessor` no DevOps imediatamente após criar as Issues, seguindo as ondas definidas no plano.

---

## 🌿 Passo 3 — Criação de Branch por Sessão

> **Branch por sessão**: Uma única branch é criada **no início da sessão**, compartilhada por todas as Issues executadas naquela sessão. Se a branch já existir (sessão em andamento), apenas fazer checkout.

```bash
# Definir a branch de sessão (executar UMA VEZ por sessão, por repositório)
SESSION_DATE=$(date -u +"%Y%m%d")
SESSION_USER=$(az account show --query "user.name" -o tsv 2>/dev/null | sed 's/@.*//')
if [ -z "$SESSION_USER" ]; then
  SESSION_USER=$(git config --global user.email 2>/dev/null | sed 's/@.*//')
fi
SESSION_CONTEXT="<contexto-kebab-case>" # Ver regra abaixo

SESSION_BRANCH="session/${SESSION_DATE}-${SESSION_USER}-${SESSION_CONTEXT}"

# Backend — criar se não existir, senão apenas mudar para ela
git -C "<CAMINHO_BACKEND>" checkout -b $SESSION_BRANCH 2>/dev/null || git -C "<CAMINHO_BACKEND>" checkout $SESSION_BRANCH

# Frontend — criar se não existir, senão apenas mudar para ela
git -C "<CAMINHO_FRONTEND>" checkout -b $SESSION_BRANCH 2>/dev/null || git -C "<CAMINHO_FRONTEND>" checkout $SESSION_BRANCH
```

> **Formato do nome**: `session/<YYYYMMDD>-<LOGIN>-<contexto-kebab-case>`
>
> | Parte        | Valor                                                                                       |
> | ------------ | ------------------------------------------------------------------------------------------- |
> | `<YYYYMMDD>` | Data atual no fuso UTC                                                                      |
> | `<LOGIN>`    | Parte local do email do usuário logado (antes do `@`), detectada via `az account show` ou `git config user.email` |
> | `<contexto>` | **Se o usuário especificou** um nome/contexto → usar exatamente o que foi informado (kebab-case). **Se não especificou** → a IA infere a partir das Issues da sessão (ex: `correcao-relatorios`, `integracao-pagamentos`) |
>
> **Regra**: Armazenar `$SESSION_BRANCH` no início da sessão e reutilizar em todas as Issues seguintes.

| Projeto   | Issue ID | Tags                          | Assign              | Estado |
|-----------|----------|-------------------------------|---------------------|--------|
| Backend   | #XXXX    | Backend;<repo>;<contexto>     | celeste@leste.com   | To Do  |
| Frontend  | #YYYY    | Frontend;<repo>;<contexto>    | celeste@leste.com   | To Do  |

🔗 Issues vinculadas: #XXXX ↔ #YYYY
🌿 Branch de sessão: session/<YYYYMMDD-HHMMSS>-<contexto>
👤 Reviewed By (ao finalizar): <NOME_REVISOR>
▶️  Movendo para Doing e iniciando implementação...
```

---

## 🔄 Passo 5 — Gestão do Ciclo de Estado da Issue

```
Backlog ──► To Do ──► Doing ──► Review
                                  └── Done (apenas humanos)
```

| Transição         | Quando                              | Ação adicional                                              |
| ----------------- | ----------------------------------- | ----------------------------------------------------------- |
| `Backlog → To Do` | Logo após criação                   | —                                                           |
| `To Do → Doing`   | Ao iniciar implementação            | Registrar **Start DateTime** exato + comentário na Discussion |
| `Doing → Review`  | Ao concluir implementação           | Registrar **Finish DateTime** exato + Completed Work + mudar Assign para `<NOME_REVISOR>` + comentário final |

### Mover para Doing + Start DateTime exato
```bash
START_DATETIME=$(date -u +"%Y-%m-%dT%H:%M:%SZ")   # capturar no momento exato da transição

az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --state "Doing" \
  --fields "Microsoft.VSTS.Scheduling.StartDate=$START_DATETIME"
```

### Mover para Review + Finish DateTime + Campos de Revisão
```bash
FINISH_DATETIME=$(date -u +"%Y-%m-%dT%H:%M:%SZ")  # capturar no momento exato da transição

az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --state "Review" \
  --fields \
    "Microsoft.VSTS.Scheduling.FinishDate=$FINISH_DATETIME" \
    "Microsoft.VSTS.Scheduling.CompletedWork=<TOTAL_TOKENS_INPUT_MAIS_OUTPUT>" \
    "System.AssignedTo=<NOME_REVISOR>" \
    "Microsoft.VSTS.Common.ReviewedBy=<NOME_REVISOR>" \
    "Microsoft.VSTS.Common.RequiresReview=Yes"
```

> **Completed Work** = total de tokens consumidos (entrada + saída) em toda a execução da Issue.

---

## 💬 Passo 6 — Discussion: Espelho Integral do Console

> **MANDATO ABSOLUTO**: Todo texto que a IA escreve no console **DEVE** ser postado integralmente na Discussion da Issue correspondente.
> Isso inclui: análises, decisões técnicas, código gerado, outputs de comandos, logs, erros encontrados e como foram resolvidos, e o resumo final.
> **Proibido resumir, filtrar ou omitir** qualquer parte do conteúdo do console.

### Regras de Postagem

1. **Espelho integral**: O comentário é uma cópia exata do que foi escrito no console naquela etapa.
2. **Incremental**: Cada etapa gera um novo comentário — nunca editar anteriores.
3. **Timestamp exato**: Usar o datetime real do momento do post (`YYYY-MM-DDTHH:MM:SS`).

### Comando padrão de comentário
```bash
COMMENT_DATETIME=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --discussion "## ⚙️ [$COMMENT_DATETIME] <Título da Etapa>

<CONTEÚDO INTEGRAL exatamente como foi escrito no console — análise, código, outputs, decisões, logs>"
```

### Momentos obrigatórios de comentário

| # | Momento                           | Conteúdo do comentário (espelho do console)                         |
|---|-----------------------------------|---------------------------------------------------------------------|
| 1 | Issue criada (→ To Do)            | Análise de impacto completa, escopo, critérios de aceite            |
| 2 | Início da implementação (→ Doing) | Estratégia adotada, arquivos/camadas que serão tocados              |
| 3 | Cada arquivo criado/modificado    | Código completo gerado ou diff completo da modificação              |
| 4 | Comando executado (shell/git)     | Comando completo + output completo retornado                        |
| 5 | Erro encontrado e resolvido       | Descrição do erro + solução aplicada + código/output após correção  |
| 6 | Testes executados                 | Output completo dos testes (passou/falhou, cobertura, erros)        |
| 7 | Commit/Push realizado             | Hash do commit, mensagem, branch, repositório de destino            |
| 8 | Issue finalizada (→ Review)       | **Comentário de Encerramento** — template abaixo                    |

### Template do Comentário de Encerramento (obrigatório ao mover para Review)
```bash
az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --discussion "## ✅ [$FINISH_DATETIME] Entrega Concluída — Resumo Final

### O que foi implementado
<Lista detalhada de TUDO que foi criado, modificado ou removido — sem omissões>

### Arquivos alterados por repositório
**<NOME_REPO_1>** (branch: `$SESSION_BRANCH` | commit: <HASH>):
- <caminho/arquivo.ext> — <o que foi feito neste arquivo>
- <caminho/arquivo.ext> — <o que foi feito neste arquivo>

**<NOME_REPO_2>** (branch: `$SESSION_BRANCH` | commit: <HASH>):
- <caminho/arquivo.ext> — <o que foi feito neste arquivo>

### Testes
<Output completo dos testes executados>

### Decisões técnicas tomadas
<Justificativas e trade-offs relevantes>

### Métricas
- **Start:** <START_DATETIME>
- **Finish:** <FINISH_DATETIME>
- **Tokens utilizados:** <TOTAL> (registrado em Completed Work)
- **Reviewed By:** <NOME_REVISOR>

### Pendências para o Revisor
<Listar itens ou escrever 'Nenhuma'>"
```

---

## 🛑 Condição de Bloqueio

Se `ESTE_PROJETO.Url` ou `PROJETO_IRMAO.Url` estiverem como `[MISSING]`:
- **NÃO** criar Issues nem branches.
- **PARAR** e solicitar que o usuário execute `setup-leste-ai.ps1` com os repositórios configurados.


---

## 🔍 Passo 1 — Análise de Impacto por Projeto

Para cada solicitação, a IA deve categorizar o impacto:

| Tipo de Impacto       | Critério                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------ |
| **Backend apenas**    | Mudanças em API, banco de dados, regras de negócio, serviços, domínio ou infraestrutura.   |
| **Frontend apenas**   | Mudanças em UI, componentes Vue, stores Pinia, serviços TypeScript ou rotas do cliente.    |
| **Ambos (Full-Stack)**| Qualquer mudança que exija contrato de dados novo ou ajuste em endpoints já existentes.    |

### Matriz de Decisão

```
Solicitação recebida
       │
       ├─ Envolve API / DB / Backend Logic?  ──► Criar Issue no BACKEND
       │
       ├─ Envolve UI / Componentes / Frontend Logic?  ──► Criar Issue no FRONTEND
       │
       └─ Envolve contrato de dados (DTO / Interface)?  ──► Criar Issues em AMBOS e vincular
```

---

## 📝 Passo 2 — Criação Obrigatória de Issues no Azure DevOps

> **Organização**: `https://dev.azure.com/LesteDevOps`
> **Tipo**: `Issue` — nunca `Task`.
> **Assign**: `celeste@leste.com` — fixo, sem exceção.
> **Tags obrigatórias**:
> - Uma tag por **repositório afetado** (nome exato). Se a Issue tocar N repositórios, adicionar N tags.
> - Uma tag de **contexto** que descreva a atividade com uma palavra-chave (ex: `CriacaoEndpoint`, `CorrecaoBug`, `RefatoracaoAuth`, `MigracaoSchema`, `AjusteLayout`). Deve ajudar a identificar o que foi feito sem abrir a Issue.

### 2.1 Para o projeto Backend
```bash
# Capturar o ID retornado após criação
ISSUE_ID=$(az boards work-item create \
  --title "[BACKEND] <Título descritivo da issue>" \
  --type "Issue" \
  --project "<NOME_REPOSITORIO_BACKEND>" \
  --org "https://dev.azure.com/LesteDevOps" \
  --fields \
    "System.AreaPath=<NOME_REPOSITORIO_BACKEND>" \
    "System.AssignedTo=celeste@leste.com" \
    "System.Tags=Backend;<NOME_REPOSITORIO_BACKEND>;<PALAVRA_CONTEXTO>" \
    "Microsoft.VSTS.Common.Activity=Development" \
    "System.Description=<Descrição detalhada do escopo, critérios de aceite e dependências>" \
  --query "id" -o tsv)

# Mover imediatamente para To Do
az boards work-item update \
  --id $ISSUE_ID \
  --org "https://dev.azure.com/LesteDevOps" \
  --state "To Do"
```

### 2.2 Para o projeto Frontend
```bash
ISSUE_ID=$(az boards work-item create \
  --title "[FRONTEND] <Título descritivo da issue>" \
  --type "Issue" \
  --project "<NOME_REPOSITORIO_FRONTEND>" \
  --org "https://dev.azure.com/LesteDevOps" \
  --fields \
    "System.AreaPath=<NOME_REPOSITORIO_FRONTEND>" \
    "System.AssignedTo=celeste@leste.com" \
    "System.Tags=Frontend;<NOME_REPOSITORIO_FRONTEND>;<PALAVRA_CONTEXTO>" \
    "Microsoft.VSTS.Common.Activity=Development" \
    "System.Description=<Descrição detalhada do escopo, critérios de aceite e dependências>" \
  --query "id" -o tsv)

az boards work-item update \
  --id $ISSUE_ID \
  --org "https://dev.azure.com/LesteDevOps" \
  --state "To Do"
```

### 2.3 Issue tocando múltiplos repositórios (ex: pacote compartilhado + backend)
Quando uma mesma Issue envolver mais de um repositório, acumular **todas** as tags de repositório:
```bash
"System.Tags=Backend;<REPO_1>;<REPO_2>;<REPO_3>;<PALAVRA_CONTEXTO>"
```

### 2.4 Vincular Issues (quando ambos os projetos são impactados)
```bash
az devops work-item relation add \
  --id <ID_ISSUE_BACKEND> \
  --target-id <ID_ISSUE_FRONTEND> \
  --relation-type "Related" \
  --org "https://dev.azure.com/LesteDevOps"
```

---

## 🌿 Passo 3 — Criação de Branch por Sessão

Criar a branch de sessão imediatamente após as Issues — **sem aprovação**. Uma única branch é criada por sessão e reutilizada para todas as Issues do mesmo ciclo de trabalho.

```bash
# Definir a branch de sessão (executar UMA VEZ por sessão, por repositório)
SESSION_DATE=$(date -u +"%Y%m%d")
SESSION_USER=$(az account show --query "user.name" -o tsv 2>/dev/null | sed 's/@.*//')
if [ -z "$SESSION_USER" ]; then
  SESSION_USER=$(git config --global user.email 2>/dev/null | sed 's/@.*//')
fi
SESSION_CONTEXT="<contexto-kebab-case>" # Ver regra abaixo

SESSION_BRANCH="session/${SESSION_DATE}-${SESSION_USER}-${SESSION_CONTEXT}"

# Backend — criar se não existir, senão apenas mudar para ela
git -C "<CAMINHO_BACKEND>" checkout -b $SESSION_BRANCH 2>/dev/null || git -C "<CAMINHO_BACKEND>" checkout $SESSION_BRANCH

# Frontend — criar se não existir, senão apenas mudar para ela
git -C "<CAMINHO_FRONTEND>" checkout -b $SESSION_BRANCH 2>/dev/null || git -C "<CAMINHO_FRONTEND>" checkout $SESSION_BRANCH
```

> **Formato do nome**: `session/<YYYYMMDD>-<LOGIN>-<contexto-kebab-case>`
>
> | Parte        | Valor                                                                                       |
> | ------------ | ------------------------------------------------------------------------------------------- |
> | `<YYYYMMDD>` | Data atual no fuso UTC                                                                      |
> | `<LOGIN>`    | Parte local do email do usuário logado (antes do `@`), detectada via `az account show` ou `git config user.email` |
> | `<contexto>` | **Se o usuário especificou** um nome/contexto → usar exatamente o que foi informado (kebab-case). **Se não especificou** → a IA infere a partir das Issues da sessão (ex: `correcao-relatorios`, `integracao-pagamentos`) |
>
> **Regra**: Armazenar `$SESSION_BRANCH` no início da sessão e reutilizar em todas as Issues seguintes.

---

## 📣 Passo 4 — Resumo ao Usuário (sem pausa)

A IA exibe o resumo e **inicia a implementação imediatamente**:

```
✅ Issues criadas — Implementação iniciada

| Projeto   | Issue ID | Tags                                        | Assign              | Estado |
|-----------|----------|---------------------------------------------|---------------------|--------|
| Backend   | #XXXX    | Backend;<repo>;<contexto>                   | celeste@leste.com   | To Do  |
| Frontend  | #YYYY    | Frontend;<repo>;<contexto>                  | celeste@leste.com   | To Do  |

🔗 Issues vinculadas: #XXXX ↔ #YYYY
🌿 Branches: feature/#XXXX-<desc> | feature/#YYYY-<desc>
▶️  Movendo para Doing e iniciando implementação...
```

---

## 🔄 Passo 5 — Gestão do Ciclo de Estado da Issue

O fluxo de estados é **sequencial e obrigatório**. A IA executa cada transição no momento certo:

```
Backlog ──► To Do ──► Doing ──► Review
                                  └── Done (apenas humanos)
```

| Transição             | Quando ocorre                          | Comando                                                                                 |
| --------------------- | -------------------------------------- | --------------------------------------------------------------------------------------- |
| `Backlog → To Do`     | Logo após criação da Issue             | `az boards work-item update --id <ID> --state "To Do" --org "https://dev.azure.com/LesteDevOps"` |
| `To Do → Doing`       | Ao iniciar a implementação             | `az boards work-item update --id <ID> --state "Doing" --org "https://dev.azure.com/LesteDevOps"` + registrar **Start Date** |
| `Doing → Review`      | Ao concluir toda a implementação       | `az boards work-item update --id <ID> --state "Review" --org "https://dev.azure.com/LesteDevOps"` + registrar **Finish Date** + **Completed Work** + comentário de encerramento |

### Registrar Start Date (ao mover para Doing)
```bash
az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --fields "Microsoft.VSTS.Scheduling.StartDate=<YYYY-MM-DD>"
```

### Registrar Finish Date e Completed Work (ao mover para Review)
```bash
az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --fields \
    "Microsoft.VSTS.Scheduling.FinishDate=<YYYY-MM-DD>" \
    "Microsoft.VSTS.Scheduling.CompletedWork=<TOTAL_TOKENS_UTILIZADOS>"
```

> **Completed Work** = total de tokens consumidos durante toda a execução da Issue (entrada + saída).

---

## 💬 Passo 6 — Discussion: Comentários Incrementais Obrigatórios

> **MANDATO**: A cada nova etapa executada, a IA **DEVE** adicionar um novo comentário na Discussion da Issue. Nunca editar comentários anteriores — cada etapa é um registro imutável de evidência.

### Comando padrão de comentário
```bash
az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --discussion "## ⚙️ [<YYYY-MM-DD HH:MM>] <Título da Etapa>

**Ação:** <O que foi feito de forma objetiva>

**Artefatos:**
- <arquivo/caminho criado ou modificado>
- <comando executado e seu output resumido>

**Resultado:** <✅ Sucesso | ⚠️ Pendência | ❌ Falha>
**Observações:** <Decisões técnicas, justificativas, logs relevantes>"
```

### Momentos obrigatórios de comentário

| # | Momento                            | O que registrar                                                    |
|---|------------------------------------|--------------------------------------------------------------------|
| 1 | Issue criada (Backlog → To Do)     | Escopo identificado, projetos impactados, critérios de aceite      |
| 2 | Implementação iniciada (→ Doing)   | Estratégia adotada, camadas/arquivos que serão tocados             |
| 3 | Cada arquivo criado ou modificado  | Caminho, propósito e resumo da mudança                             |
| 4 | Testes executados                  | Resultado completo (passou/falhou, cobertura)                      |
| 5 | Commit/Push realizado              | Hash, mensagem do commit, branch e repositório de destino          |
| 6 | Issue finalizada (→ Review)        | **Comentário de encerramento** — ver template abaixo               |

### Template do Comentário de Encerramento (obrigatório ao mover para Review)
```bash
az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --discussion "## ✅ Entrega Concluída — Resumo Final

**O que foi entregue:**
- <Item 1 entregue>
- <Item 2 entregue>

**Repositórios alterados:**
- <NOME_REPO>: branch `$SESSION_BRANCH` — commit `<HASH>`

**Testes:** <Resultado final dos testes>

**Tokens utilizados:** <TOTAL> (registrado em Completed Work)
**Start Date:** <YYYY-MM-DD> | **Finish Date:** <YYYY-MM-DD>

**Pendências / Observações para o Revisor:** <ou 'Nenhuma'>"
```

---

## 🛑 Condição de Bloqueio

Se as URLs do Azure DevOps (`ESTE_PROJETO.Url` ou `PROJETO_IRMAO.Url`) estiverem como `[MISSING]`:
- **NÃO** criar Issues nem branches.
- **PARAR** e solicitar ao usuário que execute `setup-leste-ai.ps1` com os repositórios configurados.


Para cada solicitação, a IA deve categorizar o impacto:

| Tipo de Impacto       | Critério                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------ |
| **Backend apenas**    | Mudanças em API, banco de dados, regras de negócio, serviços, domínio ou infraestrutura.   |
| **Frontend apenas**   | Mudanças em UI, componentes Vue, stores Pinia, serviços TypeScript ou rotas do cliente.    |
| **Ambos (Full-Stack)**| Qualquer mudança que exija contrato de dados novo ou ajuste em endpoints já existentes.    |

### Matriz de Decisão

```
Solicitação recebida
       │
       ├─ Envolve API / DB / Backend Logic?  ──► Criar Issue no BACKEND
       │
       ├─ Envolve UI / Componentes / Frontend Logic?  ──► Criar Issue no FRONTEND
       │
       └─ Envolve contrato de dados (DTO / Interface)?  ──► Criar Issues em AMBOS e vincular
```

---

## 📝 Passo 2 — Criação Obrigatória de Issues no Azure DevOps

> **Organização Azure DevOps**: `https://dev.azure.com/LesteDevOps`
> O nome do projeto (`--project`) é o **nome exato da pasta/repositório** do projeto no Azure DevOps.
> **Tipo obrigatório**: `Issue` (não Task).
> **Responsável fixo**: `celeste@leste.com` — toda Issue deve ser assigned a este usuário.
> **Tag de repositório**: Toda Issue deve conter uma tag com o **nome exato do repositório** de destino.

### 2.1 Para o projeto impactado (Backend)
```bash
az boards work-item create \
  --title "[BACKEND] <Título descritivo da issue>" \
  --type "Issue" \
  --project "<NOME_REPOSITORIO_BACKEND>" \
  --org "https://dev.azure.com/LesteDevOps" \
  --fields \
    "System.AreaPath=<NOME_REPOSITORIO_BACKEND>" \
    "System.AssignedTo=celeste@leste.com" \
    "System.Tags=Backend;<NOME_REPOSITORIO_BACKEND>;<TAG_FEATURE>" \
    "Microsoft.VSTS.Common.Activity=Development" \
    "System.Description=<Descrição detalhada do que precisa ser feito no Backend>"
```

### 2.2 Para o projeto impactado (Frontend)
```bash
az boards work-item create \
  --title "[FRONTEND] <Título descritivo da issue>" \
  --type "Issue" \
  --project "<NOME_REPOSITORIO_FRONTEND>" \
  --org "https://dev.azure.com/LesteDevOps" \
  --fields \
    "System.AreaPath=<NOME_REPOSITORIO_FRONTEND>" \
    "System.AssignedTo=celeste@leste.com" \
    "System.Tags=Frontend;<NOME_REPOSITORIO_FRONTEND>;<TAG_FEATURE>" \
    "Microsoft.VSTS.Common.Activity=Development" \
    "System.Description=<Descrição detalhada do que precisa ser feito no Frontend>"
```

### 2.3 Vincular Issues (quando ambos os projetos são impactados)
```bash
az devops work-item relation add \
  --id <ID_ISSUE_BACKEND> \
  --target-id <ID_ISSUE_FRONTEND> \
  --relation-type "Related" \
  --org "https://dev.azure.com/LesteDevOps"
```

---

## 🌿 Passo 3 — Criação de Branches por Projeto

Após criar as Issues, criar a branch em cada projeto impactado:

```bash
# No projeto Backend
git checkout -b feature/<ID_ISSUE_BACKEND>-<descricao-kebab-case>

# No projeto Frontend
git checkout -b feature/<ID_ISSUE_FRONTEND>-<descricao-kebab-case>
```

---

## 📣 Passo 4 — Confirmação ao Usuário

A IA **DEVE** apresentar um resumo e **já iniciar a implementação** sem aguardar confirmação:

```
✅ Análise de Impacto Concluída — Iniciando automaticamente

| Projeto   | Issue ID | Título                           | Assign              | Estado   |
|-----------|----------|----------------------------------|---------------------|----------|
| Backend   | #XXXX    | [BACKEND] <título>              | celeste@leste.com   | Doing    |
| Frontend  | #YYYY    | [FRONTEND] <título>             | celeste@leste.com   | Doing    |

🏷️  Tags: Backend;<NOME_REPOSITORIO_BACKEND> | Frontend;<NOME_REPOSITORIO_FRONTEND>
🔗  Issues vinculadas: #XXXX ↔ #YYYY
🌿  Branches criadas: feature/#XXXX-<desc> | feature/#YYYY-<desc>

▶️  Implementação iniciada...
```

---

## 💬 Passo 5 — Discussion: Registro Incremental de Evidências

> **MANDATO**: Toda ação executada associada a uma Issue **DEVE** ser registrada na seção `Discussion` da Issue correspondente no Azure DevOps, de forma **incremental** e com objetivo de **gerar evidências auditáveis**.

### Regras de Registro na Discussion

1. **Incremental**: Cada etapa concluída gera um novo comentário. Não editar comentários anteriores.
2. **Conteúdo Obrigatório por Comentário**:
   - 📌 **Momento**: O que foi feito (ex: "Branch criada", "Endpoint implementado", "Testes executados").
   - 🧱 **Artefatos**: Arquivos criados/modificados, comandos executados, outputs relevantes.
   - ✅ **Resultado**: Sucesso, falha ou pendência com justificativa.
   - ⏱️ **Timestamp**: Data/hora da ação.

### Comando para adicionar comentário na Discussion
```bash
az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --discussion "## [<TIMESTAMP>] <Título da Etapa>

**Ação realizada:** <Descrição objetiva do que foi feito>

**Artefatos:**
- <arquivo ou comando 1>
- <arquivo ou comando 2>

**Resultado:** <Sucesso / Falha / Pendência>

**Observações:** <Justificativas técnicas, decisões tomadas, logs relevantes>"
```

### Momentos Obrigatórios de Registro na Discussion

| Momento                              | Comentário esperado                                               |
| ------------------------------------ | ----------------------------------------------------------------- |
| Issue criada                         | Descrição do impacto identificado e escopo da mudança            |
| Branch criada                        | Nome da branch e projeto de destino                              |
| Início da implementação              | Arquivos/camadas envolvidos e estratégia adotada                 |
| Cada arquivo criado ou modificado    | Caminho, motivo e resumo da mudança                              |
| Testes executados                    | Resultado dos testes (output completo ou resumo)                 |
| Commit/Push realizado                | Hash do commit, mensagem e branch de destino                     |
| Issue movida para Review             | Checklist de finalização preenchido                              |

---

## 🛑 Condição de Bloqueio

Se as URLs do Azure DevOps (`ESTE_PROJETO.Url` ou `PROJETO_IRMAO.Url`) estiverem como `[MISSING]`:
- **NÃO** criar issues nem branches.
- **PARAR** e solicitar ao usuário que execute `setup-leste-ai.ps1` com os repositórios configurados corretamente.

---

## ♻️ Atualização de Estado Durante a Execução

| Momento                        | Ação no Azure DevOps                                                     |
| ------------------------------ | ------------------------------------------------------------------------ |
| Início da implementação        | Mover Issue para **Doing** + registrar comentário na Discussion          |
| Cada entrega parcial           | Registrar comentário incremental na Discussion com evidências            |
| Implementação concluída        | Mover Issue para **Review** + preencher `Completed Work` e `Finish Date` + comentário final na Discussion |
| A IA **NUNCA** move para Done  | Este estado é exclusivo de validação humana                              |
