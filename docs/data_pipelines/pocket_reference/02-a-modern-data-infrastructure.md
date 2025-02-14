# Uma Infraestrutura de Dados Moderna

Antes de decidir sobre produtos e design para construir pipelines, vale a pena entender o que compõe
uma pilha de dados moderna. Como na maioria das coisas em tecnologia, não há uma única maneira
correta de projetar seu ecossistema de análise ou escolher produtos e
fornecedores. Independentemente disso, há algumas necessidades e conceitos chave que se tornaram
padrão na indústria e estabelecem o cenário para as melhores práticas na implementação de pipelines.

## Diversidade de Fontes de Dados

A maioria das organizações possui dezenas, se não centenas, de fontes de dados que alimentam seus
esforços analíticos. As fontes de dados variam em muitas dimensões abordadas nesta seção.

**Os principais componentes de uma infraestrutura de dados moderna**

- Data warehouses na nuvem e data lakes
- Ferramentas de ingestão de dados
- Diversidade de fontes de dados
- Plataformas de orquestração de workflows
- Ferramentas e frameworks de modelagem

## Propriedade do Sistema de Origem

É comum para uma equipe de análise ingerir dados de sistemas de origem que são construídos e
mantidos pela organização, bem como de ferramentas e fornecedores de terceiros. Por exemplo, uma
empresa de e-commerce pode armazenar dados de seu carrinho de compras em um banco de dados
PostgreSQL (também conhecido como Postgres) por trás de seu aplicativo web. Eles também podem usar
uma ferramenta de análise web de terceiros, como o Google Analytics, para rastrear o uso em seu
site. A combinação dessas duas fontes de dados é necessária para obter uma compreensão completa do
comportamento do cliente até a compra. Assim, um pipeline de dados que termina com uma análise de
tal comportamento começa com a ingestão de dados de ambas as fontes.

Entender a propriedade dos sistemas de origem é importante por várias razões. Primeiro, para fontes
de dados de terceiros, você provavelmente estará limitado quanto aos dados que pode acessar e como
pode acessá-los. A maioria dos fornecedores disponibiliza uma API REST, mas poucos lhe darão acesso
direto aos seus dados na forma de um banco de dados SQL. Ainda menos fornecerão muita personalização
sobre quais dados você pode acessar e em que nível de granularidade.

Sistemas construídos internamente apresentam à equipe de análise mais oportunidades de personalizar
os dados disponíveis, bem como o método de acesso. No entanto, eles também apresentam outros
desafios. Os sistemas foram construídos com consideração à ingestão de dados? Muitas vezes a
resposta é não, o que tem implicações que variam desde a ingestão colocando uma carga não
intencional no sistema até a incapacidade de carregar dados incrementalmente. Se você tiver sorte, a
equipe de engenharia que possui o sistema de origem terá tempo e disposição para trabalhar com
você, mas na realidade das restrições de recursos, você pode descobrir que não é muito diferente de
trabalhar com um fornecedor externo.

## Interface de Ingestão e Estrutura de Dados

Independentemente de quem possui os dados de origem, como você os obtém e em que forma é a primeira
coisa que um engenheiro de dados examinará ao construir uma nova ingestão de dados. Primeiro, qual
é a interface para os dados? Alguns dos mais comuns incluem os seguintes:

- Um banco de dados por trás de um aplicativo, como um banco de dados Postgres ou MySQL
- Uma camada de abstração em cima de um sistema, como uma API REST
- Uma plataforma de processamento de streams, como o Apache Kafka
- Um sistema de arquivos de rede compartilhado ou bucket de armazenamento em nuvem contendo logs,
arquivos CSV (valores separados por vírgula) e outros arquivos planos (flat files)
- Um data warehouse ou data lake
- Dados em HDFS ou banco de dados HBase

Além da interface, a estrutura dos dados variará. Aqui estão alguns exemplos comuns:

- JSON de uma API REST
- Dados bem estruturados de um banco de dados MySQL
- JSON dentro de colunas de uma tabela de banco de dados MySQL
- Dados de log semiestruturados
- CSV, formato de largura fixa (FWF) e outros formatos de arquivos planos
- JSON em arquivos planos
- Saída de stream do Kafka

Cada interface e estrutura de dados apresenta seus próprios desafios e oportunidades. Dados bem
estruturados são frequentemente mais fáceis de trabalhar, mas geralmente são estruturados no
interesse de um aplicativo ou site. Além da ingestão dos dados, etapas adicionais no pipeline
provavelmente serão necessárias para limpar e transformar em uma estrutura em que os dados sejam
reduzidos.

