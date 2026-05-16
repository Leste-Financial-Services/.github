# ⚖️ Data Privacy (Compliance & LGPD)

Define as regras de conformidade e auditoria técnica.

## 1. LGPD (Lei Geral de Proteção de Dados)
- **Privacy by Design**: Proteção de dados desde a concepção.
- **NUNCA** exponha dados sensíveis (Padrão de Segurança Pessoal) em logs ou APIs públicas.
- Anonimize dados de usuários finais se necessário.

## 2. Auditoria Técnica
- Toda feature deve registrar logs auditáveis de ações críticas (Criação, Alteração, Exclusão).
- Campos de auditoria em banco: `created_at`, `updated_at`, `created_by`, `updated_by`.

## 3. Conformidade DevOps
- Toda implementação vinculada a um ID de Task ativo em `https://dev.azure.com/LesteDevOps/IAProjects`.
