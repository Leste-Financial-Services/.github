# 🧠 Workflow de Memória

## ⛔ REGRA INVIOLÁVEL
> **A memória DEVE ser atualizada SEMPRE, após CADA atividade, sem exceção.**
> **Uma atividade SÓ está concluída quando a memória (diário + _summary.md) foi atualizada.**
> **Se a IA não atualizar a memória, a atividade é considerada INCOMPLETA.**

## Visão Geral
A memória do projeto fica em `./memory/` na raiz de cada repositório.
Cada dia de trabalho gera um arquivo `{yyyy-MM-dd}.md` com **todos** os pedidos, interações e execuções daquele dia.

```
{projeto}/
├── memory/
│   ├── 2026-04-28.md      ← Diário completo do dia
│   ├── 2026-04-29.md
│   ├── ...
│   └── _summary.md         ← Resumo compactado de dias antigos
└── ai_skills/ (link)
```

> ⚠️ A pasta `memory/` deve estar no `.gitignore` — é local por máquina/dev.

---

## 📥 WORKFLOW: Início de Sessão (LEITURA)

Executar **ANTES** de qualquer ação:

```
1. Verificar se ./memory/ existe
   └─ Se não existir → criar a pasta

2. Ler ./memory/_summary.md (se existir)
   └─ Contexto geral: decisões, padrões, histórico compactado

3. Listar arquivos em ./memory/ ordenados por nome (desc)
   └─ Pegar os 3 mais recentes (ex: 2026-04-28.md, 2026-04-27.md, 2026-04-26.md)
   └─ Ler cada um para contexto detalhado recente

4. Verificar se existe ./memory/{data-de-hoje}.md
   └─ Se existir → ler para saber o que já foi feito hoje
   └─ Se não existir → será criado no primeiro registro
```

---

## 📝 WORKFLOW: Registro de Atividade (ESCRITA)

Executar **APÓS** concluir cada solicitação do usuário.

> **IMPORTANTE**: Não registre apenas INPUT → OUTPUT.
> Registre **TODO o processo**: o raciocínio, as escolhas do usuário durante a execução,
> os comentários da IA sobre decisões, mudanças de rumo, interações relevantes.

```
1. Determinar a data atual: yyyy-MM-dd

2. Verificar se ./memory/{yyyy-MM-dd}.md existe
   ├─ Se NÃO existir → CRIAR com cabeçalho:
   │   # 📅 {yyyy-MM-dd} — Diário de Atividades
   │   
   │   ## Resumo do Dia
   │   - Pedidos: 0
   │   - Issues: []
   │   - Temas: []
   │   - Tecnologias: []
   │   ---
   │
   └─ Se existir → abrir para append

3. Contar quantos blocos ## [N] já existem → próximo N

4. Adicionar ao final do arquivo (formato completo abaixo)

5. Atualizar o "Resumo do Dia" no topo do arquivo:
   - Incrementar contador de pedidos
   - Adicionar issues, temas e tecnologias novas
```

### Formato de cada entrada:

```markdown
---

## [N] {HH:mm} — {Título curto do pedido}

### 🎫 Issue
- **ID**: #{numero} (ou "Criada: #{numero}" se a IA criou nesta atividade)
- **Título**: {título da issue/task no Azure DevOps}
- **URL**: {link para a issue}
> Se não houver issue, registrar: "N/A — motivo"

### 🗣️ Pedido
{O que o usuário pediu — texto fiel ou resumo claro}

### 🔄 Interações e Decisões (o COMO)
Registro cronológico de tudo que aconteceu DURANTE a execução:

- **[HH:mm] IA**: {Análise inicial — como interpretei o pedido, opções consideradas}
- **[HH:mm] Usuário escolheu**: {Quando o usuário selecionou uma opção, mudou o pedido, 
  confirmou uma abordagem, ou deu qualquer input durante a execução}
- **[HH:mm] IA decidiu**: {Decisão técnica tomada e justificativa}
- **[HH:mm] IA observação**: {Comentário relevante — algo inesperado encontrado, 
  workaround aplicado, limitação descoberta, alerta sobre impacto}
- **[HH:mm] Usuário alterou**: {Se o usuário mudou de ideia, corrigiu algo, ou 
  pediu para refazer de outra forma}
- **[HH:mm] IA ajuste**: {Mudança de rumo baseada no feedback do usuário}

> Registre TODAS as interações relevantes, não apenas o pedido inicial e resultado final.
> Isso inclui: escolhas do usuário na interface, comentários da IA sobre o que encontrou,
> decisões de arquitetura, problemas descobertos, alertas dados, etc.

### ⚙️ Execução
- **Arquivos criados**: {lista}
- **Arquivos modificados**: {lista}
- **Comandos executados**: {comandos relevantes}
- **Branch**: {nome da branch, se aplicável}
- **Commits**: {hashes ou mensagens dos commits feitos}

### ✅ Resultado
{O que foi entregue} — Status: ✅ / ⏳ / ❌

### 💡 Contexto Futuro
{Info que a IA deve lembrar: padrões adotados, limitações encontradas,
decisões do usuário que afetam trabalhos futuros, pendências}
```

---

## 🗜️ WORKFLOW: Compactação (MANUTENÇÃO)

Executar quando `./memory/` tiver **mais de 7 arquivos** `.md` (excluindo `_summary.md`):

