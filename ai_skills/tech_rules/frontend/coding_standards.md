# 🛠️ Coding Standards (Pacotes & Padrões Frontend)

Define a lista oficial de pacotes NPM e padrões de codificação para Vue.js.

## 1. Core Framework & UI
- **Vue 3**: Framework base (Composition API).
- **Quasar Framework 2**: Biblioteca de componentes UI e utilitários.
- **DevExtreme (devextreme-vue)**: Componentes complexos (Grids, Charts) v23.2+.
- **Vite**: Build tool e dev server.

## 2. State & Routing
- **Pinia**: Gerenciamento de estado global.
- **Vue Router**: Roteamento da aplicação.

## 3. Communication & Utilities
- **Axios**: Cliente HTTP para chamadas à API.
- **date-fns / Day.js**: Manipulação de datas.
- **Lodash**: Utilitários de manipulação de dados.
- **Leste Framework Vue**: Biblioteca interna de componentes e padrões Leste.

## 4. TypeScript & Linting
- **TypeScript**: Tipagem estática obrigatória.
- **ESLint**: Linter com configurações Vue 3 / Standard.
- **Prettier**: Formatação de código.

## 5. Práticas Mandatórias
- **Reatividade**: Use `ref()` para primitivos e `reactive()` para objetos complexos quando apropriado.
- **Computed Props**: Use `computed` para derivar estado, evitando lógica pesada no template.
- **Emits**: Defina explicitamente os eventos emitidos usando `defineEmits`.
- **Props**: Use `defineProps` com tipagem TypeScript rigorosa.

