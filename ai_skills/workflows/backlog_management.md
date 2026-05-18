# 📋 Backlog Management Workflow (Gerenciamento de Backlog Obrigatório)

> ## ⛔ REGRA ABSOLUTA — VÁLIDA PARA TODOS OS MODELOS (GPT, Claude, Gemini, etc.)
>
> **Criar Issues no Azure DevOps é OBRIGATÓRIO e AUTOMÁTICO para TODA solicitação.**
> **Mover Issues no kanban é OBRIGATÓRIO e AUTOMÁTICO a cada transição de estado.**
> **Criar/reutilizar Test Plan e Test Cases é OBRIGATÓRIO para cada Iteration/Issue.**
> **PROIBIDO** pedir confirmação para criar Issues: "posso criar?", "deseja que eu crie?", ou qualquer variação.
> **PROIBIDO** pedir confirmação para mover Issues: "posso mover para Doing?", "confirma a movimentação?", ou qualquer variação.
> **PROIBIDO** iniciar qualquer implementação sem ter criado as Issues primeiro.
> **PROIBIDO** justificar a não-criação com frases como "vou direto ao código", "para agilizar", "pulando o backlog".
> A criação de Issues, Test Plans/Test Cases, movimentação no kanban e criação de branches acontece **sempre, imediatamente e sem aprovação prévia**.
> Quando o usuário reportar erro/bug/falha/regressão em atividade recém-entregue, criar automaticamente um work item do tipo **Bug** e vinculá-lo à Issue original quando identificável. Se a Issue original não for identificável, criar o Bug mesmo assim, solto. Seguir `ai_skills/workflows/bug_reporting.md`.
> Comentários na Discussion são obrigatórios, incrementais e completos. Para conteúdo multilinha, usar `System.History` via JSON Patch conforme o Passo 6; não usar comando que corte o comentário na primeira linha.

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

## 🗓️ Passo 0.5 — Selecionar/Criar Iteration Vigente

> **Executar sempre que uma nova sessão ou branch for iniciada**, após consultar Issues abertas em `To Do` e antes de criar novas Issues.
> A Iteration de cada Issue/Bug deve ser a Iteration válida para a data atual: `startDate <= hoje <= finishDate`.
> Primeiro consultar e reutilizar uma Iteration existente que contenha o dia atual. Criar a Iteration trimestral `YYYY.QN` apenas se nenhuma Iteration vigente existir no projeto.

### Regra de nomeação

| Período | Nome da Iteration | Data inicial | Data final |
| ------- | ----------------- | ------------ | ---------- |
| Janeiro a Março | `YYYY.Q1` | `YYYY-01-01` | `YYYY-03-31` |
| Abril a Junho | `YYYY.Q2` | `YYYY-04-01` | `YYYY-06-30` |
| Julho a Setembro | `YYYY.Q3` | `YYYY-07-01` | `YYYY-09-30` |
| Outubro a Dezembro | `YYYY.Q4` | `YYYY-10-01` | `YYYY-12-31` |

Exemplo de fallback: se nenhuma Iteration existente cobrir maio/2026, criar `2026.Q2`.

### Selecionar Iteration vigente existente

```powershell
$PROJECT_NAME = "<NOME_REPOSITORIO_OU_PROJETO>"
$today = (Get-Date).Date

$iterations = az boards iteration project list `
  --project $PROJECT_NAME `
  --org "https://dev.azure.com/LesteDevOps" `
  --depth 10 `
  -o json | ConvertFrom-Json

$currentIteration = $iterations | Where-Object {
  $_.attributes.startDate -and $_.attributes.finishDate -and
  ([datetime]$_.attributes.startDate).Date -le $today -and
  ([datetime]$_.attributes.finishDate).Date -ge $today
} | Select-Object -First 1

if ($currentIteration) {
  $ITERATION_NAME = $currentIteration.name
  $ITERATION_PATH = $currentIteration.path
  $ITERATION_ID = $currentIteration.identifier
}
```

### Calcular fallback trimestral (somente se nenhuma vigente existir)

Executar este cálculo apenas quando `$currentIteration` não existir. Se uma Iteration vigente foi encontrada, manter `ITERATION_NAME`, `ITERATION_PATH` e `ITERATION_ID` já selecionados.

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

