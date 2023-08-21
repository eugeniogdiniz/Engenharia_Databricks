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

Com o Utilitários do Databricks (Databricks Utilities) podemos fazer a manipulação dos arquivos no Databricks. Mais especificamente, com o módulo file system (fs) podemos criar diretórios, listar, copiar, mover e remover arquivos e pastas.

cp: copiar 
mv:  mover 
rm: remover 
mkdirs: criar diretório
ls: listar 

É isso mesmo! Aqui todos os comandos estão escritos da forma correta. Para acessar essa lista de comandos, basta executar a seguinte linha de código em seu notebook dbutils.fs.help().

Quando estamos explorando os datasets que possuímos em nossa base, podemos utilizar vários comandos para nos auxiliar nessa tarefa. Dois deles são essenciais para essa ação:

O dbutils.fs.ls serve para listarmos todo o conteúdo de uma pasta/diretório. Já o dbutils.fs.head permite verificar as primeiras linhas dos documentos, como CSV, MD, TXT, etc.

Exatamente! O comando dbutils.fs.ls consegue listar todo o conteúdo do diretório e o dbutils.fs.head é uma das formas de visualizar o conteúdo inicial dos arquivos.

## Para saber mais: Apache Hive

O Apache Hive é um sistema de data warehouse de código aberto usado, originalmente, para consultar e analisar grandes conjuntos de dados armazenados no Hadoop. Esse sistema foi criado pelo time de Infraestrutura de Dados do Facebook e adotado por diversos gigantes, como Yahoo, eBay e Facebook.

O Hive facilita a leitura, gravação e gerenciamento de grandes conjuntos de dados que residem em armazenamento distribuído usando SQL, projetado para trabalhar com grandes conjuntos de dados. No Apache Hive, podemos criar tabelas para armazenar dados estruturados para que mais tarde possamos processá-los.

Embora novas melhorias do projeto Hive nos permitam obter os resultados de consultas em tempo inferior a 1 segundo, ele não é projetado para processamento de transações online. Hive é melhor utilizado para tarefas tradicionais de data warehousing.

Basicamente, podemos dizer que para se trabalhar com dados em batch (lotes de dados em um intervalo de tempo específico) podemos usar o Hive. Mas quando estamos trabalhando com stream (fluxo de dados em tempo real) ou dados não estruturados temos que utilizar um dos módulos do Spark.

O Hive apresenta as seguintes características:

* Interpretação de instruções SQL para jobs MapReduce.
* Leitura de dados de arquivos estruturados e semi-estruturados no HDFS, baseando-se em metadados para simular tabelas de um banco de dados relacional.
* Não possui e nem é um SGBD (Sistema Gerenciador de Banco de Dados).
* Foi desenhado para melhor performance analisando grandes quantidades de dados que se encontram em clusters.
* É um sistema de Data Warehouse para o Apache Hadoop.

## Hive no Databricks

Assim como o HDFS do Hadoop, o Hive também tornou-se a base para serviços de plataforma em nuvem de grandes fornecedores, como o Amazon Web Services (AWS). Por exemplo, no Databricks, o Hive é usado como base para gerenciar e processar grandes conjuntos de dados. Na AWS, o Glue Catalog é baseado no Hive e é usado para gerenciar dados em data lakes. Isso demonstra a importância e a ampla utilização do Hive.

Não há bancos de dados propriamente ditos no Hive, mas sim um conjunto de diretórios, arquivos e metadados registrados. No Hive dentro do Databricks também existe o conceito de tabelas externas, nas quais os dados não "pertencem" ao Hive, ou seja, se a tabela for deletada os arquivos se manterão lá e apenas metadados serão perdidos.

### Tabela para vinho branco

No último vídeo, realizamos um procedimento com o arquivo de vinho tinto. Agora, vamos repeti-lo utilizando o arquivo de vinho branco.

Como regra, chame a tabela de white_wine. Podemos começar com uma visualização simples das primeiras linhas do arquivo CSV contendo as informações sobre os vinhos brancos.

