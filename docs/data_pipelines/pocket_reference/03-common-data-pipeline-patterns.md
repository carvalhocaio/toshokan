# Padrões Comuns de Pipeline de Dados

Mesmo para engenheiros de dados experientes, projetar um novo pipeline de dados é uma nova jornada a
cada vez. Diferentes fontes de dados e infraestruturas apresentam tanto desafios quanto
oportunidades. Além disso, os pipelines são construídos com diferentes objetivos e restrições. Os
dados precisam ser processados em quase tempo real? Podem ser atualizados diariamente? Serão
modelados para uso em um dashboard ou como entrada para um modelo de aprendizado de máquina?

Felizmente, existem alguns padrões comuns em pipelines de dados que se mostraram bem-sucedidos e são
extensíveis para muitos casos de uso.

## ETL e ELT

Talvez não haja padrão mais conhecido do que ETL e seu irmão mais moderno, ELT. Ambos são padrões
amplamente utilizados em data warehousing e business intelligence. Nos últimos anos, eles inspiraram
padrões de pipeline para ciência de dados e modelos de aprendizado de máquina em produção. Eles são
tão conhecidos que muitas pessoas usam esses termos como sinônimos de pipelines de dados, em vez de
padrões que muitos pipelines seguem.

Dadas suas raízes em data warehousing, é mais fácil descrevê-los nesse contexto, que é o que esta
seção faz.

Ambos os padrões são abordagens de processamento de dados usadas para alimentar dados em um data
warehouse e torná-los úteis para analistas e ferramentas de relatórios. A diferença entre os dois é
a ordem de suas duas últimas etapas (transformar e carregar), mas as implicações de design ao
escolher entre eles são substanciais.

A etapa de _extração_ coleta dados de várias fontes em preparação para carregamento e transformação.

A etapa de _carregamento_ traz os dados brutos (no caso de ELT) ou os dados totalmente transformados
(no caso de ETL) para o destino final. De qualquer forma, o resultado final é carregar dados no data
warehouse, data lake ou outro destino.

A etapa de _transformação_ é onde os dados brutos de cada sistema de origem são combinados e
formatados de maneira que sejam úteis para analistas, ferramentas de visualização ou qualquer caso
de uso que seu pipeline esteja atendendo. Há muito nesta etapa, independentemente de você projetar
seu processo como ETL ou ELT.

## A Emergência do ELT sobre o ETL

ETL foi o padrão ouro dos padrões de pipeline de dados por décadas. Embora ainda seja usado, mais
recentemente o ELT emergiu como o padrão preferido. Por quê? Antes da nova geração de data
warehouses, principalmente na nuvem, as equipes de dados não tinham acesso a data warehouses com a
capacidade de armazenamento ou computação necessária para lidar com o carregamento de grandes
quantidades de dados brutos e transformá-los em modelos de dados utilizáveis. Os bancos de dados
eram baseados em linhas, funcionavam bem para casos de uso transacionais, mas não para consultas em
grande volume, comuns em análises. Assim, os dados eram primeiro extraídos dos sistemas de origem e
depois transformados em um sistema separado antes de serem carregados em um warehouse para qualquer
modelagem final de dados e consulta por analistas e ferramentas de visualização.

A maioria dos data warehouses de hoje são construídos em bancos de dados colunar altamente
escaláveis que podem armazenar e executar transformações em grandes conjuntos de dados de maneira
econômica. Graças à eficiência de I/O de um banco de dados colunar, compressão de dados e a
capacidade de distribuir dados e consultas em muitos nós que podem trabalhar juntos para processar
dados, as coisas mudaram. Agora é melhor focar em extrair dados e carregá-los em um data warehouse
onde você pode então realizar as transformações necessárias para completar o pipeline.

O impacto da diferença entre data warehouses baseados em linhas e colunas não pode ser subestimado.

Abaixo, ilustra um exemplo de como os registros são armazenados em disco em um banco de dados
baseado em linhas, como MySQL ou Postgres. Cada linha do banco de dados é armazenada junta no
disco, em um ou mais blocos dependendo do tamanho de cada registro. Se um registro for menor que um
único bloco ou não for divisível pelo tamanho do bloco, ele deixa algum espaço em disco não
utilizado.

