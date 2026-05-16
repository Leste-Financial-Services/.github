# ­ƒºá Workflow de Mem├│ria

## Ôøö REGRA INVIOL├üVEL
> **A mem├│ria DEVE ser atualizada SEMPRE, ap├│s CADA atividade, sem exce├º├úo.**
> **Uma atividade S├ô est├í conclu├¡da quando a mem├│ria (di├írio + _summary.md) foi atualizada.**
> **Se a IA n├úo atualizar a mem├│ria, a atividade ├® considerada INCOMPLETA.**

## Vis├úo Geral
A mem├│ria do projeto fica em `./memory/` na raiz de cada reposit├│rio.
Cada dia de trabalho gera um arquivo `{yyyy-MM-dd}.md` com **todos** os pedidos, intera├º├Áes e execu├º├Áes daquele dia.

```
{projeto}/
Ôö£ÔöÇÔöÇ memory/
Ôöé   Ôö£ÔöÇÔöÇ 2026-04-28.md      ÔåÉ Di├írio completo do dia
Ôöé   Ôö£ÔöÇÔöÇ 2026-04-29.md
Ôöé   Ôö£ÔöÇÔöÇ ...
Ôöé   ÔööÔöÇÔöÇ _summary.md         ÔåÉ Resumo compactado de dias antigos
ÔööÔöÇÔöÇ ai_skills/ (link)
```

> ÔÜá´©Å A pasta `memory/` deve estar no `.gitignore` ÔÇö ├® local por m├íquina/dev.

---

## ­ƒôÑ WORKFLOW: In├¡cio de Sess├úo (LEITURA)

Executar **ANTES** de qualquer a├º├úo:

```
1. Verificar se ./memory/ existe
   ÔööÔöÇ Se n├úo existir ÔåÆ criar a pasta

2. Ler ./memory/_summary.md (se existir)
   ÔööÔöÇ Contexto geral: decis├Áes, padr├Áes, hist├│rico compactado

3. Listar arquivos em ./memory/ ordenados por nome (desc)
   ÔööÔöÇ Pegar os 3 mais recentes (ex: 2026-04-28.md, 2026-04-27.md, 2026-04-26.md)
   ÔööÔöÇ Ler cada um para contexto detalhado recente

4. Verificar se existe ./memory/{data-de-hoje}.md
   ÔööÔöÇ Se existir ÔåÆ ler para saber o que j├í foi feito hoje
   ÔööÔöÇ Se n├úo existir ÔåÆ ser├í criado no primeiro registro
```

---

## ­ƒôØ WORKFLOW: Registro de Atividade (ESCRITA)

Executar **AP├ôS** concluir cada solicita├º├úo do usu├írio.

> **IMPORTANTE**: N├úo registre apenas INPUT ÔåÆ OUTPUT.
> Registre **TODO o processo**: o racioc├¡nio, as escolhas do usu├írio durante a execu├º├úo,
> os coment├írios da IA sobre decis├Áes, mudan├ºas de rumo, intera├º├Áes relevantes.

```
1. Determinar a data atual: yyyy-MM-dd

2. Verificar se ./memory/{yyyy-MM-dd}.md existe
   Ôö£ÔöÇ Se N├âO existir ÔåÆ CRIAR com cabe├ºalho:
   Ôöé   # ­ƒôà {yyyy-MM-dd} ÔÇö Di├írio de Atividades
   Ôöé   
   Ôöé   ## Resumo do Dia
   Ôöé   - Pedidos: 0
   Ôöé   - Issues: []
   Ôöé   - Temas: []
   Ôöé   - Tecnologias: []
   Ôöé   ---
   Ôöé
   ÔööÔöÇ Se existir ÔåÆ abrir para append

3. Contar quantos blocos ## [N] j├í existem ÔåÆ pr├│ximo N

4. Adicionar ao final do arquivo (formato completo abaixo)

5. Atualizar o "Resumo do Dia" no topo do arquivo:
   - Incrementar contador de pedidos
   - Adicionar issues, temas e tecnologias novas
```

### Formato de cada entrada:

