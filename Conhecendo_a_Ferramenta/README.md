# Estudando Databricks

## O que é DBFS?

O DBFS (Databricks File System ou sistema de arquivos do Databricks) é um sistema de arquivos distribuído montado no workspace do Databricks e disponível nos clusters. O DBFS é uma abstração do armazenamento escalável de objetos e oferece os seguintes benefícios:

Permite montar objetos de armazenamento para que você possa acessar os dados de maneira contínua sem a exigência de credenciais.
Permite que você interaja com o armazenamento de objetos usando a semântica do diretório e do arquivo ao invés de URLs de armazenamento.
Mantém arquivos no armazenamento de objetos para que não haja perda de dados depois de encerrar um cluster.
Todos(as) os(as) usuários(as) têm acesso de leitura e gravação aos objetos no armazenamento de objetos montado em DBFS, com exceção da raiz do DBFS.

Raiz do DBFS
O local de armazenamento padrão no DBFS é conhecido como a raiz DBFS, na qual vários dados são armazenados nos seguintes locais:

/FileStore: arquivos de dados importados, gráficos gerados e bibliotecas carregadas.
/databricks-datasets: conjuntos de dados públicos de exemplo.
/databricks-results: arquivos gerados ao baixar os resultados completos de uma consulta.
/databricks/init: scripts de inicialização globais e com nome de cluster (preteridos).
/user/hive/warehouse: dados e metadados de tabelas não externas do Hive.
A raiz DBFS também contém dados incluindo metadados, credenciais e determinados tipos de logs que não estão visíveis e não podem ser acessados diretamente.