OrderId | CustomerId | ShippingCountry | OrderTotal
------: | ---------: | --------------- | ---------:
1       | 1258       | US              | 55.25
2       | 5698       | AUS             | 125.36
3       | 2265       | US              | 776.95
4       | 8954       | CA              | 32.16
        |            |                 |
Bloco 1 | 1, 1258, US, 55.25
Bloco 2 | 2, 5698, AUS, 125.36
Bloco 3 | 3, 2265, US, 776.95
Bloco 4 | 4, 8954, CA, 32.16

_Uma tabela armazenada em um banco de dados de armazenamento baseado em linhas. Cada bloco contém_
_um registro (linha) da tabela._

Considere um caso de uso de banco de dados de processamento de transações online (OLTP), como um
aplicativo web de e-commerce que utiliza um banco de dados MySQL para armazenamento. O aplicativo
web solicita leituras e gravações do banco de dados MySQL, frequentemente envolvendo múltiplos
valores de cada registro, como os detalhes de um pedido em uma página de confirmação de
pedido. Também é provável que consulte ou atualize apenas um pedido por vez. Portanto, o
armazenamento baseado em linhas é ideal, pois os dados que o aplicativo precisa estão armazenados
em proximidade no disco, e a quantidade de dados consultada de uma vez é pequena.

O uso ineficiente do espaço em disco devido aos registros deixarem espaço vazio nos blocos é uma
troca razoável nesse caso, pois a velocidade de leitura e gravação de registros únicos
frequentemente é o mais importante. No entanto, em análises a situação é inversa. Em vez da
necessidade de ler e escrever pequenas quantidades de dados frequentemente, frequentemente lemos e
escrevemos grandes quantidades de dados com pouca frequência. Além disso, é menos provável que uma
consulta analítica exija muitas, ou todas, as colunas de uma tabela, mas sim uma única coluna de uma
tabela com muitas colunas.

Por exemplo, considere a tabela de pedidos em nosso aplicativo de e-commerce funcional. Entre outras
coisas, ela contém o valor em dólares do pedido, bem como o país para o qual está sendo enviado. Ao
contrário do aplicativo web, que trabalha com pedidos um de cada vez, um analista usando o data
warehouse desejará analisar os dados do warehouse que possuem colunas adicionais que contêm valores
de várias tabelas em nosso banco de dados MySQL. Por exemplo, pode conter informações sobre o
cliente que fez o pedido. Talvez o analista queira somar todos os pedidos feitos por clientes com
contas ativas atualmente. Tal consulta pode envolver milhões de registros, mas apenas ler de duas
colunas, OrderTotal e CustomerActive. Afinal, a análise não é sobre criar ou alterar dados (como no
OLTP), mas sim a derivação de métricas e a compreensão dos dados.

Abaixo, ilustra um banco de dados colunar, como Snowflake ou Amazon Redshift, que armazena dados em
blocos de disco por coluna em vez de linha. No nosso caso, a consulta escrita pelo analista só
precisa acessar blocos que armazenam valores de OrderTotal e CustomerActive em vez de blocos que
armazenam os registros baseados em linhas, como no banco de dados MySQL. Assim, há menos I/O de
disco, bem como menos dados para carregar na memória para realizar o filtro e a soma exigidos pela
consulta do analista. Um benefício final é a redução no armazenamento, graças ao fato de que os
blocos podem ser totalmente utilizados e comprimidos de forma otimizada, já que o mesmo tipo de dado
é armazenado em cada bloco em vez de múltiplos tipos que tendem a ocorrer em um único registro
baseado em linhas.

OrderId | CustomerId | ShippingCountry | OrderTotal | CustomerActive
------: | ---------: | --------------- | ---------: | :------------:
1       | 1258       | US              | 55.25      | TRUE
2       | 5698       | AUS             | 125.36     | TRUE
3       | 2265       | US              | 776.95     | TRUE
4       | 8954       | CA              | 32.16      | FALSE
        |            |                 |            |
Bloco 1 | 1, 2, 3, 4
Bloco 2 | 1258, 5698, 2265, 8954
Bloco 3 | US, AUS, US, CA
Bloco 4 | 55.25, 125.36, 776.95, 32.16
Bloco 5 | TRUE, TRUE, TRUE, FALSE

