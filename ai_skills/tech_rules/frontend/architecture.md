# 🏗️ Architecture (Arquitetura & Padrões Frontend)

Define como o código Frontend deve ser estruturado para garantir escalabilidade e manutenibilidade.
Aplicável a **Vue.js 3** e **React 18** — framework escolhido pelo usuário.

## 1. Estrutura de Camadas (Vue 3 + Vite)
As aplicações devem ser organizadas em camadas desacopladas:
- **`src/components/`**: Componentes reutilizáveis (Dumb Components). Devem ser agnósticos à lógica de negócio sempre que possível.
- **`src/pages/`**: Componentes de página (Smart Components). Gerenciam o estado da página e interagem com as Stores.
- **`src/stores/`**: Gerenciamento de estado global (Pinia). Organizado por domínios de negócio.
- **`src/services/`**: Camada de comunicação com APIs. Uso exclusivo de `axios` para chamadas externas.
- **`src/boot/`**: Inicialização do Quasar e Plugins (Configurações globais).
- **`src/models/`**: Definições de tipos TypeScript (Interfaces/Types) para contratos de dados.

## 2. Estrutura de Camadas (React 18 + Vite)
- **`src/components/`**: Componentes reutilizáveis sem lógica de negócio.
- **`src/pages/`**: Componentes de página, composição de componentes e lógica local.
- **`src/store/`**: Stores Zustand, organizadas por domínio.
- **`src/services/`**: Chamadas à API via Axios ou React Query.
- **`src/hooks/`**: Custom hooks reutilizáveis (`useXxx`).
- **`src/models/`**: Interfaces e Types TypeScript.

## 3. Responsividade — Estrutura Obrigatória (Mobile-First)
- Toda camada de UI deve ser responsiva por padrão.
- **Vue.js**: Usar o sistema de grid do Quasar (`q-col`, breakpoints `xs/sm/md/lg/xl`).
- **React + Tailwind**: Usar classes responsivas nativas (`sm:`, `md:`, `lg:`).
- Nenhuma página ou componente deve ser entregue sem validação nos breakpoints: **360px / 768px / 1280px**.

## 4. Padrões de Componentização
- **Single Responsibility**: Um componente deve fazer apenas uma coisa.
- **Composition API / Hooks**: Obrigatório para ambos os frameworks.
- **Props in, Events out**: Comunicação clara entre componentes pai e filho.

## 5. Nomenclatura (Naming)
- **Componentes**: PascalCase (ex: `BaseButton.vue`, `UserTable.tsx`).
- **Arquivos JS/TS**: camelCase (ex: `userStore.ts`, `authService.ts`).
- **CSS/Tailwind**: Kebab-case para classes customizadas.

## 6. Identação e Estilo
- **2 espaços** para identação (padrão JS/TS).
- **Semicolons**: Consistente por projeto.
- **Quotes**: Single quotes para strings JS, Double quotes para HTML attributes.

