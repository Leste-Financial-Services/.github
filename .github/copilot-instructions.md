# 🛡️ Leste Financial Services — GitHub Copilot Instructions

Estas instruções se aplicam a **todos os repositórios** da organização **Leste-Financial-Services**.
Ao trabalhar em qualquer projeto, o Copilot **deve seguir obrigatoriamente** estas diretrizes.

---

## 🔴 ETAPA ZERO — Obrigatória para TODA Solicitação

Antes de qualquer ação de código:

1. **Criar Task no Azure DevOps** (`https://dev.azure.com/LesteDevOps`) para o(s) projeto(s) afetado(s).
2. **Identificar impacto**: Backend, Frontend ou Ambos.
3. **Vincular Tasks** entre projetos quando ambos forem impactados.
4. **Aguardar confirmação** antes de iniciar qualquer código.

> ❌ SEM TASK NO AZURE DEVOPS = SEM EXECUÇÃO

---

## 🏗️ Stack Padrão

### Backend
| Campo | Valor |
|-------|-------|
| Linguagem | `C#` |
| Framework | `ASP.NET Core` |
| Versão | `.NET 8` |
| ORM | `Entity Framework Core 8` (sem migrations) + `Dapper` |
| Arquitetura | `DDD em 4 camadas` |

### Frontend
| Campo | Valor |
|-------|-------|
| Framework | `Vue.js 3.5` |
| Build | `Vite 5` |
| State | `Pinia` |
| UI | `Quasar 2` + `DevExtreme v23.2+` |
| HTTP | `Axios` |
| Tipagem | `TypeScript` (obrigatório) |

---

## 🏗️ Arquitetura Backend — 4 Camadas DDD

```
{ProjectName}.Domain         → Entities, Interfaces de Repositório (zero dependências externas)
{ProjectName}.Application    → DTOs (records), Services/UseCases, AutoMapper, FluentValidation
{ProjectName}.Infrastructure → EF Core / Dapper, Fluent API, Refit, implementações
{ProjectName}.API            → Controllers, DI, Middleware, Scalar
```

### Nomenclatura C#
- `PascalCase` → Classes, Métodos, Propriedades
- `camelCase` → Variáveis, Parâmetros
- `_camelCase` → Campos privados
- Interfaces com prefixo `I` (ex: `IUserRepository`)
- DTOs exclusivamente como `record`

---

## 🏗️ Arquitetura Frontend — Vue.js 3

```
src/components/  → Dumb Components (reutilizáveis, sem lógica de negócio)
src/pages/       → Smart Components (gerenciam estado e chamam stores)
src/stores/      → Pinia (organizado por domínio de negócio)
src/services/    → Axios services por domínio
src/models/      → Interfaces TypeScript dos contratos de dados
src/boot/        → Inicialização Quasar e plugins globais
```

### Padrões Vue.js
- Usar `<script setup>` (Composition API) **sempre**
- `ref()` para primitivos, `reactive()` para objetos complexos
- `computed` para derivar estado (nunca lógica pesada no template)
- `defineProps` com TypeScript rigoroso
- `defineEmits` explícito

---

## 📦 Pacotes Oficiais

### Backend (NuGet)
- `Scalar.AspNetCore` — documentação de API
- `Serilog.AspNetCore` — logging estruturado
- `AutoMapper` — mapeamento entidade ↔ DTO
- `FluentValidation` — validação de regras
- `Microsoft.EntityFrameworkCore.SqlServer` — EF Core 8
- `Dapper` — queries de alta performance
- `Refit` — cliente HTTP tipado
- `Microsoft.ApplicationInsights.AspNetCore` — monitoramento Azure

### Frontend (NPM)
- `vue@3.5`, `vite@5`, `pinia`, `vue-router`
- `quasar@2`, `devextreme-vue@23.2+`
- `axios`, `date-fns`, `lodash`
- `typescript`, `eslint`, `prettier`

---

## 🔄 Workflow — Criar Nova API

1. **SQL First**: script em `DatabaseScripts/YYYY-MM-DD_demand/`
2. **Domain**: Entity + `I{Entity}Repository` (zero deps externas)
3. **Infrastructure**: Fluent API mapping + Repository (Dapper ou EF Core)
4. **Application**: DTOs (`record`) + Service + AutoMapper + FluentValidation
5. **API**: Controller + DI + Scalar + Serilog
6. **AI First**: endpoint `/ai/...` paralelo com JSON estruturado, paginação, metadata
7. **Segurança**: `[Authorize]` + Entra ID obrigatório antes do deploy

---

## 🔄 Workflow — Criar Nova Feature Frontend

1. **Componentização**: identificar Dumb vs Smart Components
2. **Models**: interfaces TypeScript alinhadas com DTOs do backend
3. **Service**: `axios` tipado com tratamento de erro
4. **Store**: Pinia por domínio (apenas se estado compartilhado)
5. **UI**: página em `src/pages/`, rota registrada, Quasar para layout
6. **Auth**: verificar autenticação antes do deploy; perguntar se quiser espelhar outro frontend
7. **Testes**: build sem warnings de lint ou tipagem

---

## 🚫 PROIBIDO (Nunca fazer)

| Proibição | Motivo |
|-----------|--------|
| `SELECT *` em SQL | Performance e segurança |
| `Add-Migration` / `Update-Database` | Controle manual de schema |
| Swagger/Swashbuckle | Usar Scalar (`/scalar/v1`) |
| Expor Entidades de Domínio na API | Sempre usar DTOs |
| Secrets em código ou arquivos | Usar Azure Key Vault |
| Concatenar strings em SQL | Risco de SQL Injection |

---

## ✅ OBRIGATÓRIO (Sempre fazer)

| Obrigação |
|-----------|
| Testes unitários com xUnit + Moq/NSubstitute + FluentAssertions |
| FluentValidation em toda entrada de dados via API |
| Auditoria: `UserId`, `Timestamp`, `Acao`, `EntidadeAlterada` via Serilog |
| Documentação de endpoint no Scalar com sumários e HTTP codes |
| Logging com templates estruturados: `Log.Information("User {UserId}", userId)` |

---

## 🔐 Segurança

- **Secrets**: Azure Key Vault obrigatório — nunca em `appsettings.json` ou código
- **Auth**: Azure Entra ID (AD) para autenticação; Claims-Based para autorização
- **Managed Identity**: entre recursos Azure (sem credenciais hardcoded)
- **Endpoints**: `[Authorize]` em todos os endpoints de produção
- **SQL**: parâmetros via Dapper/EF Core — nunca concatenação

---

## 📋 Convenções de Código

| Convenção | Valor |
|-----------|-------|
| Idioma do código | `Inglês` |
| Idioma dos comentários | `Português quando necessário` |
| Backend indentação | `4 espaços`, estilo Allman |
| Frontend indentação | `2 espaços` |
| Nomes de branches | `task-{ID}/descricao` ou `bug-{ID}/descricao` |

---

## 🗄️ Azure DevOps

- **Org**: `https://dev.azure.com/LesteDevOps`
- **Backend repo**: `ITProjects/Sentinel.Backend`
- **Frontend repo**: `ITProjects/Sentinel.Frontend`
- **Padrão de branch**: `task-{ID}/descricao-da-task`
- **Vincular Tasks**: usar `az devops work-item relation add` quando ambos os projetos são impactados
