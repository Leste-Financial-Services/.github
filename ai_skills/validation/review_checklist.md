# ✅ Review Checklist (Critérios de Revisão Humana)

O checklist que a IA deve validar antes de mover para `Ready for Review`.

## 1. Integridade Técnica
- O código segue o padrão **Generic DDD**?
- Existe um script SQL em `scripts/sql/` para mudanças de banco?
- Os pacotes NuGet usados estão homologados?
- APIs criadas/alteradas possuem endpoints/contratos paralelos AI First para a funcionalidade solicitada?
- Os endpoints AI First reutilizam os mesmos use cases da Application, sem duplicar regra de negócio?

## 2. Qualidade e Testes
- Foram gerados testes unitários (**xUnit**) cobrindo fluxos felizes e de erro?
- A cobertura de testes na camada de `Domain` e `Application` é satisfatória?
- A validação via `FluentValidation` está implementada?

## 3. Segurança e Auditoria
- Segredos estão no **Azure Key Vault**?
- A auditoria via **Serilog** está registrando as ações críticas?
- Os endpoints estão documentados no **Scalar**?
- APIs criadas/alteradas têm autenticação/autorização ou chave validada antes de deploy na Azure?
- Nenhuma API seguirá para produção aberta sem Entra ID, API key/subscription key, Managed Identity/API Management, ou mecanismo aprovado?
- Frontends/telas criadas ou alteradas têm autenticação definida, ou o usuário confirmou explicitamente que o site deve ser aberto?
- Se autenticação frontend será implementada, foi perguntado se deve espelhar outro frontend existente e consultado o knowledge `.md` relevante?
- Endpoints AI First seguem o mesmo modelo de autenticação/autorização da API principal?
