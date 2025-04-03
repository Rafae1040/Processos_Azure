# 🚀 Projeto: Redundância de Arquivos com Azure Data Factory

## 🎯 Objetivo do Projeto
Criar um pipeline automatizado no Azure Data Factory (ADF) para mover dados de um banco de dados SQL on-premises para um Azure Data Lake, salvando as informações como arquivos .TXT em camadas de armazenamento (como raw/bronze).

## Este processo inclui:
✅ Configuração do Integration Runtime para conectar ao ambiente on-premises
✅ Criação de Linked Services e Datasets
✅ Construção do Pipeline para mover os dados e convertê-los para .TXT
✅ Validação, publicação e execução do pipeline
✅ Análise de performance e boas práticas

## 🏗️ Passo 1: Configuração do Ambiente no Azure
Antes de construir o pipeline, você precisa configurar os seguintes serviços no Microsoft Azure:

1.1 Criar um Storage Account para armazenar os arquivos
Acesse o Azure Portal (https://portal.azure.com)

Pesquise por Storage Accounts e clique em Criar

Escolha um Resource Group ou crie um novo

Defina um nome para o Storage Account (exemplo: meuarmazenamento)

Escolha Região, Desempenho Standard, Replicação LRS

Clique em Review + Create e depois em Create

1.2 Criar um Azure Data Lake Storage (ADLS)
No Storage Account criado, vá para Containers

Clique em + Container e nomeie como bronze (para a camada raw)

Defina as permissões como Private e clique em Criar

1.3 Criar um Banco de Dados SQL no Azure (se necessário)
No Azure Portal, vá para SQL Databases > Criar Banco de Dados

Escolha um nome (exemplo: meudb) e associe a um SQL Server existente ou crie um novo

Defina a camada de serviço (por exemplo, Basic)

Clique em Review + Create e depois em Create

Acesse o banco pelo SQL Server Management Studio (SSMS) e crie a tabela de exemplo:

```sql
Copiar
Editar
CREATE TABLE Clientes (
    ID INT PRIMARY KEY,
    Nome VARCHAR(100),
    Email VARCHAR(100),
    DataCriacao DATETIME DEFAULT GETDATE()
);
```

## 🔗 Passo 2: Configurar o Azure Data Factory
2.1 Criar um Azure Data Factory
No Azure Portal, pesquise por Data Factory e clique em Criar

Escolha um nome (exemplo: meuADF)

Selecione um Resource Group e região

Clique em Review + Create e depois em Create

2.2 Criar um Integration Runtime para conectar ao SQL On-Premises
Se seu banco de dados está on-premises, você precisa configurar um Self-Hosted Integration Runtime:

No ADF, vá para Manage > Integration Runtimes

Clique em + New > Escolha Self-Hosted

Instale o runtime no servidor on-premises e conecte ao Azure

## 🔄 Passo 3: Criar os Linked Services
Linked Services são as conexões entre o ADF e os serviços externos.

3.1 Criar um Linked Service para o SQL Server On-Premises
No ADF, vá para Manage > Linked Services

Clique em + New e escolha Azure SQL Database ou SQL Server

Se for on-premises, selecione o Integration Runtime criado

Insira os detalhes do banco (Server Name, Database Name, User, Password)

Teste a conexão e clique em Create

3.2 Criar um Linked Service para o Azure Data Lake
Vá em Linked Services > + New

Escolha Azure Data Lake Storage Gen2

Insira os detalhes do Storage Account criado

Teste a conexão e clique em Create

## 📂 Passo 4: Criar os Datasets
Datasets representam as tabelas (origem) e os arquivos (destino).

4.1 Criar um Dataset para o SQL Server
No ADF, vá para Author > Datasets > + New Dataset

Escolha Azure SQL Database (ou SQL Server)

Selecione o Linked Service do SQL criado

Escolha a tabela Clientes e clique em Create

4.2 Criar um Dataset para o Azure Data Lake (arquivos .TXT)
Em Datasets, clique em + New Dataset

Escolha Azure Data Lake Storage Gen2

Escolha o formato DelimitedText (CSV/TXT)

Selecione o Linked Service do ADLS

Configure o caminho do container (/bronze/)

Marque First row as header e clique em Create

## 🔀 Passo 5: Criar o Pipeline no Azure Data Factory
5.1 Criar um Pipeline de Cópia de Dados
No ADF, vá para Author > Pipelines > + New Pipeline

Arraste a atividade Copy Data para o canvas

Configure:

Source: Escolha o Dataset do SQL Server

Sink: Escolha o Dataset do Azure Data Lake

File Name: Defina um nome dinâmico, como @concat('Clientes_', formatDateTime(utcNow(), 'yyyyMMddHHmmss'), '.txt')

Clique em Debug para testar

5.2 Publicar e Executar o Pipeline
Clique em Publish All para salvar as configurações

Vá para Trigger > New/Now para rodar manualmente

## 📊 Passo 6: Monitorar e Analisar a Performance
Vá para Monitor no ADF

Veja a execução do pipeline e cheque logs de erro (se houver)

Ajuste a Performance configurando Batch Size e Parallelism

## 🚀 Conclusão
Você configurou um pipeline de redundância de arquivos no Azure, movendo dados do SQL Server on-premises para um Azure Data Lake, convertendo-os em arquivos .TXT organizados na camada raw/bronze.

📢 Próximos Passos:
🔹 Melhorar a performance usando Data Flow
🔹 Criar uma automação com triggers (ex: execução diária)
🔹 Implementar segurança e auditoria
