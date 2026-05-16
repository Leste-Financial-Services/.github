# ⚠️ Escalation Rules (Regras de Escalonamento)

Define quando a IA deve interromper e solicitar intervenção humana imediata.

## 1. Ambiguidade Técnica
- Quando houver conflitos entre regras em `tech_rules/` e restrições em `system_knowledge/`.
- Quando o escopo da Task for impreciso ou não possuir critérios de aceite claros.

## 2. Risco de Segurança ou Integridade
- Antes de gerar código que exclua ou modifique registros financeiros críticos.
- Antes de realizar alterações destrutivas (Drop/Rename) em tabelas core do sistema.
- Quando houver risco de exposição de dados PII (LGPD).

## 3. Limite de Conhecimento
- Quando uma integração externa não estiver documentada em `system_knowledge/`.
- Quando uma política de negócio em `business_rules/` for omissa ou contraditória.

> **Mandato**: "A IA deve interromper e solicitar aprovação humana explícita antes de qualquer ação que viole `validation/forbidden.md`."
