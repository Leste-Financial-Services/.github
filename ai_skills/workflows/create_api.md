# 🚀 Create API Workflow (Criação de APIs .NET 8)

Define o passo a passo para implementar uma nova API seguindo o padrão da Leste.

## 1. Planejamento (SQL First)
- **Ação**: Criar o script SQL em `scripts/sql/YYYY-MM-DD_demand/`.
- **Regra**: Seguir a nomenclatura `snake_case` singular e incluir campos de auditoria.

## 2. Camada Domain
- **Ação**: Criar a Entidade de Domínio e a interface do Repositório (`I[Entity]Repository`).
- **Regra**: Nenhuma dependência externa.

## 3. Camada Infrastructure
- **Ação**: Implementar o mapeamento Fluent API e o Repositório (Dapper ou EF Core).
- **Regra**: Sem migrations automáticas.

## 4. Camada Application
- **Ação**: Criar DTOs (`record`), Service (Use Case) e Mapeamentos (AutoMapper).
- **Regra**: Validação via `FluentValidation`.

## 5. Camada API
- **Ação**: Criar o Controller e registrar as dependências (Injeção de Dependência).
- **Regra**: Documentação via **Scalar** e Logging estruturado via **Serilog**.

## 6. API paralela AI First
- **Ação**: Sempre que criar ou atualizar uma API funcional, criar também endpoints/contratos paralelos pensando em consumo por IA/agentes.
- **Objetivo**: A mesma funcionalidade solicitada deve ser acessível por humanos/sistemas tradicionais e por agentes AI First, sem duplicar regra de negócio.
- **Regra de arquitetura**: Reutilizar os mesmos serviços/use cases da camada Application; não criar lógica paralela fora do domínio.
- **Contratos AI First**:
  - DTOs explícitos e estáveis para entrada/saída.
  - Respostas estruturadas em JSON, sem depender de HTML/texto livre.
  - Campos de status, mensagens de validação, correlation id e erros padronizados.
  - Filtros, paginação, ordenação e campos de busca quando houver listagem.
  - Endpoint ou seção de metadata quando útil para agentes entenderem capacidades, parâmetros, limites e exemplos.
- **Naming**: Preferir uma convenção clara do projeto para separar endpoints AI First, por exemplo rota `/ai/...`, tag Scalar/OpenAPI `AI First`, ou controller específico, mantendo compatibilidade com o padrão existente.
- **Documentação**: Documentar no Scalar/OpenAPI e em `project_knowledge/backend-endpoints.md` quais endpoints atendem consumo AI First, auth requerida, payloads e exemplos.
- **Segurança**: Endpoints AI First seguem o mesmo gate de segurança da API principal; nunca publicar endpoint AI First aberto em produção sem Entra ID, API key/subscription key, Managed Identity/API Management, ou mecanismo aprovado.
- **Validação**: Incluir testes cobrindo o contrato AI First, principalmente validação de entrada, erro padronizado e autorização.

## 7. Segurança Obrigatória para Deploy Azure
- **Ação**: Antes de considerar a API pronta para deploy, verificar se todos os endpoints têm mecanismo de segurança.
- **Regra**: A API não pode ser publicada aberta em produção sem validação com credenciais do Microsoft Entra ID, API key/subscription key, Managed Identity entre serviços, API Management, ou outro mecanismo aprovado.
- **Bloqueio**: Se não existir segurança, não seguir para deploy Azure; perguntar ao usuário qual mecanismo deve ser implementado.
- **Documentação**: Registrar o mecanismo em `project_knowledge/backend-endpoints.md`, incluindo auth, headers/chaves esperadas sem expor segredos, e consumidores conhecidos.
