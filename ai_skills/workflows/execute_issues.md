# ▶️ Execute Issues Workflow (Execução de Issues do Backlog)

Define como a IA deve responder quando o usuário solicita a execução de Issues existentes no Azure DevOps.

---

## 🗣️ Comandos Reconhecidos

O usuário pode solicitar a execução de Issues das seguintes formas:

| Solicitação do usuário                              | Comportamento da IA                                          |
| --------------------------------------------------- | ------------------------------------------------------------ |
| `"Execute a Issue #XXXX"`                           | Executar a Issue específica informada                        |
| `"Execute as Issues #XXXX e #YYYY"`                 | Executar as Issues listadas, uma por vez, em sequência       |
| `"Execute todas as Issues em To Do"`                | Buscar todas as Issues em estado `To Do` atribuídas a `celeste@leste.com` e executar em sequência |
| `"Execute as Issues em To Do do projeto <nome>"`   | Filtrar pelo projeto e executar apenas as Issues daquele repositório |

---

## 🔍 Passo 1 — Buscar Issues para Execução

### Executar Issue específica
```bash
az boards work-item show \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --query "{id:id, title:fields.\"System.Title\", state:fields.\"System.State\", assignedTo:fields.\"System.AssignedTo\".uniqueName}" \
  -o json
```

### Buscar todas as Issues em "To Do" atribuídas à Celeste
```bash
az boards query \
  --wiql "SELECT [System.Id], [System.Title], [System.State], [System.AssignedTo], [System.TeamProject]
          FROM WorkItems
          WHERE [System.WorkItemType] = 'Issue'
            AND [System.State] = 'To Do'
            AND [System.AssignedTo] = 'celeste@leste.com'
          ORDER BY [System.CreatedDate] ASC" \
  --org "https://dev.azure.com/LesteDevOps" \
  -o table
```

> **Filtro obrigatório**: Executar apenas Issues que estejam:
> - Estado: `To Do`
> - Tipo: `Issue`
> - Assign: `celeste@leste.com`

---

## 📋 Passo 2 — Detectar Revisor e Confirmar Issues para Execução

### 2.1 — Detectar automaticamente o email do usuário logado
```bash
# 1ª opção — Azure CLI (conta logada no az login) — preferida
NOME_REVISOR=$(az account show --query "user.name" -o tsv 2>/dev/null)

# 2ª opção — Git config global
if [ -z "$NOME_REVISOR" ]; then
  NOME_REVISOR=$(git config --global user.email 2>/dev/null)
fi

# 3ª opção — Variável de ambiente do SO
if [ -z "$NOME_REVISOR" ]; then
  NOME_REVISOR="${USERNAME}@leste.com"
fi

echo "👤 Revisor detectado automaticamente: $NOME_REVISOR"
```

> **Nunca perguntar ao usuário** — a detecção é sempre automática.

### 2.2 — Apresentar as Issues encontradas e iniciar sem pausa
```
📋 Issues encontradas para execução:

| # | Issue ID | Projeto          | Título                        | Estado |
|---|----------|------------------|-------------------------------|--------|
| 1 | #XXXX    | <PROJETO>        | <Título da Issue>             | To Do  |
| 2 | #YYYY    | <PROJETO>        | <Título da Issue>             | To Do  |

👤 Reviewed By (detectado): <NOME_REVISOR>
▶️  Iniciando execução...
```

---

## ▶️ Passo 3 — Executar Cada Issue em Sequência

Para cada Issue da lista, seguir integralmente o fluxo:

### 3.1 — Ler o conteúdo completo da Issue
```bash
az boards work-item show \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --expand all \
  -o json
```
Extrair: título, descrição, critérios de aceite, tags, projeto, comentários existentes.

### 3.2 — Usar a branch de sessão

A branch é criada **uma única vez no início da sessão** e reutilizada por todas as Issues executadas no mesmo ciclo de trabalho. Se ainda não existir, criá-la agora.

