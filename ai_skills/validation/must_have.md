# ✅ Must Have (O que é obrigatório)

O firewall lógico da IA: o que a IA **SEMPRE** deve fazer.

## 1. Testes Unitários (**xUnit**)
- Todo código gerado deve vir com testes unitários cobrindo fluxos principais e de erro.
- Use **xUnit**, **Moq** ou **NSubstitute** e **FluentAssertions**.

## 2. Validação de Entrada (**FluentValidation**)
- Toda entrada de dados via API ou Job DEVE ser validada usando a biblioteca **FluentValidation**.
- As regras de validação devem estar localizadas na camada **Application**.

## 3. Auditoria Mandatória
- Registre o `UsuarioId`, `Timestamp`, `Acao` e `EntidadeAlterada` via **Serilog**.

## 4. Documentação API (**Scalar**)
- Todo endpoint deve estar documentado com sumários claros e códigos HTTP adequados.

## 5. Segurança Obrigatória em Endpoints de API
- Toda API/endpoint novo ou alterado DEVE exigir autenticação/autorização por padrão.
- Mecanismos aceitos incluem Azure Entra ID/OAuth2/OIDC/JWT, API Key, subscription key, mTLS ou padrão equivalente aprovado no projeto.
- Todo endpoint novo ou alterado DEVE ter teste/cenário cobrindo acesso sem credencial válida e retornando `401 Unauthorized` ou `403 Forbidden`.
- Qualquer endpoint anônimo só é permitido se for exceção explícita, justificada na Issue, documentada no Scalar e tecnicamente limitada ao mínimo necessário.

## 6. Proteção de Segredos e Arquivos Locais
- Antes de criar ou alterar arquivos locais de desenvolvimento, garantir que o `.gitignore` ignore arquivos com usuários, senhas, connection strings, tokens, API keys, chaves de cloud e credenciais.
- `.env`, `.env.*`, `*.local`, `*.local.*`, `appsettings.Development.json`, `appsettings.Local.json`, `appsettings.*.Local.json`, `appsettings.Development.local.json`, `local.settings.json`, `secrets.json` e `*.secrets.json` devem estar ignorados quando aplicáveis ao projeto.
- Arquivos versionáveis não podem conter credenciais reais. Se contiverem, substituir por Azure Key Vault, variável de ambiente, variável secreta de pipeline ou mecanismo equivalente antes de concluir.
- Templates versionáveis devem conter apenas placeholders, nunca valores reais.

## 7. Higiene Obrigatória do `.gitignore`
- Se o projeto não tiver `.gitignore`, a IA DEVE criá-lo antes de gerar arquivos locais, build outputs, caches ou dependências.
- O `.gitignore` DEVE ignorar artefatos de build, publicação, teste, cache, dependências baixadas, arquivos temporários, logs, arquivos de IDE e arquivos do sistema operacional.
- Padrões mínimos: `bin/`, `obj/`, `dist/`, `build/`, `out/`, `target/`, `publish/`, `coverage/`, `TestResults/`, `node_modules/`, `.next/`, `.nuxt/`, `.output/`, `.vite/`, `.quasar/`, `.svelte-kit/`, `.angular/cache/`, `.gradle/`, `__pycache__/`, `.pytest_cache/`, `.mypy_cache/`, `.ruff_cache/`, `.venv/`, `venv/`, `env/`, `.vs/`, `.idea/`, `.vscode/`, `*.log`, `logs/`, `*.tmp` e `*.temp`.
- Arquivos já versionados que sejam claramente artefatos gerados devem ser removidos do índice Git e mantidos localmente, sem apagar o arquivo do disco.
