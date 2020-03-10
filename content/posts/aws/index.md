---
title: "AWS em português"
date: "2020-03-10"
draft: true

tags: ["cloud platform", "aws"]
categories: ["misc"]
---
[Amazon Web Services](https://aws.amazon.com/pt/) (AWS) é o nome do provedor de computação em nuvem mais utilizado no mundo, de acordo com o seguinte infográfico:

{{< figure src="img/cloud-providers-marketshare-q4-2019.jpeg" caption="**Fonte:** https://www.statista.com/chart/18819/worldwide-market-share-of-leading-cloud-infrastructure-service-providers/" >}}

A AWS oferece, na data do artigo, uma lista com [175 serviços](https://aws.amazon.com/products/pt/), abrangendo desde computação em nuvem até envolvimento do cliente. O nome que a AWS usa para cada um destes serviços nem sempre é claro para os marinheiros de primeira viagem, o que acaba intimidando alguns aventureiros. Por este motivo foi escrito o [Amazon Web Services In Plain English](https://expeditedsecurity.com/aws-in-plain-english/), *Amazon Web Services em Inglês* em tradução livre deste autor, que destila descrições mais amigáveis para alguns dos serviços mais utilizados. O objetivo deste artigo é adaptar o *Amazon Web Services In Plain English* para o português.

## Executando Aplicações

### [EC2 (Elastic Compute Cloud)](https://aws.amazon.com/pt/ec2/)

**Poderia chamar-se:** Amazon Virtual Servers

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

### [Lambda](https://aws.amazon.com/pt/lambda/)

**Poderia chamar-se:** AWS Functions

**Uso:** Executar trechos de código de forma escalável sem gerenciar servidores

O AWS Lambda é uma implementação de [FaaS](https://computerworld.com.br/2017/06/16/saas-faas-proxima-fronteira-da-computacao-em-nuvem/). Possui integração com diversos serviços da AWS, consumindo e gerando dados de forma escalável. Também é possível executar o código a partir de um evento gerado por outro serviço da amazon. O número de aplicações do Lambda é grande e cresce cada dia a mais com a tração de [serverless](https://serverless-stack.com/chapters/pt/what-is-serverless.html) no mundo da tecnologia.

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

### [EMR](https://aws.amazon.com/pt/emr/)

**Compatível com:** Apache Spark, Apache Hive, Apache HBase, Apache Flink e Presto sobre o Apache Hadoop

### [EKS (Elastic Kubernetes Service)](https://aws.amazon.com/pt/eks/)

**Compatível com:** Kubernetes

### [MCS (Managed Apache Cassandra Service)](https://aws.amazon.com/pt/eks/)

**Compatível com:** Apache Cassandra

### [Neptune](https://aws.amazon.com/pt/neptune/)

**Compatível com:** Gremlin e SPARQL