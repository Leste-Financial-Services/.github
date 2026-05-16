# ÔÜÖ´©Å Config ÔÇö Configura├º├Áes do Projeto

Esta pasta cont├®m as configura├º├Áes persistentes que a IA usa para tomar decis├Áes.

## Arquivos

- **stack_preferences.md** ÔÇö Define as linguagens e frameworks do Backend e Frontend.
  A IA deve consultar este arquivo antes de qualquer implementa├º├úo.
  Se estiver como `[N├âO CONFIGURADO]`, deve perguntar ao usu├írio.

## Regra de Ouro
> A IA **NUNCA** deve assumir uma linguagem/framework sem verificar `stack_preferences.md` primeiro.