```
1. Listar todos os {yyyy-MM-dd}.md ordenados por data
2. Manter os 7 mais recentes intactos
3. Para cada arquivo antigo:
   a. Ler o conteúdo completo
   b. Extrair decisões permanentes → adicionar à seção "Decisões Permanentes"
   c. Gerar resumo do dia → adicionar à seção "Histórico de Dias"
   d. Deletar o arquivo diário antigo

4. Atualizar estatísticas e "Última Atividade" no _summary.md
```

---

## 📄 WORKFLOW: Atualizar _summary.md (A CADA ATIVIDADE)

> **O `_summary.md` deve ser atualizado SEMPRE**, não apenas na compactação.
> Após CADA atividade registrada no diário, atualizar o _summary.md.

```
1. Atualizar seção "Última Atividade" com resumo do que acabou de ser feito
2. Se houve decisão permanente → adicionar à seção "Decisões Permanentes"
3. Se houve nova preferência do usuário → adicionar à seção correspondente
4. Atualizar estatísticas
```

### Formato do `_summary.md`:

```markdown
# 📜 Memória Principal do Projeto

## 🔄 Última Atividade
- **Data**: {yyyy-MM-dd HH:mm}
- **Issue**: #{id} — {título}
- **O que foi feito**: {resumo em 2-3 linhas}
- **Status**: ✅ / ⏳ / ❌

---

## 🏛️ Decisões Permanentes
> Decisões que devem ser respeitadas SEMPRE, independente do dia.
> A IA DEVE consultar esta seção antes de qualquer implementação.
> Nunca remover — apenas adicionar ou marcar como [REVOGADA em {data}].

### Arquitetura
- [{data}] {Decisão}: {descrição e justificativa}
  Ex: [2026-04-28] Usar Repository Pattern no backend — decisão do usuário para facilitar testes

### Padrões de Código
- [{data}] {Padrão}: {descrição}
  Ex: [2026-04-28] Nomes de variáveis em inglês, comentários em português

### Preferências do Usuário
- [{data}] {Preferência}: {descrição}
  Ex: [2026-04-28] Sempre perguntar antes de refatorar código existente

### Convenções do Projeto
- [{data}] {Convenção}: {descrição}
  Ex: [2026-04-28] Branches seguem padrão feature/{ID}-{descricao}

### Integrações & Configurações
- [{data}] {Integração}: {descrição}
  Ex: [2026-04-28] Azure DevOps como backlog, issues obrigatórias

### Restrições / Proibições
- [{data}] {Restrição}: {descrição}
  Ex: [2026-04-28] Nunca criar migrations no backend

---

## 📊 Estatísticas
- Total de dias registrados: {N}
- Total de atividades: {N}
- Issues trabalhadas: #{id1}, #{id2}, ...
- Período: {primeira data} — {última atividade}
- Tecnologias usadas: {lista acumulada}

---

## 📅 Histórico de Dias (compactados, mais recente primeiro)

### {yyyy-MM-dd}
- **Pedidos**: {N} atividades
- **Issues**: #{id1}, #{id2}
- **Resumo**: [1] {resumo + decisões chave}. [2] {resumo}. ...
- **Decisões**: {já migradas para "Decisões Permanentes" acima}
- **Tech**: {tecnologias}
```

### O que vai para "Decisões Permanentes" (migrar do diário):
- Escolhas de arquitetura (patterns, estrutura de pastas, etc.)
- Padrões de código definidos pelo usuário ou pela IA e aceitos
- Preferências explícitas do usuário ("eu prefiro X", "sempre faça Y")
- Convenções de nomenclatura, branching, commits
- Integrações configuradas (APIs, serviços, ferramentas)
- Restrições ("nunca faça X", "não use Y")
- Qualquer decisão que a IA precisa lembrar em TODAS as sessões futuras

### O que NÃO vai para "Decisões Permanentes":
- Detalhes de implementação específicos de uma tarefa
- Bugs pontuais e seus fixes
- Comandos executados

---

## 🔍 WORKFLOW: Consulta (BUSCA)

Quando o usuário pedir informações históricas:

| Pedido do Usuário | Ação da IA |
|---|---|
| "O que fizemos dia 28?" | Ler `./memory/2026-04-28.md` ou buscar em `_summary.md` |
| "Resumo da última semana" | Compilar dos últimos 7 arquivos `{yyyy-MM-dd}.md` |
| "Quando implementamos o login?" | Buscar termo nos diários e no `_summary.md` |
| "O que foi feito na issue #1234?" | Buscar `#1234` nos diários e `_summary.md` |
| "Por que escolhemos bcrypt?" | Buscar nas interações/decisões dos diários |
| "Repete o que fez há 3 dias" | Calcular data, ler o diário, replicar |
| "Resumo geral do projeto" | Ler `_summary.md` completo |

---

## ⚠️ Regras Críticas
- **`_summary.md` é a memória principal** — atualizado a CADA atividade, não só na compactação
- **Decisões Permanentes nunca são removidas** — apenas marcadas como [REVOGADA] se mudarem
- **Append-only** dentro do dia (nunca editar entradas passadas do dia)
- Registrar **horário** de cada pedido e interação
- Registrar **issues** sempre (a IA é obrigada a criar issue antes de executar)
- Registrar o **COMO**, não só o IN/OUT — escolhas, raciocínio, mudanças de rumo
- Na dúvida sobre contexto passado, **SEMPRE consultar `_summary.md` primeiro**
- Se `./memory/` não existir, **criar automaticamente**
- Se `_summary.md` não existir, **criar com template vazio**
