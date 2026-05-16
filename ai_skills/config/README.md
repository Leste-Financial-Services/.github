# ⚙️ Config — Configurações do Projeto

Esta pasta contém as configurações persistentes que a IA usa para tomar decisões.

## Arquivos

- **stack_preferences.md** — Define as linguagens e frameworks do Backend e Frontend.
  A IA deve consultar este arquivo antes de qualquer implementação.
  Se estiver como `[NÃO CONFIGURADO]`, deve perguntar ao usuário.

## Regra de Ouro
> A IA **NUNCA** deve assumir uma linguagem/framework sem verificar `stack_preferences.md` primeiro.
