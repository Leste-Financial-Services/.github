# 💵 Cashflow Logic (Regras de Fluxo de Caixa)

Regras para gestão de entradas e saídas financeiras.

## 1. Integridade de Datas
- **Vencimento**: Fluxos com vencimento em feriados devem ser provisionados para o próximo dia útil (`NextBusinessDay`).
- **Validação**: Data de efetivação não pode ser retroativa para datas anteriores ao fechamento contábil.

## 2. Direção do Fluxo
- **Sinal**: Entradas (Inflow) devem ser positivas e Saídas (Outflow) negativas, salvo em relatórios de apresentação que invertam visualmente.
- **Tipagem**: Identificar explicitamente o tipo do fluxo (`CapitalCall`, `Distribution`, `Expense`, `Fee`).

## 3. Conciliação
- Todo registro de fluxo de caixa deve estar vinculado a uma **Entidade** ou **Fundo** válido no LBOS.
- Fluxos superiores a um limite parametrizado devem disparar alerta de conciliação.
