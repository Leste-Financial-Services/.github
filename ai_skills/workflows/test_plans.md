# 🧪 Test Plans Workflow (Azure DevOps)

Define como a IA deve usar Azure DevOps Test Plans para rastrear validação funcional/manual das Issues.

---

## ⛔ Regra Obrigatória

> Para toda nova solicitação que gere Issues/Bugs, a IA deve criar ou reutilizar um **Test Plan por Iteration vigente** e criar **Test Cases vinculados às Issues/Bugs** a partir dos critérios de aceite.
>
> Test Plans **não substitui testes automatizados**. Ele registra aceite funcional, QA manual/exploratório e evidência de validação.

---

## Estrutura Padrão

```text
Test Plan: <PROJECT_NAME> - <ITERATION_NAME>
  Suite: Backend
  Suite: Frontend
  Suite: Integracao
  Suite: Regressao
  Suite: Smoke
```

Exemplo:

```text
Test Plan: Sentinel.Frontend - 2026.Q2
  Suite: Frontend
    Test Case: #1234 - Deve exibir alerta de risco
  Suite: Regressao
    Test Case: #1235 - Fluxo de aprovação permanece funcional
```

---

## Passo 1 — Criar ou Reutilizar Test Plan da Iteration Vigente

Usar o mesmo `PROJECT_NAME`, `ITERATION_NAME`, `START_DATE` e `FINISH_DATE` calculados no `backlog_management.md` Passo 0.5.

```bash
TEST_PLAN_NAME="${PROJECT_NAME} - ${ITERATION_NAME}"
ITERATION_PATH="${PROJECT_NAME}\\${ITERATION_NAME}"

TEST_PLAN_ID=$(az devops invoke \
  --organization "https://dev.azure.com/LesteDevOps" \
  --area testplan \
  --resource plans \
  --route-parameters project="$PROJECT_NAME" \
  --api-version 7.1 \
  --query "value[?name=='${TEST_PLAN_NAME}'].id | [0]" -o tsv 2>/dev/null)

if [ -z "$TEST_PLAN_ID" ]; then
  cat > test-plan.json <<EOF
{
  "name": "${TEST_PLAN_NAME}",
  "areaPath": "${PROJECT_NAME}",
  "iteration": "${ITERATION_PATH}",
  "startDate": "${START_DATE}T00:00:00Z",
  "endDate": "${FINISH_DATE}T23:59:59Z",
  "state": "Active",
  "description": "Plano de testes funcional/manual da iteration ${ITERATION_NAME}."
}
EOF

  TEST_PLAN_ID=$(az devops invoke \
    --organization "https://dev.azure.com/LesteDevOps" \
    --area testplan \
    --resource plans \
    --route-parameters project="$PROJECT_NAME" \
    --api-version 7.1 \
    --http-method POST \
    --in-file test-plan.json \
    --query "id" -o tsv)
fi
```

---

## Passo 2 — Criar Suites Padrão

Criar apenas as suites necessárias ao plano de ação. Se houver backend e frontend, criar ambas. Sempre criar `Smoke` quando a entrega tiver fluxo end-to-end.

| Suite | Quando usar |
| ----- | ----------- |
| `Backend` | APIs, regras de negócio, jobs, integrações server-side |
| `Frontend` | Telas, componentes, navegação, UX |
| `Integracao` | Fluxo entre serviços, backend/frontend, mensageria, arquivos |
| `Regressao` | Garantir que comportamento existente não foi quebrado |
| `Smoke` | Validação rápida do fluxo principal entregue |

