# 🛠️ Coding Standards (Pacotes & Padrões Frontend)

Define os padrões de codificação para projetos Frontend. O framework é escolhido pelo usuário no início de cada projeto.

---

## ⚙️ Escolha de Framework (Pergunta Obrigatória)

A IA **DEVE** perguntar ao usuário antes de iniciar qualquer implementação frontend:
> "Qual framework deseja utilizar? **[1] Vue.js 3** (padrão Leste) ou **[2] React 18**?"

---

## 📱 Responsividade Mobile-First (MANDATÓRIO para ambos os frameworks)

- Toda UI deve ser projetada com abordagem **Mobile-First**.
- Breakpoints obrigatórios: `360px` (mobile) · `768px` (tablet) · `1280px` (desktop).
- Proibido usar larguras fixas (`width: 800px`). Usar `%`, `vw`, `max-width`, `flex`, `grid`.
- Elementos interativos (botões, inputs): altura mínima de **44px**.
- Menus: versão colapsada para mobile (hamburguer ou bottom navigation).
- Imagens: `srcset` ou carregamento otimizado por viewport.

---

## [VUE.JS 3] Padrão Leste

### Core Framework & UI
- **Vue 3**: Framework base (Composition API obrigatória).
- **Quasar Framework 2**: Componentes UI, responsividade e utilitários.
- **DevExtreme (devextreme-vue)**: Grids, Charts complexos — v23.2+.
- **Vite**: Build tool e dev server.

### State & Routing
- **Pinia**: Gerenciamento de estado global.
- **Vue Router**: Roteamento da aplicação.

### Communication & Utilities
- **Axios**: Cliente HTTP.
- **date-fns / Day.js**: Datas.
- **Lodash**: Utilitários.
- **Leste Framework Vue**: Biblioteca interna.

### TypeScript & Linting
- **TypeScript**: Obrigatório.
- **ESLint** + **Prettier**: Padrão Vue 3 / Standard.

### Práticas Mandatórias
- `ref()` para primitivos, `reactive()` para objetos complexos.
- `computed` para estado derivado.
- `defineEmits` e `defineProps` com tipagem TypeScript rigorosa.
- `<script setup>` obrigatório.

---

## [REACT 18] Alternativa

### Core Framework & UI
- **React 18**: Framework base (hooks funcionais obrigatórios).
- **Vite**: Build tool e dev server.
- **shadcn/ui** ou **MUI v5**: Biblioteca de componentes UI.
- **TailwindCSS**: Estilização utilitária (mobile-first nativo).

### State & Routing
- **Zustand**: Gerenciamento de estado global leve.
- **React Router v6**: Roteamento.

### Communication & Utilities
- **Axios** ou **TanStack Query (React Query)**: Chamadas à API com cache.
- **date-fns**: Datas.
- **TypeScript**: Obrigatório.

### TypeScript & Linting
- **ESLint** + **Prettier**: Configuração React/TypeScript.

### Práticas Mandatórias
- Componentes funcionais com hooks — proibido Class Components.
- Custom hooks para lógica reutilizável (`useXxx`).
- Props tipadas com `interface` ou `type`.
- Estado derivado com `useMemo` / `useCallback` quando necessário.