dbutils.fs.head('/FileStore/tables/aula-databricks/vinhos/winequality-white.csv')COPIAR CÓDIGO
Baseado no que foi feito para os dados de vinho tinto, mudamos apenas o nome da tabela para white_wine e o path para '/FileStore/tables/aula-databricks/vinhos/winequality-white.csv' e criamos a nova tabela.

    %sql
    CREATE TABLE white_wine(
        fixed_acidity float,
        volatile_acidity float,
        citric_acid float,
        residual_sugar float,
        chlorides float,
        free_sulfur_dioxide int,
        total_sulfur_dioxide float,
        density float,
        pH float,
        sulphates float,
        alcohol float,
        quality float
        )
        USING CSV
            OPTIONS (
                path '/FileStore/tables/aula-databricks/vinhos/winequality-white.csv',
                header 'true',
                delimiter ';'
            )

Podemos utilizar o comando SQL SHOW TABLES apenas para verificar a criação da tabela white_wine.

    %sql
    SHOW TABLES

Por último, podemos visualizar as dez primeiras linhas da nova tabela usando o seguinte comando:

    %sql
    SELECT *
    FROM white_wine
        LIMIT 10

## Spark Jobs

Como podemos observar, mesmo utilizando o Hive vemos que na saída, logo abaixo de cada célula do notebook, aparece o label Spark Jobs. Ele pode ser expandido como mostra a figura abaixo.

O Spark tem algumas definições listadas. Dentre elas temos que um Job é:

“Uma computação paralela que consiste em várias tarefas que são geradas em resposta a uma ação do Spark (por exemplo, save, collect); você verá esse termo sendo usado nos logs do driver”.

Ou seja, sempre que uma ação que utiliza o Spark por baixo dos panos for executada, teremos como verificar o seu histórico. Ao clicar em Spark Jobs, é possível verificar que a ação ilustrada na figura acima foi um job de um estágio apenas e que ele foi completado. Por isso, tem-se entre parênteses: (1/1).

Um job em computação paralela consiste de múltiplas tarefas que são produzidas em resposta a uma ação do Spark, como: salvar, transformar, visualizar. É possível também observar todos os jobs executados pelos logs do driver. Para acessá-los, basta ir em Compute, no menu lateral esquerdo, clicar sobre o nome do seu cluster (aulas_databricks) e depois em Driver logs.

Dentro da página dos logs é possível ver o histórico de tudo o que foi ou está sendo executado em detalhes. Abaixo temos um exemplo dos logs:

Mas pode vir a pergunta: se estou executando o Hive, por que aparece Spark Jobs? Isso acontece porque o Hive se beneficia do Spark, principalmente com relação a paralelização. De forma geral, quando estamos falando de Big Data, o Hive pode armazenar os dados no HDFS. Mas quando estamos utilizando o Hive com Spark, ao invés do Hive armazenar dados no Hadoop, ele os armazena no Spark.

A razão pela qual as pessoas usam o Spark em vez do Hadoop é que ele é um banco de dados totalmente em memória. Portanto, os trabalhos do Hive serão executados muito mais rápido por lá. Isso leva as pessoas programadoras a usar um banco de dados comum, caso a empresa para a qual trabalham execute predominantemente o Spark. Também é possível escrever programas no Spark e usá-los para se conectar aos dados do Hive, ou seja, ir na direção oposta.

## conhecendo o Apache Spark

O Apache Spark é uma ferramenta unificada cujo objetivo primordial consiste em processar grandes conjuntos de dados (Big Data) de forma paralela e distribuída. Foi originalmente desenvolvido na Universidade de Berkley na Califórnia e posteriormente doado para a Fundação Apache que o mantém até os dias de hoje.

O Spark possui armazenamento em memória para cálculos computacionais intermediários, tornando-o mais rápido que o MapReduce. Além de possuir módulos internos para streaming, SQL, machine learning, processamento de grafos e ser capaz de interagir com linguagens como Scala, Python, Java e R.

O Spark foi criado baseando-se em quatro características chaves:

