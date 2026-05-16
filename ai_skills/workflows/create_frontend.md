# 🚀 Create Frontend Workflow (Criação de Features Frontend)

Define o passo a passo para implementar novas funcionalidades seguindo o padrão da Leste.

## 0. Perguntas Obrigatórias Antes de Iniciar

Antes de qualquer implementação, a IA **DEVE** perguntar ao usuário:

### 0.1 — Framework Frontend
> "Qual framework deseja utilizar no frontend?"
> - **[1] Vue.js 3** (padrão Leste — Quasar + Pinia + Vite)
> - **[2] React** (React 18 + Zustand/Context + Vite)

Seguir os padrões correspondentes em `ai_skills/tech_rules/frontend/coding_standards.md`.

### 0.2 — Ambiente de Execução (Docker)
> "O projeto deve rodar em Docker?"
> - **[S] Sim** → Configurar container Linux (ver `ai_skills/tech_rules/docker.md`)
> - **[N] Não** → Prosseguir sem containerização

---

## 1. Planejamento de Componentização
- **Ação**: Identificar quais partes da UI podem ser componentes reaproveitáveis (Dumb) e quais são específicas da página (Smart).
- **Regra**: Verificar se já existem componentes similares na biblioteca escolhida (Quasar/DevExtreme para Vue; shadcn/ui ou MUI para React).

## 2. Responsividade & Mobile-First (MANDATÓRIO)
- **Ação**: Toda feature de UI deve ser projetada e implementada com abordagem **Mobile-First**.
- **Regras**:
  - Usar breakpoints progressivos: `xs` (< 600px) → `sm` → `md` → `lg` → `xl`.
  - Testar obrigatoriamente nos breakpoints: **360px** (mobile), **768px** (tablet), **1280px** (desktop).
  - Nunca usar valores fixos de largura (`width: 800px`). Preferir `%`, `vw`, `max-width`, `flex`, `grid`.
  - Menus de navegação devem ter versão colapsada para mobile (hamburguer/bottom nav).
  - Inputs, botões e áreas clicáveis devem ter altura mínima de **44px** (toque confortável).
  - Imagens e assets: usar `srcset` ou equivalente para otimização por dispositivo.

## 3. Camada de Dados (TypeScript / Models)
- **Ação**: Definir as interfaces TypeScript para os dados que virão da API.
- **Regra**: Seguir exatamente o contrato definido no backend (DTOs).

## 4. Camada de Comunicação (Services)
- **Ação**: Criar um arquivo de serviço (ex: `userService.ts`) usando `axios`.
- **Regra**: Tratar erros de forma global ou específica e tipar o retorno das chamadas.

## 5. Gerenciamento de Estado
- **Vue.js**: Criar Store Pinia focada em um único domínio de negócio.
- **React**: Usar Zustand ou Context API para estado compartilhado.

## 6. Implementação da UI
- **Vue.js**: Criar a página (`.vue`) em `src/pages/` com `<script setup>` + Composition API.
- **React**: Criar a página (`.tsx`) em `src/pages/` com hooks funcionais.
- **Ambos**: Registrar a rota, garantir layout responsivo conforme Passo 2.

## 7. Validação & Testes
- **Ação**: Validar responsividade em todos os breakpoints e realizar testes unitários da lógica de Store/Service.
- **Regra**: O build não deve conter warnings de lint ou tipagem. Checar no mobile real ou DevTools (Chrome/Firefox responsive mode).
