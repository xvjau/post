---
date: "2019-10-01"
title: "DTLS Simples"
desc: "Como utilizar as camadas client/server do protocolo DTLS usando a biblioteca OpenSSL."
categories: [ "code" ]
draft: true
---
O protocolo DTLS, grosso modo, é um addon do TLS, que é a versão mais nova e segura do SSL, só que em vez de usar por baixo o TCP, que garante entrega na ordem certa dos pacotes, além de outras garantias, o UDP é permitido, ou seja, datagramas. Em teoria essa forma de usar TLS é uma versão mais light. E a pergunta que tento responder aqui é: será que isso é verdade?

Meus testes foram feitos no Windows 10 64 bits em compilação 32 do OpenSSL e um sample de client/server que apenas executa o echo. A única mudança no código é a hora de chamar a função `SSL_CTX_new()`, onde definimos o protocolo SSL.

```
SSL_CTX_new(DTLS_method());
SSL_CTX_new(TLSv1_2_method());
```

