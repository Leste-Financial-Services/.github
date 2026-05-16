---
name: leste-frontend-skill
description: Domain knowledge and workflows for Leste Frontend projects based on Vue.js 3, Quasar Framework 2, and DevExtreme. Use when working with lbos-frontend, leste-framework-vue, or any Leste Vue/Quasar application.
---
# Leste Frontend Skill
This skill provides specialized knowledge for creating and maintaining Leste's frontend applications.

## 🚀 Tech Stack
- **Vue 3**: Composition API com `<script setup>`.
- **Quasar Framework 2**: UI Components, Plugins (Notify, Loading), e CLI.
- **DevExtreme v23.2**: Grids complexos e Gráficos financeiros.
- **Pinia**: Gerenciamento de estado global.
- **Vite**: Build system de alta performance.
- **TypeScript**: Tipagem obrigatória em toda a base de código.

## 🏗️ Project Architecture
- `src/components/`: Componentes base e reutilizáveis (Dumb).
- `src/pages/`: Componentes de visualização de alto nível (Smart).
- `src/stores/`: Lógica de estado e persistência local.
- `src/services/`: Camada de API (Axios).
- `src/boot/`: Configurações de inicialização do Quasar.
- `src/models/`: Interfaces e Types para contratos de dados.

## 🎨 Design Patterns & Standards
- **Component First**: Priorize o uso de componentes Quasar e classes utilitárias (ex: `q-pa-md`, `q-mt-sm`).
- **Leste Visual Identity**: Cores e tipografia definidas no `src/css/quasar.variables.scss`.
- **Reactivity**: Prefira `ref` para valores simples e `reactive` para objetos complexos. Use `computed` para lógica derivada.
- **Communication**: Use `defineProps` e `defineEmits` para fluxo de dados vertical.

## 🔧 Workflow de Criação
1. Defina o contrato de dados no `models/`.
2. Implemente o serviço de comunicação em `services/`.
3. Crie a store em `stores/` se necessário.
4. Desenvolva os componentes de UI em `components/`.
5. Monte a página final em `pages/` e configure o `routes.js`.

