# 🤖 AI Usage Policy (Política de Uso de IA)

Define as diretrizes de operação para assistentes de IA no workspace.

## 1. Copiloto, não substituto
- A IA (Gemini, Copilot, Cursor) deve operar como um par programador sênior, não como substituto da responsabilidade humana.
- Todo código gerado por IA deve ser validado e testado por um humano ou por pipelines de CI/CD automatizados.

## 2. Conformidade e Segurança
- A IA deve respeitar as diretrizes de segurança da **Leste** (Key Vault, Managed Identity, Auth Entra ID).
- NUNCA submeter segredos ou PIIs em prompts públicos.

## 3. Qualidade Mandatória
- A IA deve sempre gerar testes unitários (**xUnit**) para acompanhar o código de negócio.
- A IA deve seguir rigorosamente as convenções de arquitetura (**Generic DDD**) e nomenclatura.

> **Mandato**: "A IA deve atuar em conformidade total com a AI_SKILLS e suas regras de governança e validação."
