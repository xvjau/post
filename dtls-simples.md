---
date: "2019-10-06"
title: "DTLS Simples"
desc: "Como utilizar as camadas client/server do protocolo DTLS usando a biblioteca OpenSSL."
categories: [ "code" ]
draft: true
---
O protocolo DTLS, grosso modo, é um addon do TLS, que é a versão mais nova e segura do SSL, só que em vez de usar por baixo o TCP, que garante entrega na ordem certa dos pacotes, além de outras garantias, o UDP é permitido, ou seja, datagramas. Em teoria essa forma de usar TLS é uma versão mais light. E a pergunta que tento responder aqui é: será que isso é verdade?

Para criar um sample client/server de DTLS usando a biblioteca OpenSSL você irá precisar de alguns passos de setup, conforme especificado [neste tutorial](https://github.com/nplab/DTLS-Examples/blob/master/DTLS.pdf). O repositório [DTLS-Examples](https://github.com/nplab/DTLS-Examples) possui alguns starts para começarmos a compilar e rodar um pouco de código, mas nem tudo são flores na hora de rodar para Windows.

O exemplo que peguei, `dtls_udp_echo.c`, como diz o nome, usa DTLS em cima de UDP. As funções de setup e de definição de callbacks e settings do OpenSSL são configuradas de acordo com o esperado, mas por algum motivo quando a conexão entre um server e um client é estabelecida o server dispara vários listenings e a conexão estabelecida pelo client permanece sem escrita e leitura.
