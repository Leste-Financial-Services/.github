# ✅ Must Have (O que é obrigatório)

O firewall lógico da IA: o que a IA **SEMPRE** deve fazer.

## 1. Testes Unitários (**xUnit**)
- Todo código gerado deve vir com testes unitários cobrindo fluxos principais e de erro.
- Use **xUnit**, **Moq** ou **NSubstitute** e **FluentAssertions**.

## 2. Validação de Entrada (**FluentValidation**)
- Toda entrada de dados via API ou Job DEVE ser validada usando a biblioteca **FluentValidation**.
- As regras de validação devem estar localizadas na camada **Application**.

## 3. Auditoria Mandatória
- Registre o `UsuarioId`, `Timestamp`, `Acao` e `EntidadeAlterada` via **Serilog**.

## 4. Documentação API (**Scalar**)
- Todo endpoint deve estar documentado com sumários claros e códigos HTTP adequados.
