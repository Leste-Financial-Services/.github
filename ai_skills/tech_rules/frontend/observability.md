# 👁️ Observability (Observabilidade & Logs)

Define como monitorar e diagnosticar a saúde da aplicação.

## 1. Serilog (Mandatário)
- Framework de logging estruturado padrão.
- Use templates (ex: `Log.Information("User {UserId}...", userId)`) em vez de interpolação simples.

## 2. Sinks Obrigatórios
- **Console**: Saída de logs em tempo real para desenvolvimento.
- **Application Insights**: Telemetria e monitoramento centralizado em produção.

## 3. OpenAPI (Scalar)
- **Scalar** (`/scalar/v1`) como única documentação de API permitida.
- Descontinuado: Swagger/Swashbuckle tradicional.
- **Sumários**: Enriqueça endpoints com descrições claras e códigos HTTP adequados.
