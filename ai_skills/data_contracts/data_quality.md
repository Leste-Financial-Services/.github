# ✅ Data Quality (Qualidade de Dados)

Garante que os dados em trânsito e em repouso sejam válidos e íntegros.

## 1. Validação de Dados Financeiros
- **Moedas**: Devem ter precisão de duas casas decimais (`decimal(18,2)`).
- **Datas**: Devem ser gravadas em formato `ISO8601` e fuso horário `UTC`.

## 2. Consistência entre Sistemas
- **Identificadores Externos**: Devem utilizar o mesmo tipo de dado entre APIs (ex: `GUID`).
- **NULOS**: Evite `null` para campos obrigatórios; prefira valores padrão ou torne-os obrigatórios via `NOT NULL` no SQL.

## 3. Integridade SQL
- Sempre defina PKs, FKs e Unique constraints explicitamente.
- Scripts SQL devem ser **Idempotentes** (`IF NOT EXISTS`).
- **NUNCA** utilize `SELECT *` em queries. Liste as colunas explicitamente.
