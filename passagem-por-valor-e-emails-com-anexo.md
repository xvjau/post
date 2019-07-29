---
date: "2010-01-18"
title: Passagem por valor e emails com anexo
categories: [ "code" ]
---
Mais uma [analogia vencedora para ponteiros](http://www.caloni.com.br/basico-do-basico-ponteiros), chamadas por valor e chamadas por referência: e-mails.

Quando passamos um parâmetro por valor, estamos enviando um e-mail com um arquivo em anexo. Não importa o que o destinatário faça com o arquivo: nós não vamos saber o que foi mudado se ele não enviar uma outra cópia.

![email-para-funcao.png](http://i.imgur.com/VEPdoKz.png)

Por outro lado, ao passar um parâmetro por referência, estamos enviando um e-mail com um **endereço** de onde está o arquivo. Se o usuário alterar o arquivo diretamente do endereço que enviamos será possível ver essa alteração imediatamente, pois ambos estão olhando para o mesmo valor na memória.

![email-para-funcao2.png](http://i.imgur.com/SC3qEcw.png)

A analogia pode ser levada mais longe, com ponteiros de ponteiros: enviamos um e-mail com o endereço de um arquivo; dentro desse arquivo existe um endereço para outro arquivo. Dessa forma é possível tanto alterar o arquivo final quanto o endereço de onde ele está; ou ainda "apontar" para outro arquivo, trocando o endereço de dentro do primeiro arquivo.

![email-para-funcao3.png](http://i.imgur.com/QlvyclI.png)

Assim é fácil de visualizar que os dados estão sempre em um arquivo que ocupa espaço na memória (do disco ou da RAM), mas endereços também podem ocupar espaço, se estiverem salvos em um arquivo.

![novo-arquivo.png](http://i.imgur.com/h2w2gjt.png)

Dessa forma, um e-mail que contenha um arquivo em anexo vai ser muito maior que um e-mail apenas com o endereço do arquivo, mas é porque todo o conteúdo do arquivo está dentro do e-mail no primeiro caso. No segundo caso, o endereço ocupa apenas alguns caracteres que identificam a localização do arquivo.
