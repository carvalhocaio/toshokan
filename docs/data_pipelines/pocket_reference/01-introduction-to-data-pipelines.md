# Introdução a Pipelines de Dados

Por trás de cada dashboard brilhante, modelo de aprendizado de máquina e insight que muda negócios,
está o dado. Não apenas dados brutos, mas dados coletados de inúmeras fontes que precisam ser
limpos, processados e combinados para entregar valor. A famosa frase "dados são o novo petróleo"
provou ser verdadeira. Assim como o petróleo, o valor dos dados está em seu potencial após serem
refinados e entregues ao consumidor. Também como o petróleo, são necessários pipelines eficientes
para entregar dados através de cada estágio de sua cadeia de valor.

## O Que São Pipelines de Dados?

_Pipelines de dados_ são conjuntos de processos que movem e transformam dados de várias fontes para
um destino onde novo valor pode ser derivado. Eles são a base das capacidades de análise, relatórios
e aprendizado de máquina.

A complexidade de um pipeline de dados depende do tamanho, estado e estrutura dos dados de origem,
bem como das necessidades do projeto de análise. Na sua forma mais simples, pipelines podem extrair
dados de uma única fonte, como uma API REST, e carregar para um destino, como uma tabela SQL em um
data warehouse. Na prática, no entanto, pipelines geralmente consistem em múltiplas etapas,
incluindo extração de dados, pré-processamento de dados, validação de dados e entrega dos dados ao
seu destino final. Pipelines frequentemente contêm tarefas de múltiplos sistemas e linguagens de
programação. Além disso, equipes de dados geralmente possuem e mantêm vários pipelines de dados que
compartilham dependências e precisam ser coordenados.

## Quem Constrói Pipelines de Dados?

Com a popularização da computação em nuvem e do software como serviço (SaaS), o número de fontes de
dados que as organizações precisam entender explodiu. Ao mesmo tempo, a demanda por dados para
alimentar modelos de aprendizado de máquina, pesquisas de ciência de dados e insights sensíveis ao
tempo é maior do que nunca. Para acompanhar, a _engenharia de dados_ emergiu como um papel
fundamental nas equipes de análise. _Engenheiros de dados_ se especializam em construir e manter os
pipelines de dados que sustentam o ecossistema de análise.

O objetivo de um engenheiro de dados não é simplesmente carregar dados em um data
warehouse. Engenheiros de dados trabalham de perto com cientistas de dados e analistas para entender
o que será feito com os dados e ajudar a trazer suas necessidades para um estado de produção
escalável.

Engenheiros de dados se orgulham de garantir a validade e a pontualidade dos dados que
entregam. Isso significa testar, alertar e criar planos de contingência para quando algo der
errado. E sim, algo eventualmente dará errado!

As habilidades específicas de um engenheiro de dados dependem um pouco da pilha de tecnologia que
sua organização usa. No entanto, há algumas habilidades comuns que todos os bons engenheiros de
dados possuem.

## Fundamentos de SQL e Data Warehousing

Engenheiros de dados precisam saber como consultar bancos de dados, e SQL é a linguagem universal
para fazer isso. Engenheiros de dados experientes sabem como escrever SQL de alto desempenho e
entendem os fundamentos de data warehousing e modelagem de dados. Mesmo que uma equipe de dados
inclua especialistas em data warehousing, um engenheiro de dados com fundamentos de warehousing é um
parceiro melhor e pode preencher lacunas técnicas mais complexas que surgem.

## Python e/ou Java

A linguagem na qual um engenheiro de dados é proficiente dependerá da pilha de tecnologia de sua
equipe, mas de qualquer forma, um engenheiro de dados não conseguirá fazer o trabalho com
ferramentas "sem código", mesmo que tenha algumas boas em seu arsenal. Python e Java atualmente
dominam na engenharia de dados, mas novos como Go estão surgindo.

## Computação Distribuída

Resolver um problema que envolve grande volume de dados e o desejo de processar dados rapidamente
levou os engenheiros de dados a trabalhar com plataformas de _computação distribuída_. A computação
distribuída combina o poder de múltiplos sistemas para armazenar, processar e analisar grandes
volumes de dados de forma eficiente.

Um exemplo popular de computação distribuída em análises é o ecossistema Hadoop, que inclui
armazenamento de arquivos distribuídos via Hadoop Distributed File System (HDFS), processamento via
MapReduce, análise de dados via Pig, e mais. O Apache Spark é outro framework de processamento
distribuído popular, que está rapidamente superando o Hadoop em popularidade.

Embora nem todos os pipelines de dados exijam o uso de computação distribuída, os engenheiros de
dados precisam saber como e quando utilizar tal framework.

## Administração Básica de Sistemas

Espera-se que um engenheiro de dados seja proficiente na linha de comando do Linux e seja capaz de
realizar tarefas como analisar logs de aplicativos, agendar cron jobs e solucionar problemas de
firewall e outras configurações de segurança. Mesmo trabalhando totalmente em um provedor de nuvem
como AWS, Azure ou Google Cloud, eles acabarão usando essas habilidades para fazer os serviços em
nuvem funcionarem juntos e implantar pipelines de dados.

## Uma Mentalidade Orientada a Objetivos

Um bom engenheiro de dados não possui apenas habilidades técnicas. Eles podem não interagir com os
stakeholders regularmente, mas os analistas e cientistas de dados da equipe certamente o farão. O 
engenheiro de dados tomará melhores decisões arquitetônicas se estiver ciente do motivo pelo qual 
está construindo um pipeline em primeiro lugar.

## Por Que Construir Pipelines de Dados?

Da mesma forma que a ponta do iceberg é tudo o que pode ser visto por um navio que passa, o produto
final do fluxo de trabalho de análise é tudo o que a maioria de uma organização vê. Executivos veem
dashboards e gráficos impecáveis. O marketing compartilha insights bem embalados nas redes
sociais. O suporte ao cliente otimiza o atendimento do call center com base no resultado de um
modelo preditivo de demanda.

O que a maioria das pessoas fora da área de análise muitas vezes não consegue apreciar é que, para
gerar o que é visto, há uma maquinaria complexa que é invisível. Para cada dashboard e insight que
um analista de dados gera e para cada modelo preditivo desenvolvido por um cientista de dados, há
pipelines de dados trabalhando nos bastidores. Não é incomum que um único dashboard, ou mesmo uma
única métrica, seja derivado de dados originados em múltiplos sistemas de origem. Além disso,
pipelines de dados fazem mais do que apenas extrair dados de fontes e carregá-los em tabelas de
banco de dados simples ou arquivos planos para os analistas usarem. Os dados brutos são refinados
ao longo do caminho para serem limpos, estruturados, normalizados, combinados, agregados e, às
vezes, anonimizados ou de outra forma protegidos. Em outras palavras, há muito mais acontecendo
abaixo da linha d'água.

## Como os Pipelines São Construídos?

Junto com os engenheiros de dados, inúmeras ferramentas para construir e dar suporte a pipelines de
dados surgiram nos últimos anos. Algumas são open source, outras comerciais, e algumas são
desenvolvidas internamente. Alguns pipelines são escritos em Python, outros em Java, alguns em
outra linguagem, e alguns sem código algum.

Além disso, pipelines não são apenas construídos - eles são monitorados, mantidos e
estendidos. Os engenheiros de dados são responsáveis não apenas por entregar dados uma vez, mas por
construir pipelines e infraestrutura de suporte que entreguem e processem dados de forma
confiável, segura e pontual. Não é uma tarefa fácil, mas quando é bem feita, o valor dos dados de
uma organização pode realmente ser desbloqueado.