_Uma tabela armazenada em um banco de dados de armazenamento baseado em colunas. Cada bloco de_
_disco contém dados da mesma coluna. As duas colunas envolvidas em nossa consulta de exemplo estão_
_destacadas. Apenas esses blocos precisam ser acessados para executar a consulta. Cada bloco_
_contém dados do mesmo tipo, tornando a compressão otimizada._

No geral, a emergência dos bancos de dados colunar significa que armazenar, transformar e consultar
grandes conjuntos de dados é eficiente dentro de um data warehouse. Engenheiros de dados podem usar
isso a seu favor construindo etapas de pipeline que se especializam em extrair e carregar dados em
warehouses onde podem ser transformados, modelados e consultados por analistas e cientistas de dados
que se sentem mais confortáveis dentro dos limites de um banco de dados. Como tal, o ELT assumiu
como o padrão ideal para pipelines de data warehouse, bem como outros casos de uso em aprendizado de
máquina e desenvolvimento de produtos de dados.

## Subpadrão EtLT

Quando o ELT emergiu como o padrão dominante, ficou claro que fazer algumas transformações após a
extração, mas antes do carregamento, ainda era benéfico. No entanto, em vez de transformações
envolvendo lógica de negócios ou modelagem de dados, esse tipo de transformação é mais limitado em
escopo.

Alguns exemplos do tipo de transformação que se encaixa no subpadrão EtLT incluem os seguintes:

- Remover duplicatas em uma tabela
- Analisar parâmetros de URL em componentes individuais
- Mascarar ou de outra forma ofuscar dados sensíveis

Esses tipos de transformações estão totalmente desconectados da lógica de negócios ou, no caso de
algo como mascarar dados sensíveis, às vezes são necessários o mais cedo possível em um pipeline
por razões legais ou de segurança. Além disso, há valor em usar a ferramenta certa para o trabalho
certo.

Você pode assumir que os padrões relacionados ao ELT restantes são projetados para incluir o
subpadrão EtLT também.

## ELT para Análise de Dados

Como já discutido, bancos de dados colunar são bem adequados para lidar com grandes volumes de
dados. Eles também são projetados para lidar com tabelas largas, ou seja, tabelas com muitas
colunas, graças ao fato de que apenas os dados nas colunas usadas em uma determinada consulta são
escaneados no disco e carregados na memória.

Além das considerações técnicas, os analistas de dados geralmente são fluentes em SQL. Com ELT, os
engenheiros de dados podem se concentrar nas etapas de extração e carregamento em um pipeline
(ingestão de dados), enquanto os analistas podem utilizar SQL para transformar os dados que foram
ingeridos conforme necessário para relatórios e análises. Tal separação limpa não é possível com o
padrão ETL, pois os engenheiros de dados são necessários em todo o pipeline.

Além disso, o padrão ELT reduz a necessidade de prever exatamente o que os analistas farão com os
dados no momento da construção do processo de extração e carregamento. Embora seja necessário
entender o caso de uso geral para extrair e carregar os dados adequados, salvar a etapa de
transformação para mais tarde dá aos analistas mais opções e flexibilidade.

## ELT para Ciência de Dados

Pipelines de dados construídos para equipes de ciência de dados são semelhantes aos construídos para
análise de dados em um data warehouse. Assim como no caso de uso de análise, os engenheiros de dados
estão focados em ingerir dados em um data warehouse ou data lake. No entanto, os cientistas de dados
têm necessidades diferentes dos analistas de dados.

Embora a ciência de dados seja um campo amplo, em geral, os cientistas de dados precisarão de acesso
a dados mais granulares e, às vezes, brutos do que os analistas de dados. Enquanto os analistas de
dados constroem modelos de dados que produzem métricas e alimentam dashboards, os cientistas de
dados passam seus dias explorando dados e construindo modelos preditivos.

Se você está construindo pipelines para apoiar cientistas de dados, descobrirá que as etapas de
extração e carregamento do padrão ELT permanecerão praticamente as mesmas que para suportar
análises.

## ELT para Produtos de Dados e Aprendizado de Máquina

Os dados são usados para mais do que análise, relatórios e modelos preditivos. Eles também são
usados para alimentar _produtos de dados_. Alguns exemplos comuns de produtos de dados incluem os
seguintes:

