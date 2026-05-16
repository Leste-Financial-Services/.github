# 💰 NAV Calculation Rules (Cálculo de Cota/NAV)

Regras para o cálculo do Net Asset Value dos fundos.

## 1. Precisão e Arredondamento
- **Tipo de Dado**: Obrigatório uso de `decimal(18,8)` para cálculo de cotas e `decimal(18,2)` para valores financeiros monetários.
- **Arredondamento**: Padrão `MidpointRounding.ToEven` (Banker's Rounding) a menos que especificado diferente pelo regulamento do fundo.

## 2. Calendário e Dias Úteis
- **Framework**: Obrigatório uso de `Leste.Framework.Calendar` para determinar datas de cotização.
- **Regra**: O NAV não é calculado em feriados bancários (considerar feriados da praça do fundo: BRL vs USD).

## 3. Share Classes (Classes de Cotas)
- O cálculo deve respeitar a segregação por classe de cota (ex: Onshore Feeder, Offshore Master).
- Taxas de administração e performance devem ser deduzidas antes do fechamento do NAV diário.