```bash
ROOT_SUITE_ID=$(az devops invoke \
  --organization "https://dev.azure.com/LesteDevOps" \
  --area testplan \
  --resource plans \
  --route-parameters project="$PROJECT_NAME" \
  --api-version 7.1 \
  --query "value[?id==\`${TEST_PLAN_ID}\`].rootSuite.id | [0]" -o tsv)

create_suite() {
  SUITE_NAME="$1"
  cat > test-suite.json <<EOF
{
  "suiteType": "staticTestSuite",
  "name": "${SUITE_NAME}",
  "parentSuite": { "id": ${ROOT_SUITE_ID} },
  "inheritDefaultConfigurations": true
}
EOF

  az devops invoke \
    --organization "https://dev.azure.com/LesteDevOps" \
    --area testplan \
    --resource suites \
    --route-parameters project="$PROJECT_NAME" planId="$TEST_PLAN_ID" \
    --api-version 7.1 \
    --http-method POST \
    --in-file test-suite.json \
    --query "id" -o tsv
}
```

> Se a suite já existir, reutilizar. Não duplicar suites com mesmo nome no mesmo Test Plan.

---

## Passo 3 — Criar Test Cases a partir das Issues

Para cada Issue/Bug criado ou incluído na sessão:

1. Extrair critérios de aceite da Issue.
2. Criar pelo menos 1 Test Case por critério de aceite relevante.
3. Vincular Test Case à Issue.
4. Adicionar Test Case à suite correta.

### Criar Test Case

```bash
TEST_CASE_ID=$(az boards work-item create \
  --title "#${ISSUE_ID} - <criterio-de-aceite-resumido>" \
  --type "Test Case" \
  --project "$PROJECT_NAME" \
  --org "https://dev.azure.com/LesteDevOps" \
  --fields \
    "System.AreaPath=$PROJECT_NAME" \
    "System.IterationPath=${PROJECT_NAME}\\${ITERATION_NAME}" \
    "System.Tags=TestPlan;${ITERATION_NAME};<suite>" \
    "System.Description=<passos, dados de entrada, resultado esperado e evidencia esperada>" \
  --query "id" -o tsv)
```

### Vincular Test Case à Issue

```bash
az devops work-item relation add \
  --id "$ISSUE_ID" \
  --target-id "$TEST_CASE_ID" \
  --relation-type "Related" \
  --org "https://dev.azure.com/LesteDevOps"
```

### Adicionar Test Case à Suite

Endpoint REST usado pelo Azure DevOps:

```text
POST https://dev.azure.com/LesteDevOps/{PROJECT_NAME}/_apis/test/Plans/{TEST_PLAN_ID}/suites/{SUITE_ID}/testcases/{TEST_CASE_ID}?api-version=7.1
```

Via `az devops invoke`:

```bash
az devops invoke \
  --organization "https://dev.azure.com/LesteDevOps" \
  --area test \
  --resource testcases \
  --route-parameters project="$PROJECT_NAME" planId="$TEST_PLAN_ID" suiteId="$SUITE_ID" testCaseIds="$TEST_CASE_ID" \
  --api-version 7.1 \
  --http-method POST
```

---

## Passo 4 — Evidência antes de mover para Review

Antes de mover uma Issue para `Review`, a IA deve registrar:

| Item | Obrigatório |
| ---- | ----------- |
| Test Plan | `PROJECT_NAME - ITERATION_NAME` |
| Suite(s) | Backend/Frontend/Integracao/Regressao/Smoke conforme aplicável |
| Test Cases | IDs vinculados à Issue |
| Resultado | `Passed`, `Failed`, `Blocked` ou `Not Run` |
| Evidência | Output de testes automatizados, prints/logs manuais, ou justificativa |

Se um Test Case estiver `Failed` ou `Blocked`, a Issue/Bug **não deve** ser movida para `Review` sem registrar o motivo e criar/relacionar o Bug de correção correspondente.

---

## Template para comentário final da Issue

```markdown
### Test Plans
- **Test Plan:** <PROJECT_NAME> - <ITERATION_NAME>
- **Suites:** <Backend | Frontend | Integracao | Regressao | Smoke>
- **Test Cases:** #<ID_1>, #<ID_2>
- **Resultado:** <Passed | Failed | Blocked | Not Run>
- **Evidências:** <logs, outputs, prints, links ou justificativa>
```