```markdown
---

## [N] {HH:mm} ÔÇö {T├¡tulo curto do pedido}

### ­ƒÄ½ Issue
- **ID**: #{numero} (ou "Criada: #{numero}" se a IA criou nesta atividade)
- **T├¡tulo**: {t├¡tulo da issue/task no Azure DevOps}
- **URL**: {link para a issue}
> Se n├úo houver issue, registrar: "N/A ÔÇö motivo"

### ­ƒùú´©Å Pedido
{O que o usu├írio pediu ÔÇö texto fiel ou resumo claro}

### ­ƒöä Intera├º├Áes e Decis├Áes (o COMO)
Registro cronol├│gico de tudo que aconteceu DURANTE a execu├º├úo:

- **[HH:mm] IA**: {An├ílise inicial ÔÇö como interpretei o pedido, op├º├Áes consideradas}
- **[HH:mm] Usu├írio escolheu**: {Quando o usu├írio selecionou uma op├º├úo, mudou o pedido, 
  confirmou uma abordagem, ou deu qualquer input durante a execu├º├úo}
- **[HH:mm] IA decidiu**: {Decis├úo t├®cnica tomada e justificativa}
- **[HH:mm] IA observa├º├úo**: {Coment├írio relevante ÔÇö algo inesperado encontrado, 
  workaround aplicado, limita├º├úo descoberta, alerta sobre impacto}
- **[HH:mm] Usu├írio alterou**: {Se o usu├írio mudou de ideia, corrigiu algo, ou 
  pediu para refazer de outra forma}
- **[HH:mm] IA ajuste**: {Mudan├ºa de rumo baseada no feedback do usu├írio}

> Registre TODAS as intera├º├Áes relevantes, n├úo apenas o pedido inicial e resultado final.
> Isso inclui: escolhas do usu├írio na interface, coment├írios da IA sobre o que encontrou,
> decis├Áes de arquitetura, problemas descobertos, alertas dados, etc.

### ÔÜÖ´©Å Execu├º├úo
- **Arquivos criados**: {lista}
- **Arquivos modificados**: {lista}
- **Comandos executados**: {comandos relevantes}
- **Branch**: {nome da branch, se aplic├ível}
- **Commits**: {hashes ou mensagens dos commits feitos}

### Ô£à Resultado
{O que foi entregue} ÔÇö Status: Ô£à / ÔÅ│ / ÔØî

### ­ƒÆí Contexto Futuro
{Info que a IA deve lembrar: padr├Áes adotados, limita├º├Áes encontradas,
decis├Áes do usu├írio que afetam trabalhos futuros, pend├¬ncias}
```

---

## ­ƒù£´©Å WORKFLOW: Compacta├º├úo (MANUTEN├ç├âO)

Executar quando `./memory/` tiver **mais de 7 arquivos** `.md` (excluindo `_summary.md`):

```
1. Listar todos os {yyyy-MM-dd}.md ordenados por data
2. Manter os 7 mais recentes intactos
3. Para cada arquivo antigo:
   a. Ler o conte├║do completo
   b. Extrair decis├Áes permanentes ÔåÆ adicionar ├á se├º├úo "Decis├Áes Permanentes"
   c. Gerar resumo do dia ÔåÆ adicionar ├á se├º├úo "Hist├│rico de Dias"
   d. Deletar o arquivo di├írio antigo

4. Atualizar estat├¡sticas e "├Ültima Atividade" no _summary.md
```

---

## ­ƒôä WORKFLOW: Atualizar _summary.md (A CADA ATIVIDADE)

> **O `_summary.md` deve ser atualizado SEMPRE**, n├úo apenas na compacta├º├úo.
> Ap├│s CADA atividade registrada no di├írio, atualizar o _summary.md.

```
1. Atualizar se├º├úo "├Ültima Atividade" com resumo do que acabou de ser feito
2. Se houve decis├úo permanente ÔåÆ adicionar ├á se├º├úo "Decis├Áes Permanentes"
3. Se houve nova prefer├¬ncia do usu├írio ÔåÆ adicionar ├á se├º├úo correspondente
4. Atualizar estat├¡sticas
```

### Formato do `_summary.md`:

