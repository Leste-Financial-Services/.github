# 🧠 Memory System — Memória Persistente da IA

## ⛔ REGRA INVIOLÁVEL
> **A memória DEVE ser atualizada SEMPRE, após CADA atividade, sem exceção.**
> **Uma atividade SÓ está concluída quando a memória foi atualizada.**
> **Se a IA não atualizar a memória, a atividade é considerada INCOMPLETA.**

## Localização
A memória fica na raiz de cada projeto:
```
{raiz-do-projeto}/memory/
```
Exemplo: `D:\Source\Devops\ITProjects\Sentinel.Backend\memory\`

## Estrutura
```
{projeto}/memory/
├── _summary.md      ← MEMÓRIA PRINCIPAL — decisões permanentes + última atividade
├── 2026-04-28.md    ← Diário do dia (todos os pedidos, interações e execuções)
├── 2026-04-29.md
└── ...
```

## Workflow Completo
> **`./workflows/memory_workflow.md`**

## Checklist Obrigatório (a cada atividade)
- [ ] Li `_summary.md` antes de começar?
- [ ] Li os últimos 3 diários antes de começar?
- [ ] Registrei a atividade no diário `{yyyy-MM-dd}.md`?
- [ ] Atualizei "Última Atividade" no `_summary.md`?
- [ ] Adicionei decisões permanentes ao `_summary.md` (se houve)?
- [ ] Verifiquei se preciso compactar (>7 diários)?

**Se qualquer item acima não foi feito, a atividade NÃO está concluída.**
