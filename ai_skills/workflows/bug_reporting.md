# Bug Reporting Workflow (Azure DevOps)

Define o comportamento obrigatorio quando o usuario reporta erro, bug, falha ou regressao em uma atividade entregue pela IA.

## Regra absoluta

Quando o usuario reportar um bug, a IA deve criar automaticamente um work item do tipo **Bug** no Azure DevOps, sem pedir permissao.

- Se a Issue original da atividade for identificavel, vincular o Bug a ela com relacao `Related`.
- Se a Issue original nao for identificavel, criar o Bug mesmo assim, solto, registrando na descricao que a origem nao foi identificada.
- O Bug deve receber a Iteration vigente na data atual, isto e, uma Iteration cujo intervalo contem o dia atual.
- O Bug deve receber comentario inicial completo na Discussion com o relato do usuario, contexto, evidencias e vinculo com a Issue original quando houver.
- Se o bug for corrigido pela IA, o fluxo de execucao e Review do Bug segue as mesmas regras de Issue: `To Do` -> `Doing` -> `Review`, com `Start Date`, `Estimated Date`, `Finish Date`, `Completed Work`, commits `AB#<BUG_ID>` e `Reviewed By` preenchido com o solicitante.

## Deteccao da Issue original

Antes de criar o Bug, tentar identificar a Issue original usando, nesta ordem:

1. ID mencionado pelo usuario na conversa.
2. Issue ativa da sessao atual.
3. IDs em branch, commits recentes ou mensagens `AB#<ID>`.
4. Issues citadas na memoria local ou no log `ISSUE-<id>-discussion.md`.

Se nenhuma origem for encontrada, nao bloquear: criar o Bug sem relacao.

## Criacao do Bug

```powershell
$now = (Get-Date).ToUniversalTime()
$START_DATETIME = $now.ToString("yyyy-MM-ddTHH:mm:ssZ")
$ESTIMATED_DATETIME = $now.AddDays(1).ToString("yyyy-MM-ddTHH:mm:ssZ")

$BUG_ID = az boards work-item create `
  --title "[BUG] <titulo curto do bug reportado>" `
  --type "Bug" `
  --project "<PROJECT_NAME>" `
  --org "https://dev.azure.com/LesteDevOps" `
  --fields `
    "System.AreaPath=<PROJECT_NAME>" `
    "System.AssignedTo=celeste@leste.com" `
    "System.IterationPath=<ITERATION_PATH_ATUAL>" `
    "System.Tags=Bug;<NOME_REPOSITORIO>;<PALAVRA_CONTEXTO>;CorrecaoBug" `
    "Microsoft.VSTS.Common.Activity=Development" `
    "Microsoft.VSTS.Common.RequiresReview=Yes" `
    "Microsoft.VSTS.Scheduling.StartDate=$START_DATETIME" `
    "Microsoft.VSTS.Scheduling.TargetDate=$ESTIMATED_DATETIME" `
    "System.Description=<relato completo do usuario, contexto, passos para reproduzir, resultado esperado, resultado atual e Issue original se houver>" `
  --query "id" -o tsv

az boards work-item update `
  --id $BUG_ID `
  --org "https://dev.azure.com/LesteDevOps" `
  --state "To Do"
```

Se a Issue original existir:

```powershell
az devops work-item relation add `
  --id $BUG_ID `
  --target-id <ID_ISSUE_ORIGINAL> `
  --relation-type "Related" `
  --org "https://dev.azure.com/LesteDevOps"
```

Tambem comentar nos dois lados:

- No Bug: informar que ele foi criado a partir da validacao da Issue original.
- Na Issue original: informar que o usuario reportou um bug e apontar para `Bug #<BUG_ID>`.

## Comentarios obrigatorios

Usar o helper de Discussion em `ai_skills/workflows/backlog_management.md` para adicionar comentarios multilinha completos. Nao usar comandos que cortem o conteudo na primeira linha.

O comentario inicial do Bug deve conter:

```markdown
## Bug reportado pelo usuario

### Relato
<texto integral do usuario>

### Contexto
- Issue original: #<ID> ou "nao identificada"
- Repositorio: <repo>
- Branch: <branch>
- Commits relacionados: <hashes ou "nao identificados">

### Reproducao
<passos conhecidos ou "a detalhar">

### Resultado atual
<comportamento observado>

### Resultado esperado
<comportamento esperado>
```

