# 🧠 Decision Flow (Fluxo de Decisão da IA)

Define o processo de raciocínio lógico que a IA deve seguir para cada solicitação.

## 0. ⛔ ETAPA ZERO — Análise de Impacto & Criação de Issues (MANDATÓRIO ABSOLUTO)

> **Esta etapa é OBRIGATÓRIA e deve ser executada ANTES de qualquer outra ação.**
> ⚡ **Criação de Issues e branches é automática — não requer aprovação prévia.**
>
> ### ❌ Comportamentos PROIBIDOS (para todos os modelos, incluindo Claude)
> - Perguntar "posso criar a Issue?", "devo registrar no backlog?", "quer que eu abra uma Issue?"
> - Iniciar código antes de criar as Issues
> - Pular a criação de Issues "para agilizar" ou "ir direto ao ponto"
> - Criar Issues apenas se o usuário pedir explicitamente
>
> ### ✅ Comportamento CORRETO
> Ao receber qualquer solicitação de desenvolvimento: **criar as Issues imediatamente**, sem perguntar, sem pausar, sem aguardar confirmação.

### Detecção de intenção de execução de Issues existentes
Se o usuário solicitar execução de Issues já existentes (ex: "execute a Issue #X", "execute as Issues em To Do"), **não criar novas Issues** — seguir diretamente `ai_skills/workflows/execute_issues.md`.

### Criação de novas Issues (fluxo padrão)
1. Executar o workflow `ai_skills/workflows/backlog_management.md` na íntegra.
2. Identificar quais projetos são impactados: **Backend**, **Frontend** ou **Ambos**.
3. Detectar automaticamente o email do revisor via `az account show` (ver Seção 1.0) — sem perguntar ao usuário.
4. Criar a(s) Issue(s) no Azure DevOps — **imediatamente, sem aguardar confirmação**.
5. Criar a(s) branch(es) em cada projeto impactado — **imediatamente, sem aguardar confirmação**.
6. Vincular as Issues entre projetos quando ambos forem impactados.
7. Apresentar o resumo ao usuário e iniciar a implementação.

> **Bloqueio**: Se não houver URL de Azure DevOps configurada (`[MISSING]`), interromper e solicitar configuração.

---

## 1. Perguntas Obrigatórias ao Usuário (antes de qualquer código)

Quando a solicitação envolver criação ou configuração de projeto, a IA **DEVE** perguntar:

### 1.0 — Detecção Automática do Revisor (sem perguntar ao usuário)

A IA **DEVE** detectar automaticamente o email do usuário logado no laptop:

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
```

> `<NOME_REVISOR>` é usado em `ReviewedBy` e `AssignedTo` ao mover para Review.
> **Nunca perguntar ao usuário** — a detecção é sempre automática.

### 1.1 — Framework Frontend (se houver impacto no frontend)
> "Qual framework deseja utilizar no frontend?"
> - **[1] Vue.js 3** — padrão Leste (Quasar + Pinia + Vite)
> - **[2] React 18** — (Vite + Zustand + TailwindCSS)

Aplicar os padrões correspondentes de `ai_skills/tech_rules/frontend/coding_standards.md`.

### 1.2 — Docker (sempre perguntar para qualquer projeto novo)
> "O projeto deve rodar em Docker?"
> - **[S] Sim** → Aplicar `ai_skills/tech_rules/docker.md` — **sempre Linux**, nunca Windows.
> - **[N] Não** → Prosseguir sem containerização.

---

## 2. Detecção de Desvio Arquitetural (DDD / Padrões)

Antes de implementar qualquer alteração em código existente, a IA **DEVE** analisar a estrutura do projeto e verificar conformidade com:
- Backend: Generic DDD (`ai_skills/tech_rules/backend/architecture.md`) — 4 camadas desacopladas.
- Frontend: Arquitetura por camadas (`ai_skills/tech_rules/frontend/architecture.md`) — separação components/pages/stores/services.

**Se detectar desvio dos padrões**, a IA deve **interromper** e informar o usuário:

```
⚠️ Desvio Arquitetural Detectado

O projeto atual apresenta os seguintes desvios em relação aos padrões DDD/Arquitetura:
- <Desvio 1 identificado — ex: "Lógica de negócio no Controller">
- <Desvio 2 identificado — ex: "Chamadas diretas ao banco na camada API">
- <Desvio 3 identificado — ex: "Ausência da camada Domain">

Deseja realizar um Refactoring para adequar ao padrão antes de prosseguir?
  [S] Sim — Criar Issue de Refactoring e corrigir a arquitetura primeiro.
  [N] Não — Continuar no padrão atual (o desvio ficará registrado na Discussion da Issue).
```

- Se **[S]**: Criar uma Issue específica de Refactoring no backlog, com tag `Refactoring`, e executá-la antes da feature solicitada.
- Se **[N]**: Registrar o desvio identificado como observação na Discussion da Issue atual e prosseguir.

---

## 3. Pesquisa & Contextualização
- Antes de sugerir ou agir, a IA deve validar se o conhecimento necessário está em `ai_skills/system_knowledge/`.
- Verificar se a tarefa impacta regras de negócio descritas em `ai_skills/business_rules/`.

## 4. Escolha de Estratégia
- **Greenfield**: Seguir `ai_skills/tech_rules/backend/architecture.md` (Generic DDD) + framework escolhido no Passo 1.
- **Legado/Manutenção**: Seguir as restrições em `ai_skills/system_knowledge/` + aplicar detecção de desvio do Passo 2.
- Priorizar sempre a simplicidade e a conformidade com as `Core Mandates` em `ai.md`.

## 5. Validação de Barreiras
- Antes de apresentar o resultado, validar contra as regras em `ai_skills/validation/forbidden.md`.
- Garantir que todos os `Must Have` em `ai_skills/validation/must_have.md` foram atendidos.
