# 📊 Schemas (Contratos de Dados)

Define como os dados devem existir e trafegar.

## 1. DTOs & Records (C#)
- **DTOs**: Utilizar obrigatoriamente `record` (Imutabilidade).
- **Entidades**: NUNCA exponha Entidades de Domínio diretamente em APIs.

## 2. Tipos de Dados
- **Dinheiro**: Utilizar exclusivamente `decimal(18,2)`.
- **Datas**: Utilizar `ISO8601` e fuso horário `UTC` (`DateTimeOffset`).
- **Identificadores**: Utilizar `Guid` para IDs externos (ex: `InvestorId`).

## 3. Mapeamento Mandatário
- Utilize **AutoMapper 13.0+** para conversões na camada **Application**.
- Utilize **Refit 7.0+** para contratos de APIs externas.
