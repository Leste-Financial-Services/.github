# 🔄 Fund Lifecycle (Ciclo de Vida do Fundo)

Define os status e transições permitidas para um fundo de investimento no LBOS.

## 1. Criação (Inception)
- **Status Inicial**: `Draft` ou `Active` com Data de Início (`StartDate`) válida.
- **Categorização**: Definir a classe de cota e o público-alvo (Qualificado, Profissional).

## 2. Status Permitidos
- `Active`: Operacional e aberto para alocação.
- `Closed`: Fechado para novas alocações, mas com ativos em carteira.
- `Liquidating`: Processo de desinvestimento e encerramento.
- `Terminated`: Fundo encerrado e liquidado.

## 3. Integridade de Dados
- **CNPJ/TaxID**: Campo obrigatório e único.
- **Nome Legal**: Deve seguir o regulamento do fundo.
