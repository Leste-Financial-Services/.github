# 🛠️ Stack Preferences — Configuração de Linguagens

## Como Usar
Antes de iniciar qualquer implementação, a IA **DEVE** verificar este arquivo para saber quais linguagens/frameworks usar.

Se este arquivo não tiver sido preenchido, a IA **DEVE** perguntar ao usuário:
1. Qual linguagem/framework será usado no **Backend**?
2. Qual linguagem/framework será usado no **Frontend**?

Após a resposta, a IA deve atualizar este arquivo automaticamente.

---

## 🏠 Backend

| Campo | Valor |
|-------|-------|
| **Linguagem** | `C#` |
| **Framework** | `ASP.NET Core` |
| **Versão** | `.NET 8` |
| **ORM/Data Access** | `Entity Framework Core para consultas em banco real; sem migrations no backend` |
| **Padrão Arquitetural** | `Arquitetura em camadas` |

### Linguagens Suportadas (exemplos)
- C# (.NET 6/7/8, ASP.NET Core, Entity Framework)
- Python (Django, Flask, FastAPI, SQLAlchemy)
- Java (Spring Boot, Hibernate, Jakarta EE)
- C++ (Boost, Qt, gRPC)
- Go (Gin, Echo, GORM)
- Node.js/TypeScript (Express, NestJS, Prisma)
- Rust (Actix, Axum, Diesel)

---

## 🎨 Frontend

| Campo | Valor |
|-------|-------|
| **Linguagem/Framework** | `vue.js 3 Quazar` |
| **Versão** | `Vue 3.5 + Vite 5` |
| **State Management** | `Pinia` |
| **UI Library** | `CSS customizado / componentes próprios` |
| **Build Tool** | `Vite` |

### Frameworks Suportados (exemplos)
- Vue.js (2.x, 3.x, Vuex/Pinia, Vuetify/Quasar)
- React (Next.js, Redux/Zustand, Material UI/Chakra)
- Angular (RxJS, NgRx, Angular Material)
- Svelte/SvelteKit
- Python (Streamlit, Dash, Gradio)
- Blazor (C#)

---

## 📋 Convenções Adicionais

| Convenção | Valor |
|-----------|-------|
| **Idioma do Código** | `Inglês` |
| **Idioma dos Comentários** | `Português quando necessário` |
| **Padrão de Nomes** | `PascalCase para tipos/membros públicos; camelCase para variáveis/parâmetros` |
| **Testes** | `A definir; validar inicialmente com build da solução` |

---

## 🔄 Histórico de Alterações
<!-- A IA deve registrar aqui cada alteração feita neste arquivo -->
- 2026-05-01: Configurado backend como C# / ASP.NET Core / .NET 8 com arquitetura em camadas para a Issue #4925.
- 2026-05-02: Configurado Entity Framework Core como data access para fundos na Issue #4951, mantendo a restrição de não criar migrations.
- 2026-05-02: Configurado frontend como Vue.js 3 + Pinia + Vite a partir do stack real de Sentinel.Frontend na Issue #4960.

- **[2026-05-04 15:36]** Setup automático: Backend=C#, Frontend=Vue.js

- **[2026-05-07 19:37]** Setup automático: Backend=C#, Frontend=Vue.js

- **[2026-05-09 12:57]** Setup automático: Backend=C#, Frontend=Vue.js 3 Quasar

- **[2026-05-09 23:19]** Setup automático: Backend=, Frontend=vue.js 3 Quazar