- Um mecanismo de recomendação de conteúdo que alimenta a tela inicial de um serviço de streaming de
vídeo
- Um mecanismo de busca personalizado em um site de e-commerce
- Um aplicativo que realiza análise de sentimento em avaliações de restaurantes geradas por usuários

Cada um desses produtos de dados é provavelmente alimentado por um ou mais modelos de aprendizado de
máquina (ML), que são ávidos por dados de treinamento e validação. Esses dados podem vir de uma
variedade de sistemas de origem e passar por algum nível de transformação para prepará-los para uso
no modelo. Um padrão semelhante ao ELT é bem adequado para tais necessidades, embora existam vários
desafios específicos em todas as etapas de um pipeline projetado para um produto de dados.

## Etapas em um Pipeline de Aprendizado de Máquina

Assim como os pipelines construídos para análise, os pipelines construídos para ML seguem um padrão
semelhante ao ELT -- pelo menos no início do pipeline. A diferença é que, em vez da etapa de
transformação focar em transformar dados em modelos de dados, uma vez que os dados são extraídos e
carregados em um warehouse ou data lake, há várias etapas envolvidas na construção e atualização do
modelo de ML.

Se você está familiarizado com o desenvolvimento de ML, essas etapas podem parecer familiares 
ambém:

_Ingestão de dados_  
Embora os dados que você ingere possam diferir, a lógica permanece basicamente a mesma para
pipelines construídos para análise, bem como para ML, mas com uma consideração adicional para
pipelines de ML. Ou seja, garantir que os dados que você ingere sejam versionados de uma forma que
os modelos de ML possam posteriormente se referir como um conjunto de dados específico para
treinamento ou validação.

_Pré-processamento de dados_  
Os dados ingeridos provavelmente não estarão prontos para uso no desenvolvimento de
ML. O pré-processamento é onde os dados são limpos e preparados para os modelos. Por exemplo, esta
é a etapa em um pipeline onde o texto é tokenizado, os recursos são convertidos em valores numéricos
e os valores de entrada são normalizados.

_Treinamento de modelo_  
Após novos dados serem ingeridos e pré-processados, os modelos de ML precisam ser re-treinados.

_Implementação de modelo_  
Implantar modelos em produção pode ser a parte mais desafiadora de passar de aprendizado de máquina
orientado a pesquisa para um verdadeiro produto de dados. Aqui, não apenas a versionamento de
conjuntos de dados é necessário, mas também a versionamento de modelos treinados. Frequentemente,
uma API REST é usada para permitir a consulta de um modelo implantado, e endpoints de API para
várias versões de um modelo serão usados. É muito para acompanhar e requer coordenação entre
cientistas de dados, engenheiros de aprendizado de máquina e engenheiros de dados para chegar a um
estado de produção. Um pipeline bem projetado é fundamental para unir tudo isso.

## Incorporar Feedback no Pipeline

Qualquer bom pipeline de ML também incluirá a coleta de feedback para melhorar o modelo. Tome como
exemplo um modelo de recomendação de conteúdo para um serviço de streaming de vídeo. Para medir e
melhorar o modelo no futuro, você precisará acompanhar o que ele recomenda aos usuários, quais
recomendações eles clicam e qual conteúdo recomendado eles gostam após clicarem. Para fazer isso,
você precisará trabalhar com a equipe de desenvolvimento que utiliza o modelo na tela inicial do
serviço de streaming. Eles precisarão implementar algum tipo de coleta de eventos que acompanhe cada
recomendação feita a cada usuário; a versão do modelo que a recomendou; e quando é clicada; e então
levar esse clique para os dados que provavelmente já estão coletando relacionados ao consumo de
conteúdo do usuário.

Todas essas informações podem então ser ingeridas de volta no data warehouse e incorporadas em
futuras versões do modelo, seja como dados de treinamento ou para serem analisadas e consideradas
por um humano (talvez um cientista de dados) para inclusão em um futuro modelo ou experimento.

Os analistas frequentemente serão encarregados de medir a eficácia dos modelos e construir
dashboards para exibir métricas chave do modelo para a organização. Os stakeholders podem usar esses
dashboards para entender quão eficazes vários modelos são para o negócio e para seus clientes.
