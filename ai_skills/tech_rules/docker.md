# 🐳 Docker Patterns (Padrões de Containerização)

> **Regra Base**: Se o usuário optar por Docker, o ambiente de execução é **sempre Linux**.
> A IA **DEVE** perguntar ao usuário se deseja Docker antes de configurar qualquer ambiente.

---

## ⚙️ Pergunta Obrigatória

> "O projeto deve rodar em Docker?"
> - **[S] Sim** → Seguir este documento integralmente.
> - **[N] Não** → Prosseguir sem containerização.

---

## 🐧 Base: Sempre Linux

- Imagem base obrigatória: distribuições Linux oficiais e enxutas.
- **Nunca** usar imagens Windows (`mcr.microsoft.com/windows/*`).
- Preferir imagens `alpine` ou `slim` para menor footprint.

| Projeto     | Imagem Base Recomendada                        |
| ----------- | ---------------------------------------------- |
| .NET 8 API  | `mcr.microsoft.com/dotnet/aspnet:8.0-alpine`   |
| Vue.js      | `node:20-alpine` (build) + `nginx:alpine` (serve) |
| React       | `node:20-alpine` (build) + `nginx:alpine` (serve) |
| SQL Scripts | Não containerizar — executar via pipeline      |

---

## 📄 Dockerfile Padrão — Backend (.NET 8)

```dockerfile
# Build stage
FROM mcr.microsoft.com/dotnet/sdk:8.0-alpine AS build
WORKDIR /src
COPY . .
RUN dotnet restore && dotnet publish -c Release -o /app/publish

# Runtime stage
FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine AS runtime
WORKDIR /app
COPY --from=build /app/publish .
EXPOSE 8080
ENTRYPOINT ["dotnet", "<NomeProjeto>.API.dll"]
```

---

## 📄 Dockerfile Padrão — Frontend (Vue.js ou React)

```dockerfile
# Build stage
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Serve stage
FROM nginx:alpine AS runtime
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### nginx.conf para SPA (Vue Router / React Router)
```nginx
server {
    listen 80;
    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    gzip on;
    gzip_types text/plain text/css application/json application/javascript;
}
```

---

## 🐙 docker-compose.yml Padrão (Full-Stack)

```yaml
version: "3.9"

services:
  backend:
    build:
      context: ./<NOME_REPOSITORIO_BACKEND>
      dockerfile: Dockerfile
    ports:
      - "5000:8080"
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
    networks:
      - leste-net

  frontend:
    build:
      context: ./<NOME_REPOSITORIO_FRONTEND>
      dockerfile: Dockerfile
    ports:
      - "3000:80"
    depends_on:
      - backend
    networks:
      - leste-net

networks:
  leste-net:
    driver: bridge
```

---

## ✅ Regras Mandatórias

1. **Linux obrigatório** — nenhuma imagem Windows permitida.
2. **Multi-stage build** — separar build de runtime para imagens menores.
3. **Nenhum segredo no Dockerfile** — usar variáveis de ambiente ou Azure Key Vault.
4. **`.dockerignore`** obrigatório: excluir `node_modules/`, `bin/`, `obj/`, `.git/`.
5. **Health check**: todo container de API deve ter `HEALTHCHECK` definido.

```dockerfile
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD wget -qO- http://localhost:8080/health || exit 1
```
