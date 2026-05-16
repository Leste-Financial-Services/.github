# 🏗️ Architecture (Arquitetura & Padrões Frontend)

Define como o código Vue.js deve ser estruturado para garantir escalabilidade e manutenibilidade.

## 1. Estrutura de Camadas (Vue 3 + Vite)
As aplicações devem ser organizadas em camadas desacopladas:
- **`src/components/`**: Componentes reutilizáveis (Dumb Components). Devem ser agnósticos à lógica de negócio sempre que possível.
- **`src/pages/`**: Componentes de página (Smart Components). Gerenciam o estado da página e interagem com as Stores.
- **`src/stores/`**: Gerenciamento de estado global (Pinia). Organizado por domínios de negócio.
- **`src/services/`**: Camada de comunicação com APIs. Uso exclusivo de `axios` ou `fetch` para chamadas externas.
- **`src/boot/`**: Inicialização do Quasar e Plugins (Configurações globais).
- **`src/models/`**: Definições de tipos TypeScript (Interfaces/Types) para contratos de dados.

## 2. Padrões de Componentização
- **Single Responsibility**: Um componente deve fazer apenas uma coisa.
- **Composition API**: Uso obrigatório de `<script setup>`.
- **Props in, Events out**: Comunicação clara entre componentes pai e filho.

## 3. Nomenclatura (Naming)
- **Componentes**: PascalCase (ex: `BaseButton.vue`, `UserTable.vue`).
- **Arquivos JS/TS**: camelCase (ex: `userStore.ts`, `authService.ts`).
- **CSS/Kebab-case**: Usado para classes CSS e seletores.

## 4. Identação e Estilo
- **2 espaços** para identação (Padrão Vue/JS).
- **Semicolons**: Opcional, mas deve ser consistente no projeto.
- **Quotes**: Single quotes para strings JS, Double quotes para HTML attributes.