```bash
# Usar a branch de sessão já criada (ou criar se for a primeira Issue da sessão)
git -C "<CAMINHO_PROJETO>" checkout $SESSION_BRANCH 2>/dev/null || \
  git -C "<CAMINHO_PROJETO>" checkout -b $SESSION_BRANCH
```

> `$SESSION_BRANCH` deve ser definido **uma única vez por sessão** com o formato `session/<YYYYMMDD>-<SESSION_ID>-<contexto-kebab-case>`, onde o contexto é informado pelo usuário ou inferido pela IA a partir das Issues da sessão.

### 3.3 — Mover para Doing + registrar Start DateTime exato
```bash
START_DATETIME=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --state "Doing" \
  --fields "Microsoft.VSTS.Scheduling.StartDate=$START_DATETIME"
```

### 3.4 — Implementar o que está descrito na Issue
- Seguir o escopo e critérios de aceite da descrição da Issue.
- Aplicar os padrões de `ai_skills/tech_rules/` conforme o tipo de projeto.
- **Postar na Discussion o espelho integral de tudo que for escrito no console** (ver `backlog_management.md` Passo 6).

### 3.5 — Mover para Review + registrar Finish DateTime + campos de revisão
```bash
FINISH_DATETIME=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --state "Review" \
  --fields \
    "Microsoft.VSTS.Scheduling.FinishDate=$FINISH_DATETIME" \
    "Microsoft.VSTS.Scheduling.CompletedWork=<TOTAL_TOKENS>" \
    "System.AssignedTo=<NOME_REVISOR>" \
    "Microsoft.VSTS.Common.ReviewedBy=<NOME_REVISOR>" \
    "Microsoft.VSTS.Common.RequiresReview=Yes"
```

### 3.6 — Comentário de Encerramento (espelho integral do resumo do console)
```bash
az boards work-item update \
  --id <ID_ISSUE> \
  --org "https://dev.azure.com/LesteDevOps" \
  --discussion "## ✅ [$FINISH_DATETIME] Entrega Concluída — Resumo Final

### O que foi implementado
<Lista detalhada e completa de tudo criado/modificado/removido>

### Arquivos alterados
<Repositório, branch, commit hash e descrição de cada arquivo>

### Testes
<Output completo dos testes>

### Decisões técnicas
<Justificativas e trade-offs>

### Métricas
- Start: $START_DATETIME
- Finish: $FINISH_DATETIME
- Tokens: <TOTAL>
- Reviewed By: <NOME_REVISOR>"
```

---

## 🔁 Passo 4 — Progresso entre Issues

Ao concluir uma Issue e iniciar a próxima, exibir progresso:

```
✅ Issue #XXXX concluída → Review
   Assign: <NOME_REVISOR> | Tokens: <N> | Duração: <START> → <FINISH>

▶️ Iniciando Issue #YYYY — <Título>...
```

Ao finalizar todas:

```
🎉 Todas as Issues foram executadas e movidas para Review.

| Issue ID | Projeto    | Status  | Tokens | Reviewed By     |
|----------|------------|---------|--------|-----------------|
| #XXXX    | <projeto>  | Review  | <N>    | <NOME_REVISOR>  |
| #YYYY    | <projeto>  | Review  | <N>    | <NOME_REVISOR>  |
```

---

## 🛑 Condições de Bloqueio por Issue

| Condição                                        | Ação                                                              |
| ----------------------------------------------- | ----------------------------------------------------------------- |
| Issue não está em `To Do`                       | Ignorar e informar ao usuário ("Issue #X está em estado Y, pulando") |
| Issue não está assign para `celeste@leste.com`  | Ignorar e informar ao usuário                                     |
| Descrição da Issue está vazia ou ambígua        | Pausar, exibir o conteúdo da Issue e pedir esclarecimento         |
| Issue requer mudança destrutiva no banco        | Pausar e solicitar aprovação humana (ver `governance/approvals.md`) |
