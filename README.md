
# Projeto: Integração e Transformação de Dados MySQL no Azure com Power BI

## 1. Criação da Instância MySQL no Azure
- Acesse o portal do Azure.
- Crie um recurso do tipo **Azure Database for MySQL - Flexible Server**.
- Configure os parâmetros:
  - Nome do servidor: `mysql-empresa`
  - Região: `Brazil South`
  - Versão: `MySQL 8.0`
  - Autenticação: senha
  - Nome de usuário: `adminuser`
  - Senha segura definida
- Configure o grupo de recursos e habilite o acesso público com IPs confiáveis.
- Finalize a criação e aguarde a implantação.

## 2. Criação do Banco de Dados com Base no GitHub
- Clone o repositório do GitHub contendo os scripts SQL.
- Acesse o servidor MySQL via MySQL Workbench.
- Execute os scripts SQL para:
  - Criar o banco de dados `empresa_db`
  - Criar as tabelas: `employee`, `department`, `project`, `works_on`, etc.
  - Inserir os dados fornecidos.

## 3. Integração do Power BI com MySQL no Azure
- No Power BI Desktop:
  - Fonte de dados: MySQL
  - Servidor: `mysql-empresa.mysql.database.azure.com`
  - Banco: `empresa_db`
  - Autenticação com usuário e senha definidos
- Carregue as tabelas relevantes para o modelo.

## 4. Transformação dos Dados no Power BI

### Diretrizes Aplicadas:

1. **Verificação de Cabeçalhos e Tipos de Dados**
   - Corrija nomes de colunas inconsistentes.
   - Ajuste tipos: datas, inteiros, textos e valores monetários.

2. **Conversão de Valores Monetários**
   - Colunas como `salary` e `budget` convertidas para tipo **Decimal Number (double)** com precisão.

3. **Tratamento de Valores Nulos**
   - Identifique nulos em `Super_ssn` na tabela `employee`.
   - Verifique que esses registros são gerentes (não possuem superiores).
   - Mantenha esses registros com anotação.

4. **Verificação de Departamentos sem Gerente**
   - Identifique departamentos com `mgr_ssn` nulo.
   - Preencha com dados fictícios coerentes com o restante da base.

5. **Verificação de Horas em Projetos**
   - Coluna `hours` na tabela `works_on` verificada para valores inconsistentes (ex: negativos ou acima de 40).
   - Corrigidos ou removidos conforme necessário.

6. **Separação de Colunas Complexas**
   - Divida colunas como `address` em `street`, `city`, `state`.

7. **Mescla de `employee` com `department`**
   - Realize junção do tipo **Left Join** com base em `dno` (departamento do colaborador).
   - Resultado: tabela `employee_ext` com nome do departamento.

8. **Eliminação de Colunas Desnecessárias**
   - Remova colunas como `middle_initial`, `address`, `sex`, etc., que não seriam usadas no relatório.

9. **Junção com Nome dos Gerentes**
   - Realize junção da tabela `employee` com ela mesma:
     SELECT e.ssn, e.fname, e.lname, g.fname AS mgr_fname, g.lname AS mgr_lname
     FROM employee e
     LEFT JOIN employee g ON e.super_ssn = g.ssn;

10. **Mescla de Nome e Sobrenome**
    - Crie nova coluna `Nome Completo` com `fname & " " & lname`.

11. **Mescla de Nome de Departamento e Localização**
    - Nova coluna: `Departamento_Local = department_name & " - " & location`.

12. **Justificativa: Mesclar vs Atribuir**
    - Utilize **mesclar** pois a junção traz dados de outra tabela mantendo a estrutura original.
    - **Atribuir** substituiria a tabela, o que não é desejado nesse contexto.

13. **Agrupamento por Gerente**
    - Crie visualização com número de colaboradores por gerente usando `group by` no Power BI.

14. **Limpeza Final**
    - Remova colunas como `ssn`, `bdate`, `sex`, `address`, etc., que não seriam utilizadas no relatório final.
