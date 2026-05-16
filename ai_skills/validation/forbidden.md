# 🚫 Forbidden (O que é proibido)

O firewall lógico da IA: o que a IA **NUNCA** deve fazer.

## 1. SELECT *
- **NUNCA** utilize `SELECT *` em SQL. Liste as colunas explicitamente.

## 2. EF Core Migrations
- **PROIBIDO**: `Add-Migration`, `Update-Database`, etc.
- Toda mudança estrutural deve ser via script SQL manual em `scripts/sql/`.

## 3. Swagger/Swashbuckle
- **PROIBIDO**: Use exclusivamente o **Scalar** (`/scalar/v1`).

## 4. Exposição de Domínio
- **NUNCA** exponha Entidades de Domínio diretamente na camada de API. Sempre utilize **DTOs (records)**.

## 5. Bypassing Azure Key Vault
- **NUNCA** armazene segredos em arquivos de configuração ou código.

## 6. Endpoint de API sem Segurança
- **PROIBIDO** criar, alterar, publicar ou mover para `Review` qualquer API com endpoint acessível sem autenticação/autorização.
- **PROIBIDO** deixar endpoint anônimo por omissão, esquecimento ou conveniência.
- **PROIBIDO** aceitar acesso público implícito. Se o endpoint precisar ser público, a exceção deve estar justificada na Issue, documentada no Scalar e restrita ao menor escopo possível.
- **PROIBIDO** concluir a atividade sem teste/cenário que prove bloqueio de acesso sem credencial válida (`401` ou `403`).

## 7. Versionamento de Segredos e Configurações Locais
- **PROIBIDO** commitar arquivos locais de desenvolvimento com usuários, senhas, connection strings, tokens, API keys, chaves de cloud ou credenciais.
- **PROIBIDO** criar ou alterar arquivo local sensível sem antes garantir a regra correspondente no `.gitignore`.
- **PROIBIDO** commitar credenciais reais em arquivos não locais, mesmo que o arquivo seja necessário para a aplicação.
- **PROIBIDO** trocar uma credencial real por outra credencial real. Deve ser usado Azure Key Vault, variável de ambiente, variável secreta de pipeline ou mecanismo equivalente.

## 8. Versionamento de Build, Cache e Dependências Locais
- **PROIBIDO** commitar artefatos de build/publicação, caches, dependências baixadas, arquivos temporários, logs, arquivos de IDE ou arquivos de sistema operacional.
- **PROIBIDO** gerar build outputs, caches ou dependências locais antes de criar/atualizar o `.gitignore` com os padrões correspondentes.
- **PROIBIDO** commitar pastas como `bin/`, `obj/`, `dist/`, `build/`, `out/`, `target/`, `publish/`, `coverage/`, `TestResults/`, `node_modules/`, `.next/`, `.nuxt/`, `.output/`, `.vite/`, `.quasar/`, `.svelte-kit/`, `.angular/cache/`, `.gradle/`, `__pycache__/`, `.pytest_cache/`, `.mypy_cache/`, `.ruff_cache/`, `.venv/`, `venv/`, `env/`, `.vs/`, `.idea/` ou `.vscode/`.
