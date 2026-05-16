# ✅ Review Checklist (Critérios de Revisão Humana)

O checklist que a IA deve validar antes de mover para `Ready for Review`.

## 1. Integridade Técnica
- O código segue o padrão **Generic DDD**?
- Existe um script SQL em `scripts/sql/` para mudanças de banco?
- Os pacotes NuGet usados estão homologados?

## 2. Qualidade e Testes
- Foram gerados testes unitários (**xUnit**) cobrindo fluxos felizes e de erro?
- A cobertura de testes na camada de `Domain` e `Application` é satisfatória?
- A validação via `FluentValidation` está implementada?

## 3. Segurança e Auditoria
- Segredos estão no **Azure Key Vault**?
- A auditoria via **Serilog** está registrando as ações críticas?
- Os endpoints estão documentados no **Scalar**?
