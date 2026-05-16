# 📊 Risk Levels (Níveis de Risco)

Define o grau de sensibilidade e impacto de cada operação.

## 1. Nível Crítico (High Risk)
- Manipulação de dados financeiros (votos, saldos, extratos).
- Dados de PII (Padrão de Segurança Pessoal) conforme LGPD.
- Mudanças em tabelas core do sistema.

## 2. Nível Médio (Medium Risk)
- Alterações em lógica de negócio não financeira.
- Novos endpoints de consulta (Read-only).
- Alterações de UI/UX em componentes não críticos.

## 3. Nível Baixo (Low Risk)
- Refatoração de código sem impacto funcional.
- Mudança em documentação ou comentários.
- Correção de typo (erros de digitação).