Dados não estruturados são comuns para alguns empreendimentos analíticos. Por exemplo, modelos de
Processamento de Linguagem Natural (Natural Language Processing - NLP) requerem grandes quantidades
de dados de texto livre para treinar e validar. Projetos de Visão Computacional (Computer
Vision - CV) requerem imagens e conteúdo de vídeo. Mesmo projetos menos assustadores, como a
extração de dados de páginas da web, têm necessidade de dados de texto livre da web, além da
marcação HTML semiestruturada de uma página da web.

## Volume de Dados

Embora engenheiros de dados e gerentes de contratação gostem de se gabar sobre conjuntos de dados
em escala de petabytes, a realidade é que a maioria das organizações valoriza conjuntos de dados
pequenos tanto quanto os grandes. Além disso, é comum ingerir e modelar pequenos e grandes conjuntos
de dados em conjunto. Embora as decisões de design em cada etapa de um pipeline devam levar o volume
de dados em consideração, alto volume não significa alto valor.

Dito isso, a maioria das organizações tem pelo menos um conjunto de dados que é crucial tanto para
as necessidades analíticas quanto para o alto volume. O que é _alto volume_? Não há uma definição
fácil, mas no que diz respeito a pipelines, é melhor pensar em termos de um espectro, em vez de uma
definição binária de conjuntos de dados de _alto_ e _baixo_ volume.

## Limpeza e Validade dos Dados

Assim como há grande diversidade nas fontes de dados, a qualidade dos dados de origem varia
muito. Como diz o velho ditado, "lixo entra, lixo sai". É importante entender as limitações e
deficiências dos dados de origem e abordá-las nas seções apropriadas do seu pipeline.

Existem muitas características comuns de "dados bagunçados", incluindo, mas não limitadas a, as
seguintes:

- Registros duplicados ou ambíguos
- Registros órfãos
- Registros incompletos ou ausentes
- Erros de codificação de texto
- Formatos inconsistentes (por exemplo, números de telefone com ou sem traços)
- Dados rotulados incorretamente ou sem rótulo

Claro, existem inúmeras outras, bem como questões de validade dos dados específicas ao contexto do
sistema de origem.

## Latência e Largura de Banda do Sistema de Origem

A necessidade de extrair frequentemente grandes volumes de dados dos sistemas de origem é um caso de
uso comum em uma pilha de dados moderna. No entanto, isso apresenta desafios. As etapas de extração
de dados nos pipelines devem lidar com limites de taxa de API, tempos de conexão esgotados,
downloads lentos e proprietários de sistemas de origem que ficam insatisfeitos devido à carga
colocada em seus sistemas.

## Data Warehouses na Nuvem e Data Lakes

Três coisas transformaram o cenário da análise e do armazenamento de dados nos últimos 10 anos, e
todas estão relacionadas ao surgimento dos principais provedores de nuvem pública (Amazon, Google e
Microsoft).

- A facilidade de construir e implantar pipelines de dados, data lakes, data warehouses e
processamento analítico na nuvem. Não há mais necessidade de esperar pelos departamentos de TI e
pela aprovação do orçamento para grandes custos iniciais. Serviços gerenciados -- bancos de dados em
particular -- se tornaram comuns.
- A contínua queda nos custos de armazenamento na nuvem.
- O surgimento de bancos de dados colunar altamente escaláveis, como Amazon Redshift, Snowflake e
Google Big Query.

Um _data warehouse_ é onde os dados de diferentes sistemas são armazenados e modelados para suportar
análises e outras atividades relacionadas a responder perguntas com esses dados. Os dados em um
armazém de dados são estruturados e otimizados para consultas de relatórios e análises.

Um _data lake_ é onde os dados são armazenados, mas sem a estrutura ou otimização de consulta de um
armazém de dados. Ele provavelmente conterá um grande volume de dados, bem como uma variedade de
tipos de dados. Por exemplo, um único data lake pode conter uma coleção de postagens de blog
armazenadas como arquivos de texto, arquivos planos extraídos de um banco de dados relacional e
objetos JSON contendo eventos gerados por sensores em um sistema industrial, embora não seja
otimizado para consultar esses dados com o objetivo de relatórios e análises.

Há um lugar para ambos, data warehouses e data lakes, no mesmo ecossistema de dados, e os pipelines
de dados frequentemente movem dados entre ambos.

## Ferramentas de Transformação e Modelagem de Dados

Os termos _modelagem de dados_ e _transformação de dados_ são frequentemente usados de forma
intercambiável.

- Transformação de Dados: Transformar dados é um termo amplo que é representado pelo _T_ em um
processo _ETL_ ou _ELT_. Uma transformação pode ser algo tão simples quanto converter um timestamp
armazenado em uma tabela de um fuso horário para outro. Também pode ser uma operação mais complexa
que cria uma nova métrica a partir de múltiplas colunas de origem que são agregadas e filtradas
através de alguma lógica de negócios.

