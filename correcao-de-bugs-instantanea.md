---
date: "2010-02-01"
title: Correção de bugs instantânea
tags: [ "blog" ]
---
Um programador tarimbado sabe que a melhor situação da vida dele para corrigir um bug é quando esse bug acontece em sua máquina de desenvolvimento, na versão Debug e ainda passo-a-passo. Como nessa situação a correção é um verdadeiro "passeio no parque" (ou na mesa do café) ela tende a quase nunca acontecer. Isso é [Murphy](http://www.caloni.com.br/blog/wp-admin/Corre%C3%A7%C3%A3o%20de%20bugs%20instant%C3%A2nea.txt) Aplicado.

Para quem programa para sistemas, então, só o fato de acontecer no mesmo processo toda vez que ele for executado já é o máximo (quem já programou serviços, plugins, GINAs e afins sabe do que eu estou falando).

Porém, saber que uma determinada situação é mel na chupeta (by [Thiago](http://codebehind.wordpress.com)) por si só não adianta de muita coisa. É preciso conhecer as verdadeiras técnicas ninjas que conseguem resolver um bug escabroso num instante, coisa de deixar seu gerente de projetos tão feliz ao ponto dele não botar nenhum defeito na solução.

Dentre as mais conhecidas entre os [malloqueiros](http://www.cplusplus.com/reference/clibrary/cstdlib/malloc/), temos:

	
  * Comenta-descomenta-comenta

	
  * Faz do zero

Essas duas técnicas são tão úteis e tão fáceis de usar que merecem um artigo a respeito.

#### Tira-põe-deixa-ficar

Essa técnica milenar corresponde em tirar pedaços do código-fonte que poderiam estar causando o problema até que seja possível criar uma versão em que o problema não ocorra mais. Quando chega-se nesse nível, então volta-se a descomentar o código retirado até que o problema ocorra novamente. O processo é um fluxo de tira-código com volta-código, sendo que é necessário o bom conhecimento do projeto para não gerar outros problemas com a mutilação temporária do projeto.

#### Projeto-esqueleto ([Re](http://pt.wikipedia.org/wiki/He-Man)-[Main](http://en.wikipedia.org/wiki/Main_function_%28programming%29)!)

Se o código começa a ser tão mutilado que chegamos quase em uma versão vazia (sem código), então talvez a melhor forma de atacar o problema seja criar um esqueleto que contenha apenas o código necessário para que ele não faça nada. Isso mesmo. Não fazendo nada, mas instalado. Com isso prova-se que é possível estar lá sem fazer cagadas. A partir daí vai colocando-se o código do projeto real aos poucos no projeto-esqueleto, até que ele apresente o problema. Ou não. Já vi casos em que todo o código foi migrado e o problema sumiu. _Ce la vie_.
