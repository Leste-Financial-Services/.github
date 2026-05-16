# 📄 Documentation Rules (Regras de Documentação)

Define o padrão de documentação para código e APIs.

## 1. Código Fonte (XML Documentation)
- **Obrigatório**: Utilize `///` em interfaces, métodos públicos e classes de negócio.
- **Mandatório**: Descrever parâmetros, retornos e exceções (`<param>`, `<returns>`, `<exception>`).

## 2. APIs (Scalar)
- **Scalar** (`/scalar/v1`) como única documentação de API permitida.
- **Sumários**: Enriqueça endpoints com descrições claras e códigos HTTP adequados.

## 3. Banco de Dados (SQL Scripts)
- **Scripts**: Numerados e datados em `scripts/sql/`.
- **Cabeçalho**: Comentários no topo do script descrevendo a Task ID e o objetivo comercial.

## 4. Repositórios (Git)
- **Commits**: Mensagens estruturadas incluindo o ID da Task.
- **README**: Arquivos README devem ser atualizados em cada projeto/repositório.
