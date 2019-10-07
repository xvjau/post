---
date: "2019-10-06"
title: "DTLS Simples"
desc: "Como utilizar as camadas client/server do protocolo DTLS usando a biblioteca OpenSSL."
categories: [ "code" ]
draft: true
---
O protocolo DTLS, grosso modo, é um addon do TLS, que é a versão mais nova e segura do SSL, só que em vez de usar por baixo o TCP, que garante entrega na ordem certa dos pacotes, além de outras garantias, o UDP é permitido, ou seja, datagramas. Em teoria essa forma de usar TLS é uma versão mais light. E a pergunta que tento responder aqui é: será que isso é verdade?

Para criar um sample client/server de DTLS usando a biblioteca OpenSSL você irá precisar de alguns passos de setup, conforme especificado [neste tutorial](https://gist.github.com/Jxck/b211a12423622fe304d2370b1f1d30d5).
