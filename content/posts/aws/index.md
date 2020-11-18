---
title: AWS em português
date: 2020-03-10

tags:
- cloud platforms
- aws

categories:
- guias
---
[Amazon Web Services](https://aws.amazon.com/pt/) (AWS) é o nome do provedor de computação em nuvem mais utilizado no mundo, de acordo com o seguinte infográfico:

{{< figure src="img/cloud-providers-marketshare-q4-2019.jpeg" caption="**Fonte:** https://www.statista.com/chart/18819/worldwide-market-share-of-leading-cloud-infrastructure-service-providers/" >}}

A AWS oferece, na data do artigo, uma lista com [175 serviços](https://aws.amazon.com/products/pt/), abrangendo desde computação em nuvem até envolvimento do cliente. O nome que a AWS usa para cada um destes serviços nem sempre é claro para os marinheiros de primeira viagem, o que acaba intimidando alguns aventureiros. Por este motivo foi escrito o [Amazon Web Services In Plain English](https://expeditedsecurity.com/aws-in-plain-english/), *Amazon Web Services em Inglês* em tradução livre deste autor, que destila descrições mais amigáveis para alguns dos serviços mais utilizados. O objetivo deste artigo é adaptar o *Amazon Web Services In Plain English* para o português.

## Fundamentos

### [EC2 (Elastic Compute Cloud)](https://aws.amazon.com/pt/ec2/)

**Poderia chamar-se:** Amazon Infrastructure as a Service

**Uso:** Executar programas em ambientes virtuais de forma segura e escalável

Você pode executar em uma instância EC2 todo tipo de aplicação que roda em um computador, desde servidores web até computações dedicadas. É possível escolher a memória e capacidade de processamento, a partir do tipo de instância, a capacidade de disco e configurações de rede. É possível também escolher um sistema operacional já com softwares específicos instalados e pré-configurados, possibilitando executar um servidor logo após a inicialização da máquina.

### [IAM (Identity and Access Management)](https://aws.amazon.com/pt/iam/)

**Poderia chamar-se:** Usuários, Grupos e Políticas de acesso

**Uso:** Gerenciar usuários adicionais, grupos de usuários e acesso

O IAM permite gerenciar as regras de acesso de e entre serviços da AWS de forma prática e centralizada. O uso do IAM é central para o uso eficiente dos recursos da AWS. O acesso pelo [AWS CLI](https://aws.amazon.com/pt/cli/) e pelos [SDKs](https://aws.amazon.com/pt/tools/) também é intermediado por IAM.

### [S3 (Simple Storage Service)](https://aws.amazon.com/pt/s3/)

**Poderia chamar-se:** Simple Storage Service

**Uso:** Armazenar arquivos e poder acessá-los por HTTP

O S3 armazena arquivos de forma escalável, performática e segura, com altos níveis de disponibilidade dos dados. A habilidade de poder acessar os arquivos via HTTP permite que você disponibilize arquivos para download a baixo custo de armazenamento.

Ao acessar o console da AWS é possível ver os arquivos organizados por pastas, mas o S3 não possui pastas. Isto é apenas para familiarizar o usuário. Todos os arquivos são associados a uma chave, que é o *caminho de pastas* apresentado no console. Isso implica que renomear uma *pasta* no console implica em modificar as chaves de todos os arquivos *dentro daquela pasta*, que leva mais tempo que modificar o nome de uma pasta. Geralmente não é possível notar a diferença, mas é importante saber este detalhe.

*OBS:* No *Amazon Web Services In Plain English* o autor acredita que o serviço poderia chamar-se de *Servidor FTP ilimitado*, mas com base [nesta thread do Reddit](https://www.reddit.com/r/aws/comments/9xyo9d/aws_in_plain_english/e9wkdfi) e [neste artigo](https://expeditedsecurity.com/aws-in-plain-english/s3/) decidi que o nome dado pela própria AWS já era assertivo o suficiente.

### [VPC (Virtual Private Cloud)](https://aws.amazon.com/pt/vpc/)

**Poderia chamar-se:** Amazon Private Virtual Network

**Uso:** Definir uma rede logicamente isolada em que os recursos da conta residirão

Com o uso da VPC é possível configurar uma topologia de redes com sub-redes, gateways e conexão entre VPCs. Também é possível conectar a VPC à redes remotas do usuário (infraestrutura local, por exemplo), permitindo o uso de serviços entre estas duas redes. O acesso às sub-redes também pode ser controlado por meio do [Network ACL (Access Control List)](https://docs.aws.amazon.com/pt/vpc/latest/userguide/vpc-network-acls.html), dando uma camada a mais de segurança para a sua rede.

### [Route53](https://aws.amazon.com/pt/route53/)

**Poderia chamar-se:** Amazon DNS and Domains

**Uso:** Comprar domínios de internet e configurar os registros DNS

Além do registro de domínios o Route53 também possui serviços de health-checking para rotear requisições para serviços que possam processá-las e realizar monitoramento destes. O Route53 permite a configuração fácil de domínios para serviços dentro e fora da AWS.

### [CloudFront](https://aws.amazon.com/pt/cloudfront/)

**Poderia chamar-se:** Amazon CDN

**Uso:** Distribuir conteúdo de forma segura e eficiente

Se a sua aplicação requer a entrega de conteúdo de forma rápida e segura na AWS, você está procurando o CloudFront. Também é possível fazer alterações as requisições, como adicionar headers com o Lambda@Edge.

### [CloudSearch](https://aws.amazon.com/pt/cloudsearch/)

**Poderia chamar-se:** Amazon fulltext search

**Uso:** Realizar buscas textuais em arquivos no S3, em tabelas do DynamoDB e em instâncias do RDS

O CloudSearch já possui integração com os serviços da AWS facilitando a operação.

### [Elastic Beanstalk](https://aws.amazon.com/pt/elasticbeanstalk/)

**Poderia chamar-se:** Amazon Platform as a Service

**Uso:** Executar uma aplicação abstraindo a infraestrutura

O Elastic Beanstalk cuida da configuração da infraestrutura e a deixa pronta para executar a sua aplicação, bastando fazer o upload do código. O Elastic Beanstalk também te permite tomar todo o controle da infraestrutura, caso você precise.

## Contêineres

### [ECS (Elastic Container Service)](https://aws.amazon.com/pt/ecs/)

**Poderia chamar-se:** Elastic Container Service

**Uso:** Executar contêineres abstraindo a infraestrutura

O ECS configura todo o ambiente para que você possa executar contêineres na AWS. Você pode executar em instâncias EC2 ou usando o [Fargate](https://aws.amazon.com/fargate/). Caso você execute em instâncias EC2 o ECS facilita toda a configuração e escalonamento. O recurso que controla o ciclo de vida do contêiner chama-se [Task](https://docs.aws.amazon.com/pt_br/AmazonECS/latest/developerguide/task_definitions.html) e o recurso que expõe os contêineres para a rede, escala o número e inicia e mantém contêineres saudáveis executando chama-se [Service](https://docs.aws.amazon.com/pt_br/AmazonECS/latest/developerguide/ecs_services.html). A Task pode ser definida em um arquivo JSON.

### [ECR (Elastic Container Registry)](https://aws.amazon.com/pt/ecr/)

**Poderia chamar-se:** Elastic Container Registry

**Uso:** Armazenar e baixar imagens de contêiner Docker

### [EKS (Elastic Kubernetes Service)](https://aws.amazon.com/pt/eks/)

**Poderia chamar-se:** Elastic Kubernetes Service

**Uso:** Executar contêineres Docker em um cluster Kubernetes gerenciado

O EKS configura e inicia os nós de controle e workers do Kubernetes. O escalonamento, operação e segurança do cluster são gerenciados pela AWS. Também é possível utilizar o Fargate com o EKS.

## Serverless

Serviços [serverless](https://serverless-stack.com/chapters/pt/what-is-serverless.html) permitem desenvolver aplicações abstraindo a operação da infraestrutura. Este modelo permite criar aplicações resilientes, escaláveis e seguras.

### [Lambda](https://aws.amazon.com/pt/lambda/)

**Poderia chamar-se:** AWS Functions

**Uso:** Executar trechos de código de forma escalável sem gerenciar servidores

O AWS Lambda é uma implementação de [FaaS](https://computerworld.com.br/2017/06/16/saas-faas-proxima-fronteira-da-computacao-em-nuvem/). Possui integração com diversos serviços da AWS, consumindo e gerando dados de forma escalável. Também é possível executar o código a partir de um evento gerado por outro serviço da amazon.

### [StepFunctions](https://aws.amazon.com/pt/step-functions/)

**Poderia chamar-se:** Lambda Orchestrator

**Uso:** Criar e executar fluxos de ação entre componentes discretos e distribuídos

Com o Step Functions você pode criar fluxos complexos de ações, coordenando Lambdas, por exemplo, de forma desacoplada. Um fluxo é um conjunto de passos (steps) e suas correlações (relationships), onde cada passo pode executar computação (work), fazer escolhas, passar parâmetros, iniciar execução paralela, timeout ou finalizar o fluxo. Fluxos podem ser criados de forma visual ou com um arquivo JSON.

### [DynamoDB](https://aws.amazon.com/pt/dynamodb/)

**Poderia chamar-se:** Serverless NoSQL Tables

**Uso:** Armazenar dados não relacionais

O DynamoDB é um banco NoSQL gerenciado da AWS. Dizer que este banco é serverless significa que você não precisa preocupar-se com escalonamento. É possível reservar as capacidades de leitura e escrita ou pagar por demanda.

OBS: Até a data do artigo, o DynamoDB não permite criar instâncias de banco. O usuário cria tabelas para armazenar dados e realizar consultas. As capacidades de leitura e escrita são configuradas por tabela.

### [API Gateway](https://aws.amazon.com/pt/api-gateway/)

**Uso:** Gerenciar e monitorar APIs

Você pode configurar as regras de acesso aos serviços internos, centralizando o gerenciamento de segurança, versionamento, documentação e monitoramento. O API Gateway também facilita expor Lambdas em uma API. Também é possível mapear requisições da API diretamente com operações no DynamoDB.

### [Cognito](https://aws.amazon.com/pt/cognito/)

**Poderia chamar-se:** Serverless Authentication

**Uso:** Autenticar e inscrever usuários em base própria ou de forma federada para suas aplicações web e mobile

O Cognito facilita a implantação e uso de um serviço centralizado de autenticação. Ele também permite sincronizar dados entre dispositivos, a fim de que a experiência do usuário do seu aplicativo seja consistente. O Cognito também trabalha com provedores de identidade externos através dos protocolos SAML e OpenID Connect.

### [SQS (Simple Queue Service)](https://aws.amazon.com/pt/sqs/)

**Poderia chamar-se:** Simple Queue Service

**Uso:** Filas de mensagem

O SQS utiliza o modelo de Polling para que os consumidores recebam as mensagens. O SQS implementa apenas uma fila de mensagens. No caso em que uma mensagem deve ser entregue para mais de um consumidor é necessário fazer o uso de outros serviços, com ou sem o SQS.

### [SNS (Simple Notification Service)](https://aws.amazon.com/pt/sns/)

**Poderia chamar-se:** Simple Notification Service

**Uso:** Enviar notificações para aplicações e serviços inscritos e enviar mensagens SMS

O SNS permite enviar mensagens para vários destinos inscritos em um tópico. Diferente do SQS, o SNS utiliza o modelo de pushing. Um caso comun de uso é o [fanout](https://aws.amazon.com/getting-started/tutorials/send-fanout-event-notifications/) em que o SNS é utilizado para enviar uma mensagem para diversas filas do SQS.

### [Kinesis](https://aws.amazon.com/pt/kinesis/)

**Poderia chamar-se:** Streaming data processor

**Uso:** Processar streams de dados em tempo real

O Kinesis facilita implantar soluções de ingestão de dados em tempo real abstraindo toda a operação e infraestrutura e deixando a cargdo utilizador a fonte e processamento dos dados. O usuário pode definir o número de shards de um stream de acordo com a capacidade de entrada e saída de dados requerida. Também é possível alterar o número de shards de forma dinâmica.

### [SES (Simple Email Service)](https://aws.amazon.com/pt/ses/)

**Poderia chamar-se:** Simple Email Service

**Uso:** Enviar e receber emails

## CI/CD

### [CodeCommit](https://aws.amazon.com/pt/codecommit/)

**Poderia chamar-se:** Amazon Git

**Uso:** Repositórios git hospedados na AWS

### [Cloud9](https://aws.amazon.com/pt/cloud9/)

**Poderia chamar-se:** Amazon Cloud IDE

**Uso:** Editar e executar código direto do navegador, sem instalação de software

### [CodeBuild](https://aws.amazon.com/pt/codebuild/)

**Uso:** Fazer build e teste do código de suas aplicações

### [CodeDeploy](https://aws.amazon.com/pt/codedeploy/)

**Uso:** Gerenciar o deploy de suas aplicações em instâncias EC2 ou instâncias on-premise

### [CodePipeline](https://aws.amazon.com/pt/codepipeline/)

**Uso:** Realizar entrega e promoção automática do código

### [CodeStar](https://aws.amazon.com/pt/codestar/)

**Poderia chamar-se:** Amazon Developer Asistant

**Uso:** Configurar facilmente tudo necessário para realizar a entrega automática do código de um projeto, gerenciar time de desenvolvimento e visualizar as ações no projeto

## Gerenciamento e Monitoramento

### [CloudFormation](https://aws.amazon.com/pt/cloudformation/)

**Poderia chamar-se:** Amazon Infrastructure as Code

**Uso:** Criar e gerenciar recursos na AWS a partir de código

O CloudFormation te permite criar, modificar e remover os recursos que você utiliza como um grupo por meio de arquivos JSON ou YAML. Esta abordagem permite versionar o código que gerencia sua infraestrutura, facilitando o gerenciamento e dificultando erros humanos.

### [CloudWatch](https://aws.amazon.com/pt/cloudwatch/)

**Poderia chamar-se:** Amazon Monitoring

**Uso:** Coletar e acompanhar métricas e logs e criar alarmes

O CloudWatch pode coletar métricas de muitos dos serviços da AWS e também recebe métricas geradas por suas aplicações. Podem ser criados gráficos para exibir as métricas mais relevantes e dar uma visão resumida dos estado dos seus serviços. O CloudWatch também permite rastrear eventos de alteração dos recursos da AWS.

### [CloudTrail](https://aws.amazon.com/pt/cloudtrail/)

**Poderia chamar-se:** Amazon Audit

**Uso:** Auditar as atividades realizadas na conta da AWS

### [X-Ray](https://aws.amazon.com/pt/xray/)

**Poderia chamar-se:** Amazon Distributed Application Tracing

**Uso:** Analisar e debugar a interação entre serviços distribuídos

### [Inspector](https://aws.amazon.com/pt/inspector/)

**Uso:** Analisar a segurança da sua instância EC2

### [Trusted Advisor](https://aws.amazon.com/pt/premiumsupport/technology/trusted-advisor/)

**Uso:** Receber orientações de melhores práticas de utilização dos recursos da AWS

## Serviços gerenciados

A AWS oferece instâncias de aplicações com operação totalmente gerenciada, facilitando a instalação, configuração, escalonamento e garantia de disponibilidade.

### [RDS (Relational Database Service)](https://aws.amazon.com/pt/rds/)

**Compatível com:** MySQL, MariaDB, Oracle Database, SQL Server ou PostgreSQL

Possui também um tipo de instância chamada [Aurora](https://aws.amazon.com/rds/aurora/) compatível com MySQL e PostgreSQL.

### [ElastiCache](https://aws.amazon.com/pt/elasticache/)

**Compatível com:** Redis e Memcached

### [DocumentDB](https://aws.amazon.com/pt/documentdb/)

**Compatível com:** MongoDB

### [Elasticsearch Service](https://aws.amazon.com/pt/elasticsearch-service/)

**Compatível com:** Elasticsearch

### [Amazon MQ](https://aws.amazon.com/pt/amazon-mq/)

**Compatível com:** ActiveMQ

### [MSK (Managed Streming for Apache Kafka)](https://aws.amazon.com/pt/msk/)

**Compatível com:** Apache Kafka

### [EMR (Elastic MapReduce)](https://aws.amazon.com/pt/emr/)

**Compatível com:** Apache Spark, Apache Hive, Apache HBase, Apache Flink e Presto sobre o Apache Hadoop

### [MCS (Managed Apache Cassandra Service)](https://aws.amazon.com/pt/eks/)

**Compatível com:** Apache Cassandra

### [Neptune](https://aws.amazon.com/pt/neptune/)

**Compatível com:** Gremlin e SPARQL

### [OpsWorks](https://aws.amazon.com/opsworks/)

**Poderia chamar-se:** Managed Configuration Management Servers

**Compatível com:** Chef Automate e Puppet Enterprise

## Palavras finais

Conhecer todos os recursos de um provedor de computação em nuvem é um trabalho extenso. Cada dia surge um novo recurso para facilitar o nascimento e operação de uma nova ideia em nuvem. Este artigo não propõe ser uma lista completa dos recursos da AWS. Espero que ao final desta leitura você tenha um conhecimento básico da AWS e já saiba quais recursos estudar mais a fundo para viabilizar o seu projeto.