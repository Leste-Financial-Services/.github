# 🚀 Create Frontend Workflow (Criação de Features Vue.js)

Define o passo a passo para implementar novas funcionalidades seguindo o padrão da Leste.

## 1. Planejamento de Componentização
- **Ação**: Identificar quais partes da UI podem ser componentes reaproveitáveis (Dumb) e quais são específicas da página (Smart).
- **Regra**: Verificar se já existem componentes similares no Quasar ou DevExtreme.

## 2. Camada de Dados (TypeScript / Models)
- **Ação**: Definir as interfaces TypeScript para os dados que virão da API.
- **Regra**: Seguir exatamente o contrato definido no backend (DTOs).

## 3. Camada de Comunicação (Services)
- **Ação**: Criar um arquivo de serviço (ex: `userService.ts`) usando `axios`.
- **Regra**: Tratar erros de forma global ou específica e tipar o retorno das chamadas.

## 4. Gerenciamento de Estado (Pinia Stores)
- **Ação**: Criar a Store se o estado precisar ser compartilhado entre múltiplos componentes.
- **Regra**: Manter a store focada em um único domínio de negócio.

## 5. Implementação da UI (Vue / Quasar)
- **Ação**: Criar a página (`.vue`) em `src/pages/` e registrar a rota.
- **Regra**: Usar Composition API (`<script setup>`) e componentes Quasar para layout e interação.

## 6. Autenticação e Segurança
- **Ação**: Verificar se a tela/frontend possui camada de autenticação antes do deploy.
- **Regra**: Se for um site aberto, confirmar explicitamente com o usuário que ele quer manter aberto.
- **Pergunta obrigatória**: Se o usuário quiser autenticação, perguntar se ele quer usar a autenticação de algum outro frontend como espelho.
- **Reuso**: Quando autorizado, consultar apenas knowledge `.md` relevante (`project_knowledge/frontend-endpoints.md`, `overview.md`, `reuse-inventory.md`) de frontends similares antes de implementar.
- **Documentação**: Atualizar `project_knowledge/frontend-endpoints.md` com auth, headers, base URLs, environment variables e requisitos de chamada.

## 7. Validação & Testes
- **Ação**: Validar a responsividade e realizar testes unitários da lógica contida na Store/Service.
- **Regra**: O build não deve conter warnings de lint ou tipagem.
