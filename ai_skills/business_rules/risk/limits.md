# 📉 Risk Limits (Limites e Concentração)

Define as regras de exposição e concentração de risco.

## 1. Limites de Alocação
- **Concentração**: Nenhum ativo individual pode exceder o percentual estabelecido pela política de alocação do fundo (ex: 15% PL).
- **Validação**: Bloquear ou emitir alerta em tentativas de alocação que violem esses limites.

## 2. Alertas
- **Excesso de PL**: Disparar alerta se o Valor Líquido (NAV) do fundo sofrer variação > 5% em D-1.
- **Vencimento**: Monitorar e alertar sobre ativos com vencimento próximo (D+5).