- Modelagem de Dados: A modelagem de dados é um tipo mais específico de transformação de dados. Um
modelo de dados estrutura e define dados em um formato que é entendido e otimizado para análise de
dados. Um modelo de dados é geralmente representado como uma ou mais tabelas em um armazém de dados.

Assim como a ingestão de dados, existem várias metodologias e ferramentas presentes em uma
infraestrutura de dados moderna. Como mencionado anteriormente, algumas ferramentas de ingestão de
dados fornecem algum nível de capacidades de transformação de dados, mas estas são frequentemente bastante
simples. Por exemplo, para proteger _informações pessoalmente identificáveis (PII)_, pode ser
desejável transformar um endereço de e-mail em um valor hash que é armazenado no destino final. Tal
transformação é geralmente realizada durante o processo de ingestão.

Para transformações de dados mais complexas e modelagem de dados, é desejável buscar ferramentas e
frameworks especificamente projetados para a tarefa, como o dbt. Além disso, a transformação de
dados é frequentemente específica ao contexto e pode ser escrita em uma linguagem familiar para
engenheiros de dados e analistas de dados, como SQL ou Python.

Modelos de dados que serão usados para análise e relatórios são tipicamente definidos e escritos em
SQL ou através de interfaces de usuário de apontar e clicar. Assim como as considerações de
construir versus comprar, há considerações ao escolher construir modelos usando SQL versus uma
ferramenta _no-code_. SQL é uma linguagem altamente acessível que é comum tanto para engenheiros de
dados quanto para analistas. Ela capacita os analistas a trabalhar diretamente com os dados e
otimizar o design dos modelos para suas necessidades. Também é usada em quase todas as organizações,
proporcionando um ponto de entrada familiar para novos contratados em uma equipe. Na maioria dos
casos, escolher um framework de transformação que suporte a construção de modelos de dados em SQL em
vez de interfaces de usuário de apontar e clicar é desejável. Você terá muito mais personalização e
controle sobre seu processo de desenvolvimento de ponta a ponta.

## Plataformas de Orquestração de Workflows

À medida que a complexidade e o número de pipelines de dados em uma organização crescem, é
importante introduzir uma _plataforma de orquestração de workflows_ na sua infraestrutura de
dados. Essas plataformas gerenciam o agendamento e o fluxo de tarefas em uma pipeline. Imagine uma
pipeline com uma dúzia de tarefas que variam desde ingestões de dados escritas em Python até
transformações de dados escritas em SQL que devem ser executadas em uma sequência específica e
gerenciar dependências entre cada tarefa. Toda equipe de dados enfrenta esse desafio, mas felizmente
existem várias plataformas de orquestração de workflows disponíveis para aliviar o problema.

Algumas plataformas, como Apache Airflow, Luigi e AWS Glue, são projetadas para casos de uso mais
gerais e, portanto, são usadas para uma ampla variedade de pipelines de dados. Outras, como Kubeflow
Pipelines, são projetadas para casos de uso e plataformas mais específicos (workflows de aprendizado
de máquina construídos em contêineres Docker no caso do Kubeflow Pipelines).

## Grafos Acíclicos Dirigidos (Directed Acyclic Graphs)

Quase todas as plataformas modernas de orquestração representam o fluxo e as dependências das
tarefas em um pipeline como um grafo. No entanto, os grafos de pipelines têm algumas restrições
específicas.

Os grafos de pipelines também devem ser _acíclicos_, o que significa que uma tarefa não pode apontar
de volta para uma tarefa previamente concluída. Em outras palavras, não pode haver ciclos. Se
pudesse, então um pipeline poderia rodar indefinidamente!

Com essas duas restrições em mente, os pipelines de orquestração produzem grafos chamados grafos
acíclicos dirigidos (DAGs).

DAGs são uma representação de um conjunto de tarefas e não onde a lógica das tarefas é definida. Uma
plataforma de orquestração é capaz de executar tarefas de todos os tipos.

A plataforma de orquestração executa cada tarefa, mas a lógica das tarefas existe como código SQL e
Python, que roda em diferentes sistemas na infraestrutura de dados.

## Personalizando Sua Infraestrutura de Dados

É raro encontrar duas organizações com exatamente a mesma infraestrutura de dados. A maioria escolhe
ferramentas e fornecedores que atendem às suas necessidades específicas e constrói o restante por
conta própria.

Como mencionado anteriormente, dependendo da cultura e dos recursos da sua organização, você pode
ser incentivado a construir a maior parte da sua infraestrutura de dados por conta própria ou a
depender de fornecedores SaaS. Independentemente de qual caminho você escolha na escala de construir
versus comprar, você pode construir a infraestrutura de dados de alta qualidade necessária para
criar pipelines de dados de alta qualidade.

O que é importante é entender suas restrições (dinheiro, recursos de engenharia, segurança e
tolerância ao risco legal) e as compensações resultantes.