### Criar a Iteration fallback no projeto, se nenhuma vigente existir

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

> Se uma Iteration vigente já existir, reutilizar. Se nenhuma existir, criar o fallback trimestral. Se o vínculo com o time já existir, continuar sem erro. Não perguntar ao usuário.

### Mover Issues/Bugs em Backlog e To Do para a Iteration vigente

Mover automaticamente todas as Issues e Bugs do projeto que estejam em `Backlog` ou `To Do` para a Iteration atual:

```bash
ISSUES_TO_MOVE=$(az boards query \
  --wiql "SELECT [System.Id]
          FROM WorkItems
          WHERE [System.WorkItemType] IN ('Issue', 'Bug')
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

> **Sem confirmação**: a criação da Iteration e a movimentação de Issues/Bugs em `Backlog`/`To Do` são automáticas.
> **Escopo**: não mover Issues em `Doing`, `Review` ou `Done`.

---

## 🧪 Passo 0.6 — Criar/Reutilizar Test Plan da Iteration

> **Executar sempre que uma nova sessão ou branch for iniciada**, após selecionar/criar a Iteration vigente.

Para cada projeto impactado, criar ou reutilizar um Test Plan no formato:

```text
<PROJECT_NAME> - <ITERATION_NAME>
```

Exemplo:

```text
Sentinel.Frontend - 2026.Q2
```

Regras:

| Item | Regra |
| ---- | ----- |
| Test Plan | 1 por projeto e por Iteration vigente |
| Suites | `Backend`, `Frontend`, `Integracao`, `Regressao`, `Smoke` conforme o plano de ação |
| Test Cases | Criados a partir dos critérios de aceite de cada Issue |
| Rastreabilidade | Cada Test Case deve ser vinculado à Issue correspondente |
| Review | A Issue só pode ir para `Review` com resumo de Test Plan/Test Cases no comentário final |

Seguir integralmente `ai_skills/workflows/test_plans.md`.

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

▶️ Criação das Issues iniciada automaticamente. Se o usuário solicitar ajustes durante a execução, registrar na Discussion e atualizar/criar Issues conforme necessário.
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
> **Exceção obrigatória**: bug reportado pelo usuário deve ser `Bug`, nunca `Issue`. Seguir `ai_skills/workflows/bug_reporting.md`.
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

### 2.6 Criar Test Cases vinculados às Issues

Após criar as Issues e registrar dependências, criar Test Cases no Test Plan da Iteration vigente:

```text
Issue #<ID> → Test Case(s) #<ID_TEST_CASE>
```

Regras:
- Gerar pelo menos 1 Test Case por critério de aceite relevante.
- Vincular cada Test Case à Issue com relação `Related`.
- Adicionar os Test Cases à suite apropriada (`Backend`, `Frontend`, `Integracao`, `Regressao`, `Smoke`).
- Registrar no comentário da Issue os IDs dos Test Cases criados.

Seguir `ai_skills/workflows/test_plans.md` Passos 3 e 4.

### 2.7 Criar Bug quando o usuário reportar falha

Se o usuário reportar erro, bug, falha ou regressão sobre uma atividade recém-entregue:

1. Criar imediatamente um work item do tipo `Bug`.
2. Vincular o Bug à Issue original com `Related`, se a Issue original for identificável.
3. Se a Issue original não for identificável, criar o Bug mesmo assim e registrar a origem como "não identificada".
4. Adicionar comentário completo no Bug e na Issue original quando houver vínculo.
5. Executar o Bug como qualquer item de desenvolvimento: `To Do` -> `Doing` -> `Review`.

Seguir `ai_skills/workflows/bug_reporting.md`.

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
ESTIMATED_DATETIME="<YYYY-MM-DDTHH:MM:SSZ>"       # estimativa atualizada da atividade

az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --state "Doing" \
  --fields \
    "Microsoft.VSTS.Scheduling.StartDate=$START_DATETIME" \
    "Microsoft.VSTS.Scheduling.TargetDate=$ESTIMATED_DATETIME"
```

> Se o processo Azure DevOps usar campo customizado para "Estimated Date", preencher esse campo além de `Microsoft.VSTS.Scheduling.TargetDate`. Se o campo não existir, registrar a estimativa na Discussion sem bloquear.

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
> `<NOME_REVISOR>` é sempre o usuário que solicitou a Issue/Bug. Detectar automaticamente por `az account show --query user.name -o tsv`; se a Issue trouxer outro solicitante explícito, usar esse solicitante.

