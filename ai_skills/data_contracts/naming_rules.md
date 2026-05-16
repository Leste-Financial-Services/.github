# 🏷️ Naming Rules (Nomenclatura SQL)

Define as leis de nomenclatura para banco de dados.

## 1. Tabelas (SQL)
- **Padronização**: `snake_case` no **Singular** (ex: `user_account`, `order_item`).
- **Nomes**: Devem refletir o propósito da entidade de domínio.

## 2. Colunas (SQL)
- **Padronização**: `snake_case` (ex: `first_name`, `created_at`).

## 3. Chaves e Constraints
- **Chaves Primárias (PK)**: Sempre chamadas apenas de `id`.
- **Chaves Estrangeiras (FK)**: Padrão `referenced_table_id` (ex: `user_id`).
- **PK (Constraint)**: `pk_table_name` (ex: `pk_user_account`).
- **FK (Constraint)**: `fk_table_name_referenced_table` (ex: `fk_order_item_user`).
- **IX (Índices)**: `ix_table_name_column` (ex: `ix_user_email`).
- **UQ (Unique)**: `uq_table_name_column` (ex: `uq_user_document`).
