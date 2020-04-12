---
date: "2010-10-22"
title: Atualizando HouaissParaBabylon no saite
tags: [ "blog", "houaiss" ]
---
O [último comentário no meu último artigo](/houaissparababylon-versao-beta) sobre o conversor Houaiss para Babylon me fez lembrar de algo muito importante: eu não atualizei o branch do saite com a última versão. Deve ser por isso que as pessoas estão tendo problemas com o uso do código. Resolvo isso já:

Essa é a versão 1.2 descrita no meu [último artigo sobre o projeto](/houaiss-para-babylon-12).

De qualquer forma, qual não foi minha surpresa quando tentei recompilar o projeto e ocorreram erros no atlcom. Depois de uma [breve pesquisa](http://social.msdn.microsoft.com/Forums/en-US/vclanguage/thread/ad698507-d62c-4e7b-bb7a-12a03b939594) descobri que precisava rodar alguns "patches" para o include funcionar direito. Então, provavelmente, Willians, era esse o problema. Tente de novo.