---

## 💬 Passo 6 — Discussion: Espelho Integral do Console

> **MANDATO ABSOLUTO**: Todo texto que a IA escreve no console **DEVE** ser postado integralmente na Discussion da Issue correspondente.
> Para o GitHub Copilot CLI, isso significa **todo texto gerado pelo Copilot na CLI**: preambulos, analises, avisos, decisoes, perguntas, respostas, resultados finais e mensagens de erro.
> Isso inclui: análises, decisões técnicas, código gerado, outputs de comandos, logs, erros encontrados e como foram resolvidos, e o resumo final.
> **Proibido resumir, filtrar ou omitir** qualquer parte do conteúdo do console.
> A mem├│ria local (`./memory/`) deve registrar as IDs das Issues e os principais coment├írios/textos escritos pelo GitHub Copilot CLI; a Issue deve conter o texto integral.

### Regras de Postagem

1. **Espelho integral**: O comentário é uma cópia exata do que foi escrito no console naquela etapa.
2. **Incremental**: Cada etapa gera um novo comentário — nunca editar anteriores.
3. **Timestamp exato**: Usar o datetime real do momento do post (`YYYY-MM-DDTHH:MM:SS`).
4. **Sem perda entre CLI e Issue**: Antes de concluir, comparar os textos escritos pelo GitHub Copilot CLI durante a atividade com os comentários da Discussion e postar qualquer trecho ausente.
5. **Mem├│ria vinculada**: Registrar no diário `./memory/{yyyy-MM-dd}.md` as IDs das Issues e os principais comentários da IA na CLI, indicando que o espelho integral foi publicado na Issue.

### Comando padrão de comentário multilinha

Usar `System.History` via JSON Patch para preservar o comentário inteiro. **Não usar** `az boards work-item update --discussion` para conteúdo multilinha, pois ele pode cortar o texto na primeira linha dependendo do shell.

```powershell
function Add-WorkItemDiscussion {
  param(
    [Parameter(Mandatory=$true)][int]$WorkItemId,
    [Parameter(Mandatory=$true)][string]$Title,
    [Parameter(Mandatory=$true)][string]$Body
  )

  $timestamp = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")
  $history = "## [$timestamp] $Title`n`n$Body"
  $payload = @(
    @{
      op = "add"
      path = "/fields/System.History"
      value = $history
    }
  ) | ConvertTo-Json -Depth 5

  $tmp = New-TemporaryFile
  Set-Content -Path $tmp -Value $payload -Encoding UTF8

  az devops invoke `
    --organization "https://dev.azure.com/LesteDevOps" `
    --area wit `
    --resource workitems `
    --route-parameters id=$WorkItemId `
    --api-version 7.1 `
    --http-method PATCH `
    --headers "Content-Type=application/json-patch+json" `
    --in-file $tmp

  Remove-Item $tmp -Force
}

Add-WorkItemDiscussion `
  -WorkItemId <ID_ISSUE> `
  -Title "<Título da Etapa>" `
  -Body @"
<CONTEÚDO INTEGRAL exatamente como foi escrito no console — análise, código, outputs, decisões, logs>
"@
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
| 9 | Bug reportado pelo usuário        | Criar Bug, comentário completo, relação com Issue original quando houver |

> Se a atividade impactar múltiplas Issues, postar o texto integral em cada Issue relevante ou, quando o conteúdo for específico de uma Issue, postar no respectivo item e registrar a distribuição na memória.

### Template do Comentário de Encerramento (obrigatório ao mover para Review)
```powershell
Add-WorkItemDiscussion `
  -WorkItemId <ID_ISSUE> `
  -Title "Entrega Concluída — Resumo Final" `
  -Body @"
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
<Listar itens ou escrever 'Nenhuma'>
"@
```

---

## 🛑 Condição de Bloqueio

Se `ESTE_PROJETO.Url` ou `PROJETO_IRMAO.Url` estiverem como `[MISSING]`:
- **NÃO** criar Issues nem branches.
- **PARAR** e solicitar que o usuário execute `setup-leste-ai.ps1` com os repositórios configurados.
