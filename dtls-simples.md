---
date: "2019-10-06"
title: "DTLS Simples... simples?"
desc: "Como utilizar o protocolo DTLS usando a biblioteca OpenSSL no Windows."
categories: [ "code" ]
draft: true
---
O [protocolo DTLS](https://tools.ietf.org/html/rfc4347), grosso modo, é um addon do TLS, que é a versão mais nova e segura do SSL, só que em vez de usar por baixo o TCP, que garante entrega na ordem certa dos pacotes, além de outras garantias, o UDP é permitido. Ou seja, datagramas. Em teoria essa forma de usar TLS é uma versão mais light. E a pergunta que tento responder aqui é: será que isso é verdade?

A primeira tarefa é conseguir compilar e rodar um sample DTLS para Windows, que é meu sistema operacional alvo. Para criar um sample client/server de DTLS usando a biblioteca OpenSSL (no momento 1.1.1d) você irá precisar de alguns passos de setup, conforme especificado [neste tutorial](https://github.com/nplab/DTLS-Examples/blob/master/DTLS.pdf). O repositório [DTLS-Examples](https://github.com/nplab/DTLS-Examples) possui alguns starts para começarmos a compilar e rodar um pouco de código, mas nem tudo são flores na hora de rodar para Windows.

O exemplo que peguei, `dtls_udp_echo.c`, como diz o nome, usa DTLS em cima de UDP. As funções de setup e de definição de callbacks e settings do OpenSSL são configuradas de acordo com o esperado, mas por algum motivo quando a conexão entre um server e um client é estabelecida o server dispara vários listenings e a conexão estabelecida pelo client permanece sem escrita e leitura.

Analisando a troca de pacotes pelo Wire Shark descobri um erro no handshake envolvendo fragmentação.

```
No. Time Source Destination Protocol Length Info
 10 2019-10-14 12:31:35.476498 127.0.0.1 127.0.0.1 DTLSv1.0 243 Client Hello
 11 2019-10-14 12:31:35.476652 127.0.0.1 127.0.0.1 DTLSv1.0 80 Hello Verify Request
 12 2019-10-14 12:31:35.476756 127.0.0.1 127.0.0.1 DTLSv1.0 260 Client Hello (Fragment)
 13 2019-10-14 12:31:35.476793 127.0.0.1 127.0.0.1 DTLSv1.0 60 Client Hello (Reassembled)
 14 2019-10-14 12:31:36.476196 127.0.0.1 127.0.0.1 DTLSv1.0 260 Client Hello[Reassembly error,
 15 2019-10-14 12:31:36.476249 127.0.0.1 127.0.0.1 DTLSv1.0 60 Client Hello[Reassembly error,
 16 2019-10-14 12:31:38.476407 127.0.0.1 127.0.0.1 DTLSv1.0 260 Client Hello[Reassembly error,
 17 2019-10-14 12:31:38.476541 127.0.0.1 127.0.0.1 DTLSv1.0 60 Client Hello[Reassembly error,
 19 2019-10-14 12:31:42.476444 127.0.0.1 127.0.0.1 DTLSv1.0 260 Client Hello[Reassembly error,
...
```

Tentando descobrir o motivo encontrei [alguns issues](https://github.com/sipwise/rtpengine/issues/413) no GitHub a respeito de problemas no OpenSSL, e a solução era definir um MTU (Maximum transmission unit) em vez de deixar o OpenSSL usar o default, que é pequeno demais para poder enviar as mensagens do handshake de uma só vez, requisito do protocolo.

```
SSL_CTX_set_options(ctx, SSL_OP_NO_QUERY_MTU); // IMPORTANT: before SSL_new
ssl = SSL_new(ctx);

SSL_set_mtu(ssl, 1500);
BIO_ctrl(bio, BIO_CTRL_DGRAM_SET_MTU, 1500, NULL);
```

Isso corrigiu o envio do ClientHello, mas após isso o handshake entrou em loop no envio do resto das mensagens até retornar com erro.

```
No. Time Source Destination Protocol Length Info

 11780 2019-10-14 16:49:21.672786 127.0.0.1 127.0.0.1 DTLSv1.2 118 Server Hello
 11781 2019-10-14 16:49:21.672890 127.0.0.1 127.0.0.1 DTLSv1.2 1016 Certificate
 11782 2019-10-14 16:49:21.672941 127.0.0.1 127.0.0.1 DTLSv1.2 353 Server Key Exchange
 11783 2019-10-14 16:49:21.672985 127.0.0.1 127.0.0.1 DTLSv1.2 1016 Certificate
 11784 2019-10-14 16:49:21.673035 127.0.0.1 127.0.0.1 DTLSv1.2 111 Certificate Request
 11785 2019-10-14 16:49:21.673053 127.0.0.1 127.0.0.1 DTLSv1.2 57 Server Hello Done
 11786 2019-10-14 16:49:21.673114 127.0.0.1 127.0.0.1 DTLSv1.2 90 Client Key Exchange
 11787 2019-10-14 16:49:21.673161 127.0.0.1 127.0.0.1 DTLSv1.2 317 Certificate Verify
 11788 2019-10-14 16:49:21.673207 127.0.0.1 127.0.0.1 DTLSv1.2 46 Change Cipher Spec
 11789 2019-10-14 16:49:21.673263 127.0.0.1 127.0.0.1 DTLSv1.2 93 Encrypted Handshake Message
 11804 2019-10-14 16:50:21.673036 127.0.0.1 127.0.0.1 DTLSv1.2 1016 Certificate

 11805 2019-10-14 16:50:21.673121 127.0.0.1 127.0.0.1 DTLSv1.2 118 Server Hello
 11806 2019-10-14 16:50:21.673158 127.0.0.1 127.0.0.1 DTLSv1.2 1016 Certificate
 11807 2019-10-14 16:50:21.673199 127.0.0.1 127.0.0.1 DTLSv1.2 353 Server Key Exchange
 11808 2019-10-14 16:50:21.673229 127.0.0.1 127.0.0.1 DTLSv1.2 111 Certificate Request
 so on...
```

Faltam as mensagens Finished após ChangeCipherSpec, o que terminaria o fluxo, mas por algum motivo o Finished nunca chega em nenhum dos lados, e as mensagens a partir de ServerHello se repetem até o retorno de erro de conexão (`SSL_ERROR_SSL`). O Sequence Number do server e client indicam que apesar da troca de mensagens estar ocorrendo existe um loop.


Ao pesquisar a respeito encontrei [um artigo](https://chris-wood.github.io/2016/05/06/OpenSSL-DTLS.html) de Christopher A. Wood que também está explorando esse protocolo usando OpenSSL. Antes disso também havia encontrado um [gist](https://gist.github.com/Jxck/b211a12423622fe304d2370b1f1d30d5) que acompanha passo a passo o setup necessário da biblioteca.

```
openssl req -x509 -newkey rsa:2048 -days 3650 -nodes -keyout client-key.pem -out client-cert.pem
openssl req -x509 -newkey rsa:2048 -days 3650 -nodes -keyout server-key.pem -out server-cert.pem
```
TK