```markdown
# ­ƒô£ Mem├│ria Principal do Projeto

## ­ƒöä ├Ültima Atividade
- **Data**: {yyyy-MM-dd HH:mm}
- **Issue**: #{id} ÔÇö {t├¡tulo}
- **O que foi feito**: {resumo em 2-3 linhas}
- **Status**: Ô£à / ÔÅ│ / ÔØî

---

## ­ƒÅø´©Å Decis├Áes Permanentes
> Decis├Áes que devem ser respeitadas SEMPRE, independente do dia.
> A IA DEVE consultar esta se├º├úo antes de qualquer implementa├º├úo.
> Nunca remover ÔÇö apenas adicionar ou marcar como [REVOGADA em {data}].

### Arquitetura
- [{data}] {Decis├úo}: {descri├º├úo e justificativa}
  Ex: [2026-04-28] Usar Repository Pattern no backend ÔÇö decis├úo do usu├írio para facilitar testes

### Padr├Áes de C├│digo
- [{data}] {Padr├úo}: {descri├º├úo}
  Ex: [2026-04-28] Nomes de vari├íveis em ingl├¬s, coment├írios em portugu├¬s

### Prefer├¬ncias do Usu├írio
- [{data}] {Prefer├¬ncia}: {descri├º├úo}
  Ex: [2026-04-28] Sempre perguntar antes de refatorar c├│digo existente

### Conven├º├Áes do Projeto
- [{data}] {Conven├º├úo}: {descri├º├úo}
  Ex: [2026-04-28] Branches seguem padr├úo feature/{ID}-{descricao}

### Integra├º├Áes & Configura├º├Áes
- [{data}] {Integra├º├úo}: {descri├º├úo}
  Ex: [2026-04-28] Azure DevOps como backlog, issues obrigat├│rias

### Restri├º├Áes / Proibi├º├Áes
- [{data}] {Restri├º├úo}: {descri├º├úo}
  Ex: [2026-04-28] Nunca criar migrations no backend

---

## ­ƒôè Estat├¡sticas
- Total de dias registrados: {N}
- Total de atividades: {N}
- Issues trabalhadas: #{id1}, #{id2}, ...
- Per├¡odo: {primeira data} ÔÇö {├║ltima atividade}
- Tecnologias usadas: {lista acumulada}

---

## ­ƒôà Hist├│rico de Dias (compactados, mais recente primeiro)

### {yyyy-MM-dd}
- **Pedidos**: {N} atividades
- **Issues**: #{id1}, #{id2}
- **Resumo**: [1] {resumo + decis├Áes chave}. [2] {resumo}. ...
- **Decis├Áes**: {j├í migradas para "Decis├Áes Permanentes" acima}
- **Tech**: {tecnologias}
```

### O que vai para "Decis├Áes Permanentes" (migrar do di├írio):
- Escolhas de arquitetura (patterns, estrutura de pastas, etc.)
- Padr├Áes de c├│digo definidos pelo usu├írio ou pela IA e aceitos
- Prefer├¬ncias expl├¡citas do usu├írio ("eu prefiro X", "sempre fa├ºa Y")
- Conven├º├Áes de nomenclatura, branching, commits
- Integra├º├Áes configuradas (APIs, servi├ºos, ferramentas)
- Restri├º├Áes ("nunca fa├ºa X", "n├úo use Y")
- Qualquer decis├úo que a IA precisa lembrar em TODAS as sess├Áes futuras

### O que N├âO vai para "Decis├Áes Permanentes":
- Detalhes de implementa├º├úo espec├¡ficos de uma tarefa
- Bugs pontuais e seus fixes
- Comandos executados

---

## ­ƒöì WORKFLOW: Consulta (BUSCA)

Quando o usu├írio pedir informa├º├Áes hist├│ricas:

| Pedido do Usu├írio | A├º├úo da IA |
|---|---|
| "O que fizemos dia 28?" | Ler `./memory/2026-04-28.md` ou buscar em `_summary.md` |
| "Resumo da ├║ltima semana" | Compilar dos ├║ltimos 7 arquivos `{yyyy-MM-dd}.md` |
| "Quando implementamos o login?" | Buscar termo nos di├írios e no `_summary.md` |
| "O que foi feito na issue #1234?" | Buscar `#1234` nos di├írios e `_summary.md` |
| "Por que escolhemos bcrypt?" | Buscar nas intera├º├Áes/decis├Áes dos di├írios |
| "Repete o que fez h├í 3 dias" | Calcular data, ler o di├írio, replicar |
| "Resumo geral do projeto" | Ler `_summary.md` completo |

---

## ÔÜá´©Å Regras Cr├¡ticas
- **`_summary.md` ├® a mem├│ria principal** ÔÇö atualizado a CADA atividade, n├úo s├│ na compacta├º├úo
- **Decis├Áes Permanentes nunca s├úo removidas** ÔÇö apenas marcadas como [REVOGADA] se mudarem
- **Append-only** dentro do dia (nunca editar entradas passadas do dia)
- Registrar **hor├írio** de cada pedido e intera├º├úo
- Registrar **issues** sempre (a IA ├® obrigada a criar issue antes de executar)
- Registrar o **COMO**, n├úo s├│ o IN/OUT ÔÇö escolhas, racioc├¡nio, mudan├ºas de rumo
- Na d├║vida sobre contexto passado, **SEMPRE consultar `_summary.md` primeiro**
- Se `./memory/` n├úo existir, **criar automaticamente**
- Se `_summary.md` n├úo existir, **criar com template vazio**