* Velocidade: consegue executar tudo com rapidez.
* Facilidade de uso: menores dificuldades e complicações ao ser utilizado.
* Modularidade: possui unidades funcionais individuais.
* Extensibilidade: tem a possibilidade de adicionar novas funcionalidades.

O Apache Spark tem sua arquitetura baseada no conjunto de dados distribuídos resiliente (ou RDD - Resilient Distributed Dataset). É um conjunto múltiplo de itens contendo dados apenas de leitura distribuídos sobre um conjunto de máquinas tolerantes a falhas. Assim, os RDDs funcionam como um conjunto de ferramentas para programas distribuídos que oferecem uma forma restrita de memória compartilhada distribuída.

Dentro do Apache Spark, o fluxo de trabalho é gerenciado como um gráfico acíclico dirigido (DAG). Os nós representam RDDs, enquanto as terminações representam as operações realizadas sobre eles.

O Apache Spark requer um gerenciador de cluster e um sistema de armazenamento distribuído. Para o gerenciamento de cluster, o Spark suporta standalone, Hadoop YARN, Apache Mesos ou Kubernetes.

Standalone é um cluster Spark nativo no qual é possível lançar um cluster manualmente ou usar os scripts de lançamento fornecidos pelo pacote de instalação. Também é possível executar esses daemons (programas executados nos processos em segundo plano) em uma única máquina para testes.

Para armazenamento distribuído, o Spark pode interagir com uma grande variedade de sistemas, incluindo: Alluxio, Hadoop Distributed File System (HDFS), MapR File System (MapR-FS), Cassandra, OpenStack Swift, Amazon S3, Kudu, Lustre file system, ou uma solução personalizada pode ser implementada.

O Spark também suporta um modo local pseudo-distribuído, normalmente usado apenas para fins de desenvolvimento ou teste. Nesse modo, o armazenamento distribuído não é necessário e o sistema de arquivo local pode ser usado em seu lugar. Em tal cenário, o Spark é executado em uma única máquina com um executor por núcleo de CPU.

O Driver é um processo que executa o programa principal de sua aplicação Spark e cria o SparkContext que coordena a execução dos trabalhos. Os executores (executers) são processos em execução nos nós de trabalho (worker nodes) do cluster que são responsáveis por executar as tarefas que o Driver lhes atribuiu. O gerente do cluster (como o Mesos ou YARN) é responsável pela alocação de recursos físicos para as aplicações Spark.

### Pontos de Entradas

Toda aplicação Spark precisa de um ponto de entrada que permita a integração com fontes de dados, além de realizar determinadas operações como leitura e gravação de dados. Na versão Spark 1.0 foram introduzidos três pontos de entrada:

#### SparkContext
É usado pelo Driver Process para estabelecer uma comunicação com o cluster e com os gerenciadores de recursos, a fim de coordenar e executar jobs. O SparkContext também permite o acesso às demais funcionalidades disponíveis, a saber: SQLContext e HiveContext.

#### SQLContext
Ponto de entrada para SparkSQL que é um módulo para o processamento de dados estruturados. Uma vez que o SQLContext é inicializado, o(a) usuário(a) tem a possibilidade de realizar várias operações utilizando a linguagem SQL sobre Datasets e Dataframes.

#### HiveContext
Tem todas as funcionalidades do SQLContext. Além de recursos adicionais tais como a capacidade de escrever consultas usando o analisador HiveQL e a de ler dados de tabelas Hive.

No Spark 2.0 foi introduzido um novo ponto de entrada que combina essencialmente todas as funcionalidades disponíveis nas três entradas anteriores.

#### SparkSession
Este ponto de entrada se tornou uma opção do Spark para trabalhar com RDD, DataFrame e Dataset. Ele é capaz de substituir o SQLContext e o HiveContext. O spark, que é o objeto do SparkSession, é a variável padrão disponível no spark-shell e pode ser criado usando o padrão do construtor SparkSession. No Databricks já temos uma SparkSession iniciada e que pode ser acessada pela variável spark, como veremos nos próximos vídeos.





