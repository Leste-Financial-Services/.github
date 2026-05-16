# ✅ Review Checklist (Critérios de Revisão Humana)

O checklist que a IA deve validar antes de mover para `Ready for Review`.

## 1. Integridade Técnica
- O código segue o padrão **Generic DDD**?
- Existe um script SQL em `scripts/sql/` para mudanças de banco?
- Os pacotes NuGet usados estão homologados?
- O `.gitignore` existe e cobre build outputs, caches, dependências, logs, arquivos temporários e arquivos de IDE/SO?
- Nenhum artefato gerado, pasta de dependência ou arquivo local foi incluído no commit?

## 2. Qualidade e Testes
- Foram gerados testes unitários (**xUnit**) cobrindo fluxos felizes e de erro?
- A cobertura de testes na camada de `Domain` e `Application` é satisfatória?
- A validação via `FluentValidation` está implementada?

## 3. Segurança e Auditoria
- Segredos estão no **Azure Key Vault**?
- O `.gitignore` ignora arquivos locais de desenvolvimento com senhas, connection strings, tokens, API keys, chaves de cloud e credenciais?
- Arquivos versionáveis estão sem credenciais reais, usando Key Vault, variáveis de ambiente, variáveis secretas de pipeline ou placeholders?
- Todo endpoint de API novo/alterado exige autenticação/autorização?
- Existe teste/cenário validando `401 Unauthorized` ou `403 Forbidden` para acesso sem credencial válida?
- Qualquer endpoint anônimo foi justificado na Issue, documentado no Scalar e limitado ao mínimo necessário?
- A auditoria via **Serilog** está registrando as ações críticas?
- Os endpoints estão documentados no **Scalar**?
