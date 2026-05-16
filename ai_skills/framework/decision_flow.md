# 🔄 Fluxo de Decisão da IA

## Início de TODA interação

```
┌─────────────────────────────────────────┐
│         NOVA SOLICITAÇÃO DO USUÁRIO      │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│ 1. CONSULTAR MEMÓRIA LOCAL              │
│    - Seguir workflows/memory_workflow.md │
│    - Ler ./memory/_summary.md           │
│    - Ler últimos 3 diários recentes     │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│ 2. VERIFICAR STACK PREFERENCES          │
│    - Ler config/stack_preferences.md    │
│    - Se [NÃO CONFIGURADO] → PERGUNTAR  │
│    - Usar linguagens definidas          │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│ 3. VERIFICAR REUSO / CONHECIMENTO       │
│    - Ler project_knowledge_workflow.md  │
│    - Buscar no indice central           │
│    - Se houver match, abrir docs locais │
│    - Frontend: checar endpoints prod    │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│ 4. CRIAR ISSUE (OBRIGATÓRIO)            │
│    ⚠️ SEMPRE criar issue no Azure DevOps │
│    - Org: https://dev.azure.com/        │
│      LesteDevOps                        │
│    - Auth: credenciais do usuário       │
│      via az login                       │
│    - Antes de qualquer código/alteração │
│    - Título claro do que será feito     │
│    - Vincular ao projeto correto        │
│    - Se impactar ambos projetos, criar  │
│      uma issue em cada e vincular       │
│    - Informar ID ao usuário             │
│    ❌ SEM ISSUE = SEM EXECUÇÃO          │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│ 5. ANALISAR IMPACTO                     │
│    - Backend? Frontend? Ambos?          │
│    - Consultar tech_rules/ relevantes   │
│    - Consultar business_rules/          │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│ 6. CARREGAR CONTEXTO MINIMO             │
│    - Seguir workflows/token_efficient_  │
│      context.md                         │
│    - Para Microsoft/Azure/Graph, usar   │
│      microsoft-learn-skill              │
│    - Ler routers/indices antes de       │
│      referencias detalhadas             │
│    - Carregar apenas regras relevantes  │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│ 7. EXECUTAR                             │
│    - Implementar na linguagem correta   │
│    - Seguir padrões da stack definida   │
│    - Respeitar decisões anteriores      │
│    - Registrar interações/decisões      │
│      durante a execução                 │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│ 8. REGISTRAR NA MEMÓRIA LOCAL           │
│    ⛔ OBRIGATÓRIO — SEM EXCEÇÃO         │
│    - Seguir workflows/memory_workflow.md │
│    - Append em ./memory/{yyyy-MM-dd}.md │
│    - Atualizar ./memory/_summary.md:    │
│      • "Última Atividade"              │
│      • "Decisões Permanentes" (se há)  │
│    - Compactar se > 7 diários           │
│    ❌ SEM MEMÓRIA = ATIVIDADE INCOMPLETA│
└─────────────────────────────────────────┘
```

## Regras de Prioridade
1. **Issue obrigatória** > Qualquer execução (sem issue, não faz nada)
2. **Memória obrigatória** > Atividade concluída (sem registro, não está feito)
3. **Memória** > Suposições (sempre consultar antes de decidir)
4. **Reuso/conhecimento de projetos** > Criar funcionalidade duplicada
5. **Stack configurada** > Preferência da IA (usar o que o usuário definiu)
6. **Microsoft Learn** > Suposições sobre Azure, Graph, Entra e ambiente Microsoft
7. **Contexto mínimo** > Leitura ampla (usar routers e carregar detalhes sob demanda)
8. **Consistência** > Novidade (manter padrões já estabelecidos)
9. **Perguntar** > Assumir (na dúvida, perguntar ao usuário)
