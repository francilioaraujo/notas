---
title: Redes - 01 Internet e modelo OSI
date: 2024-09-30

tags:
- redes

categories:
- caderno-de-redes
---

Uma rede de computadores serve para que dois computadores possam se comunicar entre si. Para o caso simples de conectar dois computadores, podemoms ligá-los com um cabo e desenvolver um formato de comunicação própria que permita a troca de dados entre os dois. No entanto, ao adicionar mais computadores temos de arranjar formas mais elaboradas para permitir essa troca de dados. Quando um computador inicia uma comunicação com um segundo para obter um retorno chamamo-o de cliente e ao segundo de servidor, pois o primeiro requisita algum dado e o segundo o serve.

Para que a comunicação entre computadores diferentes (e possivelmente de fabricantes diferentes) ocorra é necessário fazer uso de um formato de dados e um comportamento de envio e recebimento padronizados. Chamamos de protocolo o padrão de formato de dados e comportamento de envio e recebimento destes. Para facilitar o desenvolvimento de protocolos foi proposto um modelo para guiar o desenvolvimento de protocolos de rede.

## Modelo OSI

O modelo OSI é um modelo de sete camadas, em que a função e a relação de cada camada é bem estabelecida, de modo que um protocolo desenvolvido para funcionar em uma camada possa ser facilmente trocado sem afetar as outras camadas. As sete camadas propostas e suas funcionalidades são apresentados a seguir:

* Camada de Aplicação: é a camada mais alta. É responsável por implementar as funções das aplicações. Protocolos nesta camada possibilitam a comunicação entre aplicações em computadores diferentes;
* Camada de Apresentação: é a camada responsável por formatar os dados, por exemplo, lindando com encriptação, codificação e compressão de dados;
* Camada de Sessão: é a camada responsável por estabelecer uma sessão entre a comunicação, lidando com autenticação e autorização, por exemplo;
* Camada de Transporte: é a camada responsável pela transmissão de dados fim a fim, seguindo os requisitos da comunicação, como verificação de erros, erros de transmissão e erros de ordenação dos dados;
* Camada de Rede: é a camada responsável pela seleção da rota que será utilizada pela transmissão dos dados;
* Camada de Ligação de Dados: é a camada responsável pela transmissão dos dados através de uma ligação;
* Camada Física: é a camada mais baixa. É responsável por converter os bytes em sinais físicos e os padrões destes sinais, como voltagem, intensidade, distância máxima de trâsmissão e etc.

É comum se referir à camada de aplicação como camada sete, à camada de apresentação como camada seis e etc. O protocolo da camada sete adiciona dados "ao redor" dos dados da aplicação para uso do protocolo. O protocolo da camada seis adiciona mais dados (se o protocolo for utilizado) e cada protocolo sebsequente. A esse processo damos o nome de encapsulamento de dados. O processo inverso é chamado de desencapsulamento. Ao transmitir dados de um computador para outro, os dados são encapsulados camada a camada, enviados através da rede e desencapsulados no outro computador. Na imagem seguinte podemos ver como ocorre o processo de encapsulamento e desencapsulamento no modelo OSI:

![encapsulamento](./images/encapsulamento.png)

## Modelo TCP/IP

Com a popularização da internet, dois protocolos em específico ganharam ampla utilização, o TCP da camada de transporte e o IP da camada de rede. Isso deu origem a um modelo mais simples, com apenas quatro camadas. Neste modelo, as camadas de aplicação, apresentação e sessão são mescladas em uma só camada, chamada de Camada de Aplicação, a camada de rede é chamada de camada internet e as camadas de Enlace de Dados e física são mescladas em uma camada chamada de camada de Enlace. Na imagem abaixo podemos ver um comparativo visual entre os modelos:

![Caparação entre modelos](./images/modelos.png)