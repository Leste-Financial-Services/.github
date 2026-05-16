# 🧩 Audit Policy (Política de Auditoria)

Garante que toda ação crítica no sistema seja rastreável e reportável.

## 1. Registro de Ações
- Toda criação, alteração ou exclusão de registros deve ser logada estruturadamente via **Serilog**.
- O log deve conter: `UserId`, `Timestamp`, `Action`, `EntityName` e `EntityId`.

## 2. Telemetria Azure
- Integração mandatória com **Azure Application Insights**.
- Logar falhas de autenticação e acesso a recursos sensíveis.

## 3. Retenção
- Logs de auditoria devem seguir a política de retenção da **Leste** para fins de compliance e LGPD.
