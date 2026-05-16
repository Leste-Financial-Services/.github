# 💱 Currency Rules (Regras Multi-Moeda)

Regras para manipulação de diferentes moedas no portfólio.

## 1. Moedas Core (Base Currency)
- **BRL (Real)**: Moeda base para fundos Onshore (Brasil).
- **USD (Dólar)**: Moeda base para fundos Offshore (EUA, Cayman).
- **EUR (Euro)**: Considerado moeda secundária, conversão sempre para USD ou BRL.

## 2. Taxas de Câmbio (FX Rates)
- **PTAX (Banco Central)**: Uso obrigatório para fechamento BRL/USD diário.
- **Precisão**: Taxas de câmbio devem ser armazenadas com precisão mínima de `decimal(18,4)`.

## 3. Conversão e Agrupamento
- Relatórios consolidados devem permitir a escolha da moeda de apresentação.
- Ao somar ativos de moedas diferentes, a conversão deve usar a PTAX do dia de referência do relatório